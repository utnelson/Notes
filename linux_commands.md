# Usefull Commands

### Processes


- show processes
```console
root@machine:~$ ps aux
```

- show start command by PID
```console
root@machine:~$ ps -e {PID} -o args
```

- show start command with grep
```console
root@machine:~$ ps aux | grep mongo
```
---

- cat all with keyword "test" and 20 lines bevor and after
```console
cat * | grep -A 20 -B 20 "test"
```