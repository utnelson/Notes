# GDB

## Fuzzing

```console
$ echo $(python -c 'print("A" * 650)') | ./bof
Enter some string:
Segmentation fault (core dumped)
```

## Using GDB

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
```