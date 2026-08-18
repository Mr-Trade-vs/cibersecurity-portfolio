# Report Scanme.nmap.org

### Read First:

It's important to explain that **$scanme.nmap.org$** allows making scan to his direction because is like a laboratory where everyone can parctice and improve their skills. Knowing this:

The porpuse with the report is to show vulnerabilities and think "what'll do an attacker", and the same scenario for a defender of the direction that's being attacked.

---

### Environment

I made this under a WSL Ubuntu distro, we start from the IP: **172.17.226.214**. When I execute ping command terminal return us a **TTL = 45**

    ping scanme.nmap.org
    PING scanme.nmap.org (45.33.32.156) 56(84) bytes of data.
    64 bytes from scanme.nmap.org (45.33.32.156): icmp_seq=1 ttl=45 time=133 ms
    64 bytes from scanme.nmap.org (45.33.32.156): icmp_seq=2 ttl=45 time=134 ms
    64 bytes from scanme.nmap.org (45.33.32.156): icmp_seq=3 ttl=45 time=134 ms
    64 bytes from scanme.nmap.org (45.33.32.156): icmp_seq=4 ttl=45 time=134 ms
    64 bytes from scanme.nmap.org (45.33.32.156): icmp_seq=5 ttl=45 time=133 ms

That means $TTL$ started in 64 and response takes 19 jumpes among the different networks.

To know what's the route that package used it we can use:

    traceroute scanme.nmap.org

This helpful to understand the package travel, from this enviroment we got:

    1  DESKTOP-5O1HOBL.mshome.net (172.17.224.1)  0.244 ms  0.232 ms  0.223 ms
    2  192.168.1.1 (192.168.1.1)  1.490 ms  0.962 ms  0.950 ms
    3  181.32.64.1 (181.32.64.1)  7.276 ms  6.462 ms  7.250 ms
    4  * * *
    5  190.98.141.28 (190.98.141.28)  19.686 ms  19.661 ms  19.638 ms
    6  94.142.117.11 (94.142.117.11)  61.602 ms  59.634 ms  59.619 ms
    7  * * *
    8  84.16.6.185 (84.16.6.185)  80.211 ms  76.529 ms  79.024 ms
    9  * * *
    10  ae1.r21.iad02.icn.netarch.akamai.com (23.209.165.93)  74.763 ms ae2.r23.iad02.icn.netarch.akamai.com (23.209.165.139)  75.847 ms ae1.r21.iad02.icn.netarch.akamai.com (23.209.165.93)  75.819 ms
    11  * * *
    12  ae16.r01.sjc01.icn.netarch.akamai.com (23.32.62.79)  130.267 ms ae16.r02.sjc01.icn.netarch.akamai.com (23.193.113.29)  129.895 ms ae16.r01.sjc01.icn.netarch.akamai.com (23.32.62.79)  134.043 ms
    13  ae1.r11.sjc01.ien.netarch.akamai.com (23.207.232.35)  131.782 ms  131.153 ms ae2.r11.sjc01.ien.netarch.akamai.com (23.207.232.39)  131.126 ms
    14  ae22.gw4-scz1.netarch.akamai.com (23.203.158.53)  135.487 ms  135.428 ms  135.411 ms
    15  * * *
    16  * * *
    17  * * *
    18  scanme.nmap.org (45.33.32.156)  133.898 ms  133.879 ms  133.860 ms

Every line means a jump between networks with the final porpuse of arrive to server that host the domain **scanme.nmap.org**. How you can see, some lines have **\*  \* \*** that isn't a error. Is just package arrived to a specific direction and is sending to another network but didn't get any response from that direction (every * is a package), that's related with security issues.

With the help of [NordVPN](https://nordvpn.com/es/ip-lookup/?srsltid=AfmBOopTil4G7VTIBD62_m07FjbpAerA0TnKmZadfL0x6pvYJ9xUoXmz) we can notice the packages travel around different countrys like Argentina, Spain and finally United States.

Deeping more in **scanme.nmap.org** we want to know an acces point to this server, using:

    nmap -sV -sC scanme.nmap.org

I got the next results:

    Starting Nmap 7.98 ( https://nmap.org ) at 2026-08-18 13:43 -0500
    Nmap scan report for scanme.nmap.org (45.33.32.156)
    Host is up (0.13s latency).
    Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
    Not shown: 995 closed tcp ports (conn-refused)
    PORT      STATE    SERVICE    VERSION
    22/tcp    open     ssh        OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (Ubuntu Linux; protocol 2.0)
    | ssh-hostkey:
    |   1024 ac:00:a0:1a:82:ff:cc:55:99:dc:67:2b:34:97:6b:75 (DSA)
    |   2048 20:3d:2d:44:62:2a:b0:5a:9d:b5:b3:05:14:c2:a6:b2 (RSA)
    |   256 96:02:bb:5e:57:54:1c:4e:45:2f:56:4c:4a:24:b2:57 (ECDSA)
    |_  256 33:fa:91:0f:e0:e1:7b:1f:6d:05:a2:b0:f1:54:41:56 (ED25519)
    80/tcp    open     http       Apache httpd 2.4.7 ((Ubuntu))
    |_http-title: Go ahead and ScanMe!
    |_http-favicon: Nmap Project
    |_http-server-header: Apache/2.4.7 (Ubuntu)
    161/tcp   filtered snmp
    9929/tcp  open     nping-echo Nping echo
    31337/tcp open     tcpwrapped
    Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 27.09 seconds

With this results now we know what ports are possible to make a connection, but I want to serach if someone is using a old version, because normally old systems don't get more updates and have more vulnerabilities found and for this scan I found 2 good choices:

- Port 22 -> SSH protocol -> Version: OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13
- Port 80 -> HTTP protocol -> Version: Apache httpd 2.4.7 ((Ubuntu))

Also we can see a special port (port 31337) knowing like "elite" famous by **Back Orifice** and be a back door for malicous programs. When we make a simple scan to **scanme.nmap.org**:

    nmap -sC scanme.nmap.org

We can notice that return something like this:

    Starting Nmap 7.98 ( https://nmap.org ) at 2026-08-18 17:42 -0500
    Nmap scan report for scanme.nmap.org (45.33.32.156)
    Host is up (0.13s latency).
    Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
    Not shown: 995 closed tcp ports (conn-refused)
    PORT      STATE    SERVICE
    22/tcp    open     ssh
    | ssh-hostkey:
    |   1024 ac:00:a0:1a:82:ff:cc:55:99:dc:67:2b:34:97:6b:75 (DSA)
    |   2048 20:3d:2d:44:62:2a:b0:5a:9d:b5:b3:05:14:c2:a6:b2 (RSA)
    |   256 96:02:bb:5e:57:54:1c:4e:45:2f:56:4c:4a:24:b2:57 (ECDSA)
    |_  256 33:fa:91:0f:e0:e1:7b:1f:6d:05:a2:b0:f1:54:41:56 (ED25519)
    80/tcp    open     http
    |_http-favicon: Nmap Project
    |_http-title: Go ahead and ScanMe!
    161/tcp   filtered snmp
    9929/tcp  open     nping-echo
    31337/tcp open     Elite

How we can look, port 31337 have a special service "Elite", but if we make a more deep scan. Like the first we made, we can see this service changed to "tcpwrapped". Why? Is so simple, that means nmap try it to make a connection but the port acept the connection and then close it without sending any information.

There are old versions that must have vulnerabilities, when I searched some CVE reports I found this:

**In OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13**

In **SSH** versions through 7.7 we have the report **CVE-2018-15473** related with user enumeration vulnerability. Of the view from an attacker, if I succeeded exploit this vulnerabilitie I'll make a script that trys the most common password in all the users. If someone allow me the acces the work is done.

**In Apache httpd 2.4.7**

Here I found the report **CVE-2014-0118** that affect versions before $2.4.10$ where a fail is related with the module **mod_deflate**, this module has the work of compress and decompress the request. An attacker send a *HTTP* request with inflated data, if I'm a hacker I'll abuse the inflated data request to make server outage. 
