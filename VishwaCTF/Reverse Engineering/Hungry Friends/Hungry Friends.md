# Hungry Friends

## Challenge Description

```
Difficulty: Medium
Category: Reverse Engineering
Description: Feed my friend.
```

# Solution

We were given an executable file, `snake.exe`. When I try to run it, it gives me several errors like `The code execution cannot proceed because libgcc_s_dw2-1.dll was not found. Reinstalling the program may fix this problem.`, `The code execution cannot proceed because libstdc++-6.dll was not found. Reinstalling the program may fix this problem.` and `The code execution cannot proceed because libwinpthread-1.dll was not found. Reinstalling the program may fix this problem.`

I then downloaded those dlls from the internet and I put it inside the same directory as the `snake.exe` executable. After that I tried to run the game again and apparently it's just a normal snake game.

Then I loaded the executable into `Ghidra` to disassemble the executable and understand what's going on behind the scene.

```c
int __cdecl _main(int _Argc,char **_Argv,char **_Env)

{
  int iVar1;
  basic_ostream *this;
  basic_ostream<> *this_00;
  time_t tVar2;
  
  ___main();
  tVar2 = _time((time_t *)0x0);
  _srand((uint)tVar2);
  HIDE_CURSOR();
  INIT_GAME();
  SPAWN_FOOD();
  while (iVar1 = _CHAKDE, _GAME_OVER == '\0') {
    iVar1 = __kbhit();
    if (iVar1 != 0) {
      iVar1 = __getch();
      if (iVar1 == 100) {
        _DX = 1;
        _DY = 0;
      }
      else if (iVar1 < 0x65) {
        if (iVar1 == 0x61) {
          _DX = 0xffffffff;
          _DY = 0;
        }
      }
      else if (iVar1 == 0x73) {
        _DX = 0;
        _DY = 1;
      }
      else if (iVar1 == 0x77) {
        _DX = 0;
        _DY = 0xffffffff;
      }
    }
    MOVE_SNAKE();
    DRAW_GAME();
    if (_CHAKDE == 9999) {
      SHOW_FLAG();
    }
    _Sleep@4(100);
  }
  this = std::operator<<((basic_ostream *)&_ZSt4cout,"Game Over! Score: ");
  this_00 = (basic_ostream<> *)std::basic_ostream<>::operator<<((basic_ostream<> *)this,iVar1);
  std::basic_ostream<>::operator<<(this_00,std::endl<>);
  return 0;
}
```

After reading the main function, there's a piece of code that caught my eye. 

```c
if (_CHAKDE == 9999) {
    SHOW_FLAG();
}
```

This statement in particular caught my eye, I assumed this `_CHAKDE` variable is used to store points that the player gained while playing the game. So immediately I thought of modifying the memory by using Cheat Engine.

First I loaded the game into x32dbg and I ran the game and immediately pause it. I then loaded Cheat Engine and attached it to the game and scanned the number 0 as the first scan since the initial value for `_CHAKDE` is most likely 0.

Then I ran the game again to play it and get one point. After getting two points, I paused the game again and I scanned 2 for the next scan in Cheat Engine. After that, there's two addresses with the name snake.exe+something something. I chose those 2 addresses and pressed Ctrl + E to set a new value for those addresses. Then I typed 9999 because the if statement checks if `_CHAKDE` is equals to 9999.

After that I reran the game and immediately paused it and then I got the flag.

Flag: `VishwaCTF{th3r3_4r3_5n4k35_all_4r0und}`