# Linux

## Unshadow Passwd & Shadow

You need to download 2 files. /etc/passwd & /etc/shadow

```console
$ unshadow <passwd> <shadow> > hashes.txt
```

Then crack it with john

```shell
$ john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt new.txt 
Using default input encoding: UTF-8
Loaded 3 password hashes with 3 different salts (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Press 'q' or Ctrl-C to abort, almost any other key for status
Password1        (karen)
Password1        (user2)
test123          (gerryconway)
3g 0:00:00:13 DONE (2022-02-19 19:18) 0.2155g/s 1268p/s 1783c/s 1783C/s 111292..ellie123
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```