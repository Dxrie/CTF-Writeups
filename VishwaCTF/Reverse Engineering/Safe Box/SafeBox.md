# Safe Box

## Challenge Description

```
Difficulty: Hard
Category: Reverse Engineering
Description: There are many ways, but the choice is yours.
```

# Solution

We were given a zip file with a game inside built with unity. When we first run the game, we can see that it's a simple game with a safe. Our objective is to somehow crack the safe and obtain the flag.

So we can conclude that what we need to do is somehow modify, the game's code to either leak the passcode or tamper with the game's logic when checking our passcode.

I used dnSpy to look at the code of the `Assembly-CSharp.dll` file inside the folder `Safe_Data/Managed`.

Once I loaded the `Assembly-CSharp.dll` into dnSpy, I saw a lot of classes but there's one function that immediately caught my eye. The function `EnterCheck` seems promising so I tried to read the code. However it seems like the whole dll file is obfuscated and we can't edit the dll code without error.

I then took a step back and used de4dot to deobfuscate the dll file. After that I got a new dll file `Assembly-CSharp-cleaned.dll` and I replaced the old `Assembly-CSharp.dll` file with the new deobfuscated dll file. I reloaded the `Assembly-CSharp.dll` file into my dnSpy.

I then reread the `EnterCheck` class to try and comprehend what's going on.

```cs
private void method_0()
{
	if (!this.method_7())
	{
		Debug.LogError("LEGAL WARNING: Any attempt to reverse engineer, decompile, or modify this software is a direct violation of SafeBox Security Solutions' proprietary agreement.");
		Application.Quit();
	}
	new R();
	S.T();
	this.method_5();
	this.d.text = "ENTER PASSWORD: ";
	this.string_0 = this.method_8(Application.dataPath, "../Safe_Data");
	this.string_1 = this.method_8(this.string_0, "reset.txt");
	this.method_3();
	this.int_4 = this.method_13("counter", 0);
	this.int_0 = this.method_16(this.h[0]);
	this.int_1 = this.method_16(this.h[1]);
	this.int_2 = this.method_16(this.h[2]);
	this.int_3 = this.method_16(this.h[3]);
}
```

It seems like this `method_0` function calls another function `method_7`. It seems like this `method_7` function checks the validity of this dll file and prevent us from tampering with the code. I then deleted the if statement from the code and continued reading the code.

After reading for several minutes, I came across a logic that limits the user from trying more than 3 times.

```cs
if (this.int_4 >= 3)
{
	this.d.text = "Too Many Wrong Passwords";
	return;
}
```

After reading the code even more I found a function that retrieves `PlayerPrefs`.

```cs
private int method_13(string string_2, int int_5)
{
    return PlayerPrefs.GetInt(string_2, int_5);
}
```

This function was also called on method_0, where the program checks the validity of this code. This function was called to retrieve "counter" from PlayerPrefs which I assume is to limit players from trying to crack the code more than 3 times. I modified the method_13 to return `0` instead of `PlayerPrefs.GetInt(string_2, int_5);`. I also modified a line of code on the function K0 where it increments the variable `int_4`. I modified it into:

```cs
this.int_4 = 0;
```

I saved this dll file and replaced the old one and ran the game again and tried to enter random passcodes however after 3 attempts, the game still forbids me from trying again.

It seems like tampering with the `Assembly-CSharp.dll` file doesn't affect the game at all. I confirmed this by deleting the file and I ran the game again. And my suspicion was right, it seems like the game wasn't relying on the `Assembly-CSharp.dll` file. I then reset the counter by deleting the reset.txt file inside the `Safe_Data` folder. And I looked for all dll files inside `Safe_Data/Managed` folder. And I found that `UnityEngine.NetworkUtils.dll` contains a class with the same name `EnterCheck`. The content of the class was also kind of similar so I tried modifying the class.

There was a function called `OnMouseDown` in the class which I assume is called whenever player clicks inside the game. We can see that there's an if statement which I assume is to check if the passcode entered is correct. I modified the code from:

```cs
if (this.text.text == string.Concat(new string[]
{
	"ENTER PASSWORD: ",
	(this.o << 1).ToString(),
	(this.o + this.r).ToString(),
	(this.o | this.u + 1).ToString(),
	((this.t - 2 << this.r - 2) + (this.u + 5)).ToString(),
	(this.t & this.o - 2).ToString()
}))
{
	this.check = 1f;
	return;
}
```

to:
```cs
if (this.text.text != string.Concat(new string[]
{
	"ENTER PASSWORD: ",
	(this.o << 1).ToString(),
	(this.o + this.r).ToString(),
	(this.o | this.u + 1).ToString(),
	((this.t - 2 << this.r - 2) + (this.u + 5)).ToString(),
	(this.t & this.o - 2).ToString()
}))
{
	this.check = 1f;
	return;
}
```

This will make sure the game unlocks the safe even if I entered the wrong passcode.

I reran the game and I clicked on random numbers and the safe opened up and the flag is inside the safe.

Flag: `VishwaCTF{h3r3_y0u_@r3}`