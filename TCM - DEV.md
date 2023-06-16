# TCM - DEV

June 4, 2023 

---

## Introduction

This machine is created by TCM Security (of which I am currently taking the PEH course), it is supposed to be an easy CTF challenge.
The object is to acquire root access and receive the flag via any means possible (Ethically).

---

## Information Gathering

***Port Scanning***

- Beginning the reconnaissance by running `nmap -T4 -p- -A {IP}`
    
    ```bash
    Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-04 18:34 EDT
    Nmap scan report for dev (192.168.1.173)
    Host is up (0.00096s latency).
    Not shown: 65526 closed tcp ports (reset)
    PORT      STATE SERVICE  VERSION
    22/tcp    open  ssh      OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
    | ssh-hostkey: 
    |   2048 bd96ec082fb1ea06cafc468a7e8ae355 (RSA)
    |   256 56323b9f482de07e1bdf20f80360565e (ECDSA)
    |_  256 95dd20ee6f01b6e1432e3cf438035b36 (ED25519)
    80/tcp    open  http     Apache httpd 2.4.38 ((Debian))
    |_http-server-header: Apache/2.4.38 (Debian)
    |_http-title: Bolt - Installation error
    111/tcp   open  rpcbind  2-4 (RPC #100000)
    | rpcinfo: 
    |   program version    port/proto  service
    |   100000  2,3,4        111/tcp   rpcbind
    |   100000  2,3,4        111/udp   rpcbind
    |   100000  3,4          111/tcp6  rpcbind
    |   100000  3,4          111/udp6  rpcbind
    |   100003  3           2049/udp   nfs
    |   100003  3           2049/udp6  nfs
    |   100003  3,4         2049/tcp   nfs
    |   100003  3,4         2049/tcp6  nfs
    |   100005  1,2,3      41379/tcp6  mountd
    |   100005  1,2,3      50106/udp6  mountd
    |   100005  1,2,3      56831/udp   mountd
    |   100005  1,2,3      57297/tcp   mountd
    |   100021  1,3,4      35571/udp   nlockmgr
    |   100021  1,3,4      43327/tcp   nlockmgr
    |   100021  1,3,4      46687/tcp6  nlockmgr
    |   100021  1,3,4      56904/udp6  nlockmgr
    |   100227  3           2049/tcp   nfs_acl
    |   100227  3           2049/tcp6  nfs_acl
    |   100227  3           2049/udp   nfs_acl
    |_  100227  3           2049/udp6  nfs_acl
    2049/tcp  open  nfs_acl  3 (RPC #100227)
    8080/tcp  open  http     Apache httpd 2.4.38 ((Debian))
    |_http-server-header: Apache/2.4.38 (Debian)
    | http-open-proxy: Potentially OPEN proxy.
    |_Methods supported:CONNECTION
    |_http-title: PHP 7.3.27-1~deb10u1 - phpinfo()
    37603/tcp open  mountd   1-3 (RPC #100005)
    43327/tcp open  nlockmgr 1-4 (RPC #100021)
    45197/tcp open  mountd   1-3 (RPC #100005)
    57297/tcp open  mountd   1-3 (RPC #100005)
    MAC Address: 00:50:56:33:7B:EE (VMware)
    Device type: general purpose
    Running: Linux 4.X|5.X
    OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
    OS details: Linux 4.15 - 5.6
    Network Distance: 1 hop
    Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
    
    TRACEROUTE
    HOP RTT     ADDRESS
    1   0.96 ms dev (192.168.1.173)
    
    OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 20.79 seconds
    ```
    
- A few ports are open, but the important ones would be ports 22, 80, 111, 2049, and 8080

***Port 22***

- For this, we have OpenSSH 7.9p1, which we can't really do anything (for now) unless we have some credentials to connect to

***Port 80***

- When opening the website, there wasn't much to look at, just some information about the server it's running and some links to the server's page.
- What we could do is run `dirbuster` to see if there are subpages that have leaked information. Other than this there's nothing else we can do here.
- After running `dirbuster` we get some information (and to be honest I did look at some spoilers in here because there were just so many subpages). The subpage leaked a credential and a password, but nowhere to sign in with it
    
    - <img width="628" alt="Screenshot_2023-06-05_at_7 34 39_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/406d6096-137a-40ac-a854-f23ab1608259">


***Port 111***

- I have never seen this port before and I did some research on it for which I found this [website](https://book.hacktricks.xyz/network-services-pentesting/pentesting-rpcbind). But nothing worked out so I moved on.
    
    *Ps.: Definitely need to do some more research to understand how to exploit this port later on*
    

***Port* 2049**

- `nfs_acl open` for which I immediately found something online to connect to, and also found a file inside the mount
    - <img width="409" alt="Screenshot_2023-06-05_at_7 40 19_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/91be902e-9b72-4bc3-a613-77a043b8f59b">
    - <img width="578" alt="Screenshot_2023-06-05_at_7 47 52_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/e6921bdf-1e99-4922-8d08-254eb407a17e">

- After finding the zip file I tried to brute force it (which was successful) and unzipped it. The zip found leaked some information about what the user liked and the user's initials (for which I tried ssh but nothing worked), also worth mentioning that the password found in the subpages of port 80 could have been a very good match.
    - <img width="577" alt="Screenshot_2023-06-05_at_7 52 07_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/d57c5a1c-51aa-443b-89c5-1cef3896a143">


***Port 8080***

- This site had information about something called ********Bolt********
- It lead to their main page, and no other information was gathered from here.
- Same as for port 80, I ran `dirbuster` to gather more information about their subpages

- After running `dirbuster`, there were subpages that lead to a massive lead for me to exploit this machine because everyone was able to register, and this [exploit](https://www.exploit-db.com/exploits/48411) required just that :)
    - <img width="962" alt="Screenshot_2023-06-05_at_7 34 52_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/b5310c52-fa69-4ef9-9255-3665f2438821">

        

<img width="583" alt="Screenshot_2023-06-05_at_7 37 43_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/cb8641c4-3822-481e-bf27-e3863aa1a858">
<img width="688" alt="Screenshot_2023-06-05_at_7 37 57_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/1bcc6e6a-cd8e-4cbe-99be-1e4690cd4212">


- There we go, that's the user (for which the initials were found in the zip file)

*Now we got everything we need, just need to exploit the machine.*

---

## Exploitation and Root access

***Port 22***

- After finding the user I tried the password found in the subpages of port 80 (which worked) and I connected to the ssh with those credentials
    
    - <img width="879" alt="Screenshot_2023-06-05_at_7 56 19_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/f934913e-4c9e-485f-a881-2e3681a86345">

- Now after seeing this, I got the information that we have `sudo` privilege for `zip` files so I googled this up and found for which I found this amazing website [GTFOBins](https://gtfobins.github.io)
    - <img width="898" alt="Screenshot_2023-06-05_at_7 57 23_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/9988df82-67b4-450a-b53b-1ffdb2a60d96">
    
- After running this, the machine was rooted and flag was captured :)
    - <img width="270" alt="Screenshot_2023-06-05_at_7 58 07_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/ba771555-bcd9-4e0e-a828-f588d8084ef9">
    - <img width="175" alt="Screenshot_2023-06-05_at_7 58 20_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/0be9e8ba-8b33-467f-bee4-52fad7aeffb6">    

***Also check it out [here](https://e-ctf-writeups.notion.site/TCM-DEV-562a2ab9869b46e28aa0f59cd65c177a?pvs=4)***
