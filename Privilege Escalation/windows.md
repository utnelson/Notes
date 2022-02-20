# Windows

## Scripts

- WinPEAS -> [Link](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)
- PowerUp -> [Link](https://github.com/PowerShellMafia/PowerSploit/tree/master/Privesc)
- Windows Exploit Suggester -> [Link](https://github.com/bitsadmin/wesng)

## User enumeration

```console
Current user privileges:
$ whoami /priv

List users:
$ net users

List details of a user:
$ net user <username>

Other logged in Users:
$ qwinsta

User group on system:
$ net localgroup

List memers of a specific group:
$ net localgroup <groupname>
```

## System information

```console
$ systeminfo
$ systeminfo | findstr /B /C:"OS Name" /C:"OS Version"

$ hostname
```

## Searching files

```console
$ findstr /si password *.txt
findstr: Searches for patterns of text in files.
/si: Searches the current directory and all subdirectories (s), ignores upper case / lower case differences (i)
password: The command will search for the string “password” in files
*.txt: The search will cover files that have a .txt extension
```

## Vulnerable Software

```console
$ wmic qfe get Caption,Description,HotFixID,InstalledOn
$ wmic service get name,displayname,pathname,startmode
$ wmic product get name,version,vendor
$ wmic service list brief | findstr  "Running"
```
Check for Vulns :
- Searchsploit
- Metasploit
- Exploit-DB
- Github
- Google

## Network connections

```console
C:\Users\user\Desktop>netstat -ano
-a: Displays all active connections and listening ports on the target system.
-n: Prevents name resolution. IP Addresses and ports are displayed with numbers instead of attempting to resolves names using DNS.
-o: Displays the process ID using each listed connection.

Active Connections

  Proto  Local Address          Foreign Address        State           PID
  TCP    0.0.0.0:135            0.0.0.0:0              LISTENING       68
  TCP    0.0.0.0:445            0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:3389           0.0.0.0:0              LISTENING       1092
  TCP    0.0.0.0:5985           0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:47001          0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:49664          0.0.0.0:0              LISTENING       660
  TCP    0.0.0.0:49665          0.0.0.0:0              LISTENING       1348
  TCP    0.0.0.0:49666          0.0.0.0:0              LISTENING       1636
  TCP    0.0.0.0:49667          0.0.0.0:0              LISTENING       2288
  TCP    0.0.0.0:49668          0.0.0.0:0              LISTENING       2468
  TCP    0.0.0.0:49671          0.0.0.0:0              LISTENING       780
  TCP    0.0.0.0:49681          0.0.0.0:0              LISTENING       788
```

Any port listed as “LISTENING” that was not discovered with the external port scan can present a potential local service.

## Sheduled tasks

```console
$ schtasks /query /fo LIST /v
```

