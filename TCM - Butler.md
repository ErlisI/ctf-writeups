# TCM - Butler

June 6, 2023 

---

## Introduction

This machine is created by TCM Security (of which I am currently taking the PEH course), it is supposed to be an easy CTF challenge.
The object is to acquire root access and receive the flag via any means possible (Ethically).

---

## Information Gathering

***Port Scanning***

- Beginning the reconnaissance by running `nmap -T4 -p- -A {IP}`
    
    ```bash
    Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-06 19:48 EDT
    Nmap scan report for Butler (192.168.1.178)
    Host is up (0.035s latency).
    Not shown: 65523 closed tcp ports (reset)
    PORT      STATE SERVICE       VERSION
    135/tcp   open  msrpc         Microsoft Windows RPC
    139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
    445/tcp   open  microsoft-ds?
    5040/tcp  open  unknown
    7680/tcp  open  pando-pub?
    8080/tcp  open  http          Jetty 9.4.41.v20210516
    |_http-title: Site doesn't have a title (text/html;charset=utf-8).
    |_http-server-header: Jetty(9.4.41.v20210516)
    | http-robots.txt: 1 disallowed entry 
    |_/
    49664/tcp open  msrpc         Microsoft Windows RPC
    49665/tcp open  msrpc         Microsoft Windows RPC
    49666/tcp open  msrpc         Microsoft Windows RPC
    49667/tcp open  msrpc         Microsoft Windows RPC
    49668/tcp open  msrpc         Microsoft Windows RPC
    49669/tcp open  msrpc         Microsoft Windows RPC
    MAC Address: 00:0C:29:AF:13:D3 (VMware)
    No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
    TCP/IP fingerprint:
    OS:SCAN(V=7.93%E=4%D=6/6%OT=135%CT=1%CU=38823%PV=Y%DS=1%DC=D%G=Y%M=000C29%T
    OS:M=647FC6A1%P=x86_64-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=10A%TI=I%TS=U)SEQ(
    OS:SP=101%GCD=1%ISR=10A%TI=I%CI=I%II=I%SS=S%TS=U)SEQ(SP=101%GCD=1%ISR=10A%T
    OS:I=I%CI=I%TS=U)OPS(O1=M5B4NW8NNS%O2=M5B4NW8NNS%O3=M5B4NW8%O4=M5B4NW8NNS%O
    OS:5=M5B4NW8NNS%O6=M5B4NNS)WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=F
    OS:F70)ECN(R=N)ECN(R=Y%DF=Y%T=80%W=FFFF%O=M5B4NW8NNS%CC=N%Q=)T1(R=Y%DF=Y%TG
    OS:=80%S=O%A=S+%F=AS%RD=0%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)
    OS:T2(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T3(R=N)T3(R=Y%DF=Y%T=80%W=0
    OS:%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=N)T4(R=Y%DF=Y%T=80%W=0%S=O%A=O%F=R%O=%RD=0
    OS:%Q=)T5(R=N)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T6(R=N)T6(R=Y%DF
    OS:=Y%T=80%W=0%S=O%A=O%F=R%O=%RD=0%Q=)T7(R=N)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F
    OS:=AR%O=%RD=0%Q=)U1(R=N)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G
    OS:%RUCK=G%RUD=G)IE(R=N)IE(R=Y%DFI=N%T=80%CD=Z)
    
    Network Distance: 1 hop
    Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
    
    Host script results:
    | smb2-security-mode: 
    |   311: 
    |_    Message signing enabled but not required
    |_nbstat: NetBIOS name: BUTLER, NetBIOS user: <unknown>, NetBIOS MAC: 000c29af13d3 (VMware)
    | smb2-time: 
    |   date: 2023-06-07T02:51:50
    |_  start_date: N/A
    |_clock-skew: 2h59m59s
    
    TRACEROUTE
    HOP RTT      ADDRESS
    1   34.58 ms Butler (192.168.1.178)
    
    OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 225.40 seconds
    ```
    
- A few ports are open, but the important ones would be ports 135, 139/445, and 8080. The others are not very important or interesting

***Port 135***

- I didn't know much about this port, read something here [https://book.hacktricks.xyz/network-services-pentesting/135-pentesting-msrpc](https://book.hacktricks.xyz/network-services-pentesting/135-pentesting-msrpc), but nothing very interesting or something I could have used to exploit the port.
- But I did learn something about this port, so that's why Im putting it here lol

***Port 139/445***

- As for this port, I had no credentials and nothing I could use to connect to it, basically no leads at all.
- Maybe might be useful later!

***Port 8080***

- When I opened this on the web, I saw a `[Jenkins](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&cad=rja&uact=8&ved=2ahUKEwiToIaphLz_AhU9M1kFHZtqB8MQFnoECA4QAQ&url=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FJenkins_(software)&usg=AOvVaw1obqNYdlbM-aqvHp5GmmyY)` login for which I also had no credentials.
- I started with a simple search for default credentials (which didn't get me in), so I used `Burpsuite` to kind of brute-force it with some default credentials after sending it to `intruder`. This surprisingly worked and I was in.
    - <img width="1263" alt="Screenshot_2023-06-08_at_2 34 19_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/4b2ddfb1-3f3f-49be-a931-28b153478927">
    - <img width="1680" alt="Screenshot_2023-06-08_at_2 37 49_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/720d39d1-06c6-4218-93e2-30acbc951b1f">
    
- From here on, I just did some basic googling for which I found that this has a major vulnerability if you are logged in to Jenkins. I used [this](https://gist.github.com/frohoff/fed1ffaab9b9beeb1c76) and set a listener to get a shell.
    - <img width="1331" alt="Screenshot_2023-06-08_at_2 55 42_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/6911cb75-c95c-4525-93d3-2e43866240b1">
    
    ---
    

## Exploitation and Root access

***Port 8080***

- After running the code shown above from the screenshot, I was able to get a shell, but this user didn't have root access (or authority system in Windows)
    - <img width="1063" alt="Screenshot_2023-06-08_at_2 40 42_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/135b6852-3475-45a9-8e5b-aacf9eca9545">
    
- After I was in, I needed some privilege escalation to get root access, for which I opened my port 80 to host and download to the targeted machine https://github.com/carlospolop/PEASS-ng (more precisely `winpeas`) to get information about how to privilege escalate the machine.
- After this, I saw that a program was running directly from the root access, meaning that it was time for some `msfvenom` to get root access.
After setting everything up and downloading the payload that I just made to the machine that we are attacking, I stopped the program that ran as root and started our little payload shell code.
    - <img width="1167" alt="Screenshot_2023-06-08_at_2 59 29_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/d97d2230-a8fe-451b-827a-8cea300edfbe">
    
- After running the malicious code I was in :)
    <img width="404" alt="Screenshot_2023-06-08_at_3 00 03_PM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/26b1eda1-ea5f-4cfe-be56-8ff37e0cfc65">
    

*P.S.: This took more time than anticipated, and honestly if the Jenkins credentials wouldn't work, I might not have been able to solve this.* 

**Note for the future learn more about the new ports that I saw and try to find ways to exploit them**


***Also check it out [here](https://e-ctf-writeups.notion.site/TCM-Butler-3b16dd07838a4fcc8e5c0419674367c1?pvs=4)***
