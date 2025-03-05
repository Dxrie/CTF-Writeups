# Verify

## Challenge Information
```
Difficulty: Easy
Category: Forensics
Description: People keep trying to trick my players with imitation flags. I want to make sure they get the real thing! I'm going to provide the SHA-256 hash and a decrypt script to help you know that my flags are legitimate.
```

# Solution
Connect to the server using `ssh`.
```bash
┌──(dxrie㉿Dxrie)-[~/challenge-file/picoctf/forensics]
└─$ ssh -p 56464 ctf-player@rhea.picoctf.net
ctf-player@rhea.picoctf.net's password:
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 6.8.0-1021-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.
Last login: Sat Feb 15 13:24:03 2025 from 127.0.0.1
ctf-player@pico-chall$
```

Use `ls` to check the files on that directory
```bash
ctf-player@pico-chall$ ls
checksum.txt  decrypt.sh  files
```

Let's look at the `decrypt.sh` shell script.
```shell

        #!/bin/bash

        # Check if the user provided a file name as an argument
        if [ $# -eq 0 ]; then
            echo "Expected usage: decrypt.sh <filename>"
            exit 1
        fi

        # Store the provided filename in a variable
        file_name="$1"

        # Check if the provided argument is a file and not a folder
        if [ ! -f "/home/ctf-player/drop-in/$file_name" ]; then
            echo "Error: '$file_name' is not a valid file. Look inside the 'files' folder with 'ls -R'!"
            exit 1
        fi

        # If there's an error reading the file, print an error message
        if ! openssl enc -d -aes-256-cbc -pbkdf2 -iter 100000 -salt -in "/home/ctf-player/drop-in/$file_name" -k picoCTF; then
            echo "Error: Failed to decrypt '$file_name'. This flag is fake! Keep looking!"
        fi
```

So all this shell script does is it decrypts a file. We can use this script to decrypt the file. But first we need to find the correct file to decrypt.

We can do this by checking the checksum of all the files and using `grep` to capture the checksum that matches the content of the `checksum.txt`.

Let's use `cat` to look at the content of `checksum.txt`.

```bash
ctf-player@pico-chall$ cat checksum.txt
5848768e56185707f76c1d74f34f4e03fb0573ecc1ca7b11238007226654bcda
```

And then we can copy the content and use it to grep the checksum. We can use `sha256sum` to get the checksum of a file.

```bash
ctf-player@pico-chall$ sha256sum files/* | grep 5848768e56185707f76c1d74f34f4e03fb0573ecc1ca7b11238007226654bcda
5848768e56185707f76c1d74f34f4e03fb0573ecc1ca7b11238007226654bcda  files/8eee7195
```

Now we can use the `decrypt.sh` script decrypt the file.

```bash
ctf-player@pico-chall$ ./decrypt.sh files/8eee7195
picoCTF{trust_but_verify_8eee7195}
```

`Flag: picoCTF{trust_but_verify_8eee7195}`