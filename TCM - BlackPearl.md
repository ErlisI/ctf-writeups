# TCM - Black Pearl

June 18, 2023 

---

## Introduction

This machine is created by TCM Security (of which I am currently taking the PEH course), it is supposed to be an easy CTF challenge.
The object is to acquire root access and receive the flag via any means possible (Ethically).

---

## Information Gathering

***Port Scanning***

- Beginning the reconnaissance by running `nmap -T4 -p- -A {IP}`
    
    ```bash
    Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-15 21:16 EDT
    Nmap scan report for blackpearl (192.168.1.181)
    Host is up (0.0011s latency).
    Not shown: 65532 closed tcp ports (reset)
    PORT   STATE SERVICE VERSION
    22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
    | ssh-hostkey: 
    |   2048 66381450ae7dab3972bf419c39251a0f (RSA)
    |   256 a62e7771c6496fd573e9227d8b1ca9c6 (ECDSA)
    |_  256 890b73c153c8e1885ec316ded1e5260d (ED25519)
    53/tcp open  domain  ISC BIND 9.11.5-P4-5.1+deb10u5 (Debian Linux)
    | dns-nsid: 
    |_  bind.version: 9.11.5-P4-5.1+deb10u5-Debian
    80/tcp open  http    nginx 1.14.2
    |_http-title: Welcome to nginx!
    |_http-server-header: nginx/1.14.2
    MAC Address: 00:0C:29:3A:C3:FF (VMware)
    Device type: general purpose
    Running: Linux 4.X|5.X
    OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
    OS details: Linux 4.15 - 5.6
    Network Distance: 1 hop
    Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
    
    TRACEROUTE
    HOP RTT     ADDRESS
    1   1.09 ms blackpearl (192.168.1.181)
    
    OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 26.94 seconds
    ```
    
- So we got port 22, 53(need to do some research on this), and 80…let's see what we can do :)

***Port 22***

- As of now, not much we can do…will hold on to this for later to see if we can find some credentials.

***Port 53***

- Need a `dnsrecon` to get some information on this port, since it's running DNS
    - For which we find something right away
        - <img width="490" alt="Screenshot_2023-06-18_at_7 40 51_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/d3c23b75-0faa-40a0-b2da-00d861b62b89">
        
- Now we add that to our DNS at `/etc/hosts` so we access it
    - <img width="336" alt="Screenshot_2023-06-18_at_7 44 03_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/3de750fa-98f4-41c2-8e87-56f78c51d367">
    
- After opening this, I do another directory busting, and immediately I get something
    - <img width="1675" alt="Screenshot_2023-06-18_at_7 47 18_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/901a9534-ac10-4032-8b44-260df4651f2e">
    
    - After just a little googling, I found out that there is a remote code execution by [Rapid7](https://www.rapid7.com/db/modules/exploit/multi/http/navigate_cms_rce/) (BINGO), which uses `metasploit` to exploit it

 ***Port 80***

- After going here, it was a simple website giving information that the server is running
    - Looking at the page source, looks like they left some information there (which might be useful for later)
        - <img width="572" alt="Screenshot_2023-06-18_at_7 34 18_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/1e5eaa8d-c153-40a8-bdfb-37fdd280dbfe">        

---

## Exploitation and Root access

- After using going to `blackpearl.tcm/navigate` I ran that Rapid7 and immediately got a shell with `metasploit` but we need to do some privilege escalating to get root access
    - <img width="863" alt="Screenshot_2023-06-18_at_7 56 13_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/169c7c4c-a086-49fd-a073-5f6c8209bd0a">
    
- After reading [this](https://sushant747.gitbooks.io/total-oscp-guide/content/spawning_shells.html) (it was clear that I needed a `tty shell`) to interact with the system
- Now to get some information on the real privilege escalation, I set up my python server to host `linpeas`.
    - After running this, found something very interesting, and what this means is that we can run it as the person that owns the machine (root), which we can get malicious with
        - <img width="146" alt="Screenshot_2023-06-18_at_8 16 50_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/4418752c-87bc-4c38-8991-716058a5ae01">
        
- Now after finding all the files that run on `SUID` we go to [GTFOBins](https://gtfobins.github.io/#+suid) and find something to abuse this vulnerability.
    - <img width="827" alt="Screenshot_2023-06-18_at_8 21 10_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/4c9ccc44-e587-4b6a-abcc-9df53e103dbb">
    
- After running the command  `/usr/bin/php7.3 -r "pcntl_exec('/bin/sh', ['-p']);"` we get root access and capture the flag
  - <img width="329" alt="Screenshot_2023-06-18_at_8 24 11_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/c7f790bd-9e2c-4989-b833-21f8aefb8df4">

***Also check it out [here](https://e-ctf-writeups.notion.site/TCM-Black-Pearl-1e53f570c12a4f98abd6c11678da4c0a?pvs=4)***
