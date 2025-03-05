# interencdec

## Challenge Information
```
Difficulty: Easy
Category: Cryptography
Description: Can you get the real meaning from this file.
```

# Solution
Display the content of the `enc_flag` file using `cat` and decode it using `base64 -d`
```bash
┌──(dxrie㉿Dxrie)-[~/challenge-file/picoctf/cryptography/interencdec]
└─$ cat enc_flag | base64 -d
b'd3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrX2xoNjBsMDBpfQ=='
```

We got another base64 format and let's decode it using `base64 -d` again

```bash
┌──(dxrie㉿Dxrie)-[~/challenge-file/picoctf/cryptography/interencdec]
└─$ echo d3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrX2xoNjBsMDBpfQ== | base64 -d
wpjvJAM{jhlzhy_k3jy9wa3k_lh60l00i}
```

Now it looks like a ROT13 or a Caesar cipher. Let's try to decode it with caesar.
<br>
Install bsdgames to use the caesar command

```bash
┌──(dxrie㉿Dxrie)-[~/challenge-file/picoctf/cryptography/interencdec]
└─$ sudo apt install bsdgames
```
Then we can finally use the command
```bash
┌──(dxrie㉿Dxrie)-[~/challenge-file/picoctf/cryptography/interencdec]
└─$ echo d3BqdkpBTXtqaGx6aHlfazNqeTl3YTNrX2xoNjBsMDBpfQ== | base64 -d | caesar
picoCTF{caesar_d3cr9pt3d_ea60e00b}
```