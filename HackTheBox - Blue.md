# HackTheBox - Blue

June 2, 2023 

---

## Introduction

This [HackTheBox](https://app.hackthebox.com/machines/Blue) is an easy challenge.
The object is to acquire root access and receive the flag via any means possible (Ethically).

---

## Information Gathering

***Port Scanning***

- Beginning the reconnaissance by running `nmap -T4 -p- -A {IP}`
    
    ```bash
    Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-02 10:24 EDT
    Nmap scan report for WIN-845Q99OO4PP (192.168.1.168)
    Host is up (0.00077s latency).
    Not shown: 65526 closed tcp ports (reset)
    PORT      STATE SERVICE      VERSION
    135/tcp   open  msrpc        Microsoft Windows RPC
    139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
    445/tcp   open  microsoft-ds Windows 7 Ultimate 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
    49152/tcp open  msrpc        Microsoft Windows RPC
    49153/tcp open  msrpc        Microsoft Windows RPC
    49154/tcp open  msrpc        Microsoft Windows RPC
    49155/tcp open  msrpc        Microsoft Windows RPC
    49156/tcp open  msrpc        Microsoft Windows RPC
    49158/tcp open  msrpc        Microsoft Windows RPC
    MAC Address: 00:0C:29:6E:7C:AC (VMware)
    Device type: general purpose
    Running: Microsoft Windows 7|2008|8.1
    OS CPE: cpe:/o:microsoft:windows_7::- cpe:/o:microsoft:windows_7::sp1 cpe:/o:microsoft:windows_server_2008::sp1 cpe:/o:microsoft:windows_server_2008:r2 cpe:/o:microsoft:windows_8 cpe:/o:microsoft:windows_8.1
    OS details: Microsoft Windows 7 SP0 - SP1, Windows Server 2008 SP1, Windows Server 2008 R2, Windows 8, or Windows 8.1 Update 1
    Network Distance: 1 hop
    Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
    
    Host script results:
    |_nbstat: NetBIOS name: WIN-845Q99OO4PP, NetBIOS user: <unknown>, NetBIOS MAC: 000c296e7cac (VMware)
    | smb2-security-mode: 
    |   210: 
    |_    Message signing enabled but not required
    |_clock-skew: mean: 1h19m59s, deviation: 2h18m33s, median: 0s
    | smb-security-mode: 
    |   account_used: guest
    |   authentication_level: user
    |   challenge_response: supported
    |_  message_signing: disabled (dangerous, but default)
    | smb2-time: 
    |   date: 2023-06-02T14:26:05
    |_  start_date: 2023-06-02T14:12:33
    | smb-os-discovery: 
    |   OS: Windows 7 Ultimate 7601 Service Pack 1 (Windows 7 Ultimate 6.1)
    |   OS CPE: cpe:/o:microsoft:windows_7::sp1
    |   Computer name: WIN-845Q99OO4PP
    |   NetBIOS computer name: WIN-845Q99OO4PP\x00
    |   Workgroup: WORKGROUP\x00
    |_  System time: 2023-06-02T10:26:05-04:00
    
    TRACEROUTE
    HOP RTT     ADDRESS
    1   0.77 ms WIN-845Q99OO4PP (192.168.1.168)
    
    OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 90.10 seconds
    ```
    
- We have a couple of ports open, but only the 139/445 seem promising
    - “Dynamic ports—Ports in the range 49152 to 65535 are not assigned, controlled, or registered. They are used for **temporary or private ports**. They are also known as private or non-reserved ports. Clients should choose ephemeral port numbers from this range, but many systems do not”
    - Also nothing for port 135
    

***Port 139/445***

- SMB
- From the scan, we get the OS version (`OS: Windows 7 Ultimate 7601 Service Pack 1`), and with a quick Google search, there was a vulnerability called `EternalBlue` for which there were a lot of GitHub repos to clone from e.g: https://github.com/3ndG4me/AutoBlue-MS17-010and exploit, but I used the [Rapid7](https://www.rapid7.com/db/modules/exploit/windows/smb/ms17_010_eternalblue/) one and followed through with the steps.

---

## Exploitation and Root access

***Port 139/445***

- Using the information listed above, the root access was easy and quick to get
    -  <img width="353" alt="Screenshot_2023-06-02_at_11 10 49_AM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/948c1f25-b50a-44fa-9cf5-4df5a0524d1f">   
    - <img width="742" alt="Screenshot_2023-06-02_at_11 10 59_AM" src="https://github.com/ErlisI/ctf-writeups/assets/63165733/660ba844-1b8c-476e-9c10-d565d7ef0941">

    
***Also check it out [here](https://e-ctf-writeups.notion.site/HackTheBox-Blue-f0658ec36ead4ba486bc2f77610593b4)***
