# The Numbers

## Challenge Information
```
Difficulty: Easy
Category: Cryptography
Description: The numbers... what do they mean?
```

# Solution
Download the file using `wget`
```bash
┌──(dxrie㉿Dxrie)-[~/challenge-file/picoctf/cryptography/the-numbers]
└─$ wget https://jupiter.challenges.picoctf.org/static/f209a32253affb6f547a585649ba4fda/the_numbers.png
--2025-02-15 17:31:14--  https://jupiter.challenges.picoctf.org/static/f209a32253affb6f547a585649ba4fda/the_numbers.png
Resolving jupiter.challenges.picoctf.org (jupiter.challenges.picoctf.org)... 3.131.60.8
Connecting to jupiter.challenges.picoctf.org (jupiter.challenges.picoctf.org)|3.131.60.8|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 100721 (98K) [application/octet-stream]
Saving to: ‘the_numbers.png’

the_numbers.png               100%[=================================================>]  98.36K   202KB/s    in 0.5s

2025-02-15 17:31:16 (202 KB/s) - ‘the_numbers.png’ saved [100721/100721]
```
Open the image using 
```bash
eog the_numbers.png
```
Write down the numbers and make python scripts to interpret those numbers as alphabets.


```python
import string

alphabet=list(string.ascii_lowercase)
numbers="16 9 3 15 3 20 6 20 8 5 14 21 13 2 5 18 19 13 1 19 15 14"

final = ""

for i in numbers.split(" "):
        final += alphabet[int(i)-1]

print(final)
```

Run the python file
```bash
┌──(dxrie㉿Dxrie)-[~/script]
└─$ python script.py
picoctfthenumbersmason
```

The flag that we got is
```
picoctf{thenumbersmason}
```