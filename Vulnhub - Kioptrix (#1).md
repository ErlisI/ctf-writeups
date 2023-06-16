# Vulnhub - Kioptrix (#1)

June 1, 2023 - *My first CTF*

---

## Introduction

This [Kioptrix](https://www.vulnhub.com/entry/kioptrix-level-1-1,22/) VM Image is an easy challenge. The purpose of these games is to learn the basic tools and techniques in vulnerability assessment and exploitation. There are more ways than one to successfully complete the challenges.
The object is to acquire root access and receive the flag via any means possible (Ethically).

---

## Information Gathering

***Port Scanning***

- Beginning the reconnaissance by running `nmap -T4 -p- -A {IP}`

```bash
Starting Nmap 7.93 ( https://nmap.org ) at 2023-06-01 09:31 EDT
Nmap scan report for {IP}
Host is up (0.00087s latency).
Not shown: 65529 closed tcp ports (reset)
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 2.9p2 (protocol 1.99)
|_sshv1: Server supports SSHv1
| ssh-hostkey: 
|   1024 b8746cdbfd8be666e92a2bdf5e6f6486 (RSA1)
|   1024 8f8e5b81ed21abc180e157a33c85c471 (DSA)
|_  1024 ed4ea94a0614ff1514ceda3a80dbe281 (RSA)
80/tcp    open  http        Apache httpd 1.3.20 ((Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: Test Page for the Apache Web Server on Red Hat Linux
111/tcp   open  rpcbind     2 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2            111/tcp   rpcbind
|   100000  2            111/udp   rpcbind
|   100024  1          32768/tcp   status
|_  100024  1          32768/udp   status
139/tcp   open  netbios-ssn Samba smbd (workgroup: MYGROUP)
443/tcp   open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2009-09-26T09:32:06
|_Not valid after:  2010-09-26T09:32:06
|_http-server-header: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
|_ssl-date: 2023-06-01T13:31:55+00:00; +4s from scanner time.
| sslv2: 
|   SSLv2 supported
|   ciphers: 
|     SSL2_RC4_128_EXPORT40_WITH_MD5
|     SSL2_DES_192_EDE3_CBC_WITH_MD5
|     SSL2_RC2_128_CBC_WITH_MD5
|     SSL2_DES_64_CBC_WITH_MD5
|     SSL2_RC4_64_WITH_MD5
|     SSL2_RC2_128_CBC_EXPORT40_WITH_MD5
|_    SSL2_RC4_128_WITH_MD5
|_http-title: 400 Bad Request
32768/tcp open  status      1 (RPC #100024)
MAC Address: 00:0C:29:91:37:CB (VMware)
Device type: general purpose
Running: Linux 2.4.X
OS CPE: cpe:/o:linux:linux_kernel:2.4
OS details: Linux 2.4.9 - 2.4.18 (likely embedded)
Network Distance: 1 hop

Host script results:
|_clock-skew: 3s
|_smb2-time: Protocol negotiation failed (SMB2)
|_nbstat: NetBIOS name: KIOPTRIX, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)

TRACEROUTE
HOP RTT     ADDRESS
1   0.87 ms {IP}

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 30.45 seconds
```

- Running `Nikto` for web vulnerability scanning
    
    ```bash
    - Nikto v2.5.0
    ---------------------------------------------------------------------------
    + Target IP:          {IP}
    + Target Hostname:    {IP}
    + Target Port:        80
    + Start Time:         2023-06-01 10:34:35 (GMT-4)
    ---------------------------------------------------------------------------
    + Server: Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
    + /: Server may leak inodes via ETags, header found with file /, inode: 34821, size: 2890, mtime: Wed Sep  5 23:12:46 2001. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2003-1418
    + /: The anti-clickjacking X-Frame-Options header is not present. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
    + /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
    + OpenSSL/0.9.6b appears to be outdated (current is at least 3.0.7). OpenSSL 1.1.1s is current for the 1.x branch and will be supported until Nov 11 2023.
    + Apache/1.3.20 appears to be outdated (current is at least Apache/2.4.54). Apache 2.2.34 is the EOL for the 2.x branch.
    + mod_ssl/2.8.4 appears to be outdated (current is at least 2.9.6) (may depend on server version).
    + /: Apache is vulnerable to XSS via the Expect header. See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2006-3918
    + OPTIONS: Allowed HTTP Methods: GET, HEAD, OPTIONS, TRACE .
    + /: HTTP TRACE method is active which suggests the host is vulnerable to XST. See: https://owasp.org/www-community/attacks/Cross_Site_Tracing
    + Apache/1.3.20 - Apache 1.x up 1.2.34 are vulnerable to a remote DoS and possible code execution.
    + Apache/1.3.20 - Apache 1.3 below 1.3.27 are vulnerable to a local buffer overflow which allows attackers to kill any process on the system.
    + Apache/1.3.20 - Apache 1.3 below 1.3.29 are vulnerable to overflows in mod_rewrite and mod_cgi.
    + mod_ssl/2.8.4 - mod_ssl 2.8.7 and lower are vulnerable to a remote buffer overflow which may allow a remote shell.
    + ///etc/hosts: The server install allows reading of any system file by adding an extra '/' to the URL.
    + /usage/: Webalizer may be installed. Versions lower than 2.01-09 vulnerable to Cross Site Scripting (XSS). See: http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2001-0835
    + /manual/: Directory indexing found.
    + /manual/: Web server manual found.
    + /icons/: Directory indexing found.
    + /icons/README: Apache default file found. See: https://www.vntweb.co.uk/apache-restricting-access-to-iconsreadme/
    + /test.php: This might be interesting.
    + /wp-content/themes/twentyeleven/images/headers/server.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
    + /wordpress/wp-content/themes/twentyeleven/images/headers/server.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
    + /wp-includes/Requests/Utility/content-post.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
    + /wordpress/wp-includes/Requests/Utility/content-post.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
    + /wp-includes/js/tinymce/themes/modern/Meuhy.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
    + /wordpress/wp-includes/js/tinymce/themes/modern/Meuhy.php?filesrc=/etc/hosts: A PHP backdoor file manager was found.
    + /assets/mobirise/css/meta.php?filesrc=: A PHP backdoor file manager was found.
    + /login.cgi?cli=aa%20aa%27cat%20/etc/hosts: Some D-Link router remote command execution.
    + /shell?cat+/etc/hosts: A backdoor was identified.
    + /#wp-config.php#: #wp-config.php# file found. This file contains the credentials.
    + 8908 requests: 0 error(s) and 30 item(s) reported on remote host
    + End Time:           2023-06-01 10:34:59 (GMT-4) (24 seconds)
    ---------------------------------------------------------------------------
    + 1 host(s) tested
    ```
    
- The open ports are 22, 80/443, 111, 139, and 32768

***Port 22***

- SSH
    - OpenSSH 2.9p2
- Trying to connect to ssh
    
    ![Screenshot 2023-06-01 at 11.03.09 AM.png](Vulnhub%20-%20Kioptrix%20(#1)%20a38e2842a9ab4550a4b0143024d8701e/Screenshot_2023-06-01_at_11.03.09_AM.png)
    

***Port 80/443***

- Port 80
    - Some “Juicy” information
        - + Apache/1.3.20 - Apache 1.x up 1.2.34 are vulnerable to a remote DoS and possible code execution.
            - [https://www.exploit-db.com/exploits/764](https://www.exploit-db.com/exploits/764)
        - + mod_ssl/2.8.4 - mod_ssl 2.8.7 and lower are vulnerable to a remote buffer overflow which may allow a remote shell.
            - [https://www.rapid7.com/db/vulnerabilities/HTTP-MODS-0003/](https://www.rapid7.com/db/vulnerabilities/HTTP-MODS-0003/)
            - https://github.com/heltonWernik/OpenLuck
    
- Port 443
    - Nothing we can find or do here

![Screenshot 2023-06-01 at 10.31.53 AM.png](Vulnhub%20-%20Kioptrix%20(#1)%20a38e2842a9ab4550a4b0143024d8701e/Screenshot_2023-06-01_at_10.31.53_AM.png)

***Port 139***

- Samba smb
    - Finding the `smb` version using `Metasploit`
        
        ![Screenshot 2023-06-01 at 11.26.45 AM.png](Vulnhub%20-%20Kioptrix%20(#1)%20a38e2842a9ab4550a4b0143024d8701e/Screenshot_2023-06-01_at_11.26.45_AM.png)
        
    - Found an auxiliary scanner to find the `smb` version. Now all we have to do is set the `RHOST (the target)`
        
        ![Screenshot 2023-06-01 at 11.28.20 AM.png](Vulnhub%20-%20Kioptrix%20(#1)%20a38e2842a9ab4550a4b0143024d8701e/Screenshot_2023-06-01_at_11.28.20_AM.png)
        
    - Found the exact version
        
        ![Screenshot 2023-06-01 at 11.30.25 AM.png](Vulnhub%20-%20Kioptrix%20(#1)%20a38e2842a9ab4550a4b0143024d8701e/Screenshot_2023-06-01_at_11.30.25_AM.png)
        
        - [https://www.rapid7.com/db/modules/exploit/linux/samba/trans2open/](https://www.rapid7.com/db/modules/exploit/linux/samba/trans2open/)
        - [https://www.cvedetails.com/vulnerability-list/vendor_id-102/product_id-171/version_id-373318/Samba-Samba-2.2.1a.html](https://www.cvedetails.com/vulnerability-list/vendor_id-102/product_id-171/version_id-373318/Samba-Samba-2.2.1a.html)
        - [https://www.exploit-db.com/exploits/10](https://www.exploit-db.com/exploits/10)

---

## Exploitation and Root access

***Port 22***

- Trying to Brute Force port 22 using `Metasploit` and the built-in wordlist
- After running it for a while with no result, I stopped it and went to try to exploit other ports

***Port 80***

- Using OpenFuck, we see that we have the exact version exploitable of the Apache server ~ `0x6b`
- Running it
    
    ![Screenshot 2023-06-01 at 11.37.25 AM.png](Vulnhub%20-%20Kioptrix%20(#1)%20a38e2842a9ab4550a4b0143024d8701e/Screenshot_2023-06-01_at_11.37.25_AM.png)
    
- Root access gained
    
    ![Screenshot 2023-06-01 at 11.39.14 AM.png](Vulnhub%20-%20Kioptrix%20(#1)%20a38e2842a9ab4550a4b0143024d8701e/Screenshot_2023-06-01_at_11.39.14_AM.png)
    
    ```bash
    cat /etc/shadow
    
    root:$1$XROmcfDX$tF93GqnLHOJeGRHpaNyIs0:14513:0:99999:7:::
    bin:*:14513:0:99999:7:::
    daemon:*:14513:0:99999:7:::
    adm:*:14513:0:99999:7:::
    lp:*:14513:0:99999:7:::
    sync:*:14513:0:99999:7:::
    shutdown:*:14513:0:99999:7:::
    halt:*:14513:0:99999:7:::
    mail:*:14513:0:99999:7:::
    news:*:14513:0:99999:7:::
    uucp:*:14513:0:99999:7:::
    operator:*:14513:0:99999:7:::
    games:*:14513:0:99999:7:::
    gopher:*:14513:0:99999:7:::
    ftp:*:14513:0:99999:7:::
    nobody:*:14513:0:99999:7:::
    mailnull:!!:14513:0:99999:7:::
    rpm:!!:14513:0:99999:7:::
    xfs:!!:14513:0:99999:7:::
    rpc:!!:14513:0:99999:7:::
    rpcuser:!!:14513:0:99999:7:::
    nfsnobody:!!:14513:0:99999:7:::
    nscd:!!:14513:0:99999:7:::
    ident:!!:14513:0:99999:7:::
    radvd:!!:14513:0:99999:7:::
    postgres:!!:14513:0:99999:7:::
    apache:!!:14513:0:99999:7:::
    squid:!!:14513:0:99999:7:::
    pcap:!!:14513:0:99999:7:::
    john:$1$zL4.MR4t$26N4YpTGceBO0gTX6TAky1:14513:0:99999:7:::
    harold:$1$Xx6dZdOd$IMOGACl3r757dv17LZ9010:14513:0:99999:7:::
    ```
    

***Port 139***

- Using the information that I got from the version. `trans2open` seemed to do the trick using `Metasploit`
    - `Meterpreter session 5 closed.  Reason: Died` ~ after the first try, when looking at the `options` we change the payload from `staged` to `non-staged`
        
        ![Screenshot 2023-05-30 at 4.49.54 PM.png](Vulnhub%20-%20Kioptrix%20(#1)%20a38e2842a9ab4550a4b0143024d8701e/Screenshot_2023-05-30_at_4.49.54_PM.png)
        
    - Root access gained
        
        ![Screenshot 2023-06-01 at 11.52.27 AM.png](Vulnhub%20-%20Kioptrix%20(#1)%20a38e2842a9ab4550a4b0143024d8701e/Screenshot_2023-06-01_at_11.52.27_AM.png)
