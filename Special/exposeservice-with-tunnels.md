# SSH tunnels

![image](/images/sshtunnel.png)

Reverse SSH port forwarding specifies that the given port on the remote server host is to be forwarded to the given host and port on the local side.
-L is a local tunnel (YOU <-- CLIENT). If a site was blocked, you can forward the traffic to a server you own and view it. For example, if imgur was blocked at work, you can do ssh -L 9000:imgur.com:80 user@example.com. Going to localhost:9000 on your machine, will load imgur traffic using your other server.
-R is a remote tunnel (YOU --> CLIENT). You forward your traffic to the other server for others to view. Similar to the example above, but in reverse.

investigate sockets running on a host:
```console
$ ss -tulpn

-t	Display TCP sockets
-u	Display UDP sockets
-l	Displays only listening sockets
-p	Shows the process using the socket
-n	Doesn't resolve service names

Netid  State      Recv-Q Send-Q Local Address:Port               Peer Address:Port              
udp    UNCONN     0      0                *:10000                        *:*                  
udp    UNCONN     0      0                *:68                           *:*                  
tcp    LISTEN     0      80       127.0.0.1:3306                         *:*                  
tcp    LISTEN     0      128              *:10000                        *:*                  
tcp    LISTEN     0      128              *:22                           *:*                  
tcp    LISTEN     0      128             :::80                          :::*                  
tcp    LISTEN     0      128             :::22                          :::*
```

Run from local machine:

```console
$ ssh -L 10000:localhost:10000 <username>@<ip>
```