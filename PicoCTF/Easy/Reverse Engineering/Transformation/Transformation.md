# Transformation

## Challenge Information
```
Difficulty: Easy
Category: Reverse Engineering
Description: I wonder what this really is... enc

''.join([chr((ord(flag[i]) << 8) + ord(flag[i + 1])) for i in range(0, len(flag), 2)])
```

# Solution
Look at the content of the `enc` file

```bash
┌──(dxrie㉿Dxrie)-[~/challenge-file/picoctf/reverse-engineering/transformation]
└─$ cat enc
灩捯䍔䙻ㄶ形楴獟楮獴㌴摟潦弸強㕤㐸㤸扽
```

Make a python script to decode those sequence of texts
```python
decoded = open("enc", "r").read()

print(decoded.encode('utf-16-be'))
```

Run the script
```python
┌──(dxrie㉿Dxrie)-[~/challenge-file/picoctf/reverse-engineering/transformation]
└─$ python solve.py
b'picoCTF{16_bits_inst34d_of_8_75d4898b}'
```

`Flag: picoCTF{16_bits_inst34d_of_8_75d4898b}`