# Machine Name : Preignition
## Platform : HackTheBox
### Difficulty : Easy
----------------------------------------------------------------------------------------------------------
# Enumeration -

```bash
nmap -p- -T4 -min-rate=10000 10.129.246.247 -Pn -vv 
```

We found only one open port which was 80. 
Performing aggressive scan to find its services 

```bash
nmap -p80 -A -sV -sC -vv -T4 <IP> -oA agr_scan
```

```
PORT   STATE SERVICE REASON         VERSION
80/tcp open  http    syn-ack ttl 63 nginx 1.14.2
|_http-title: Welcome to nginx!
|_http-server-header: nginx/1.14.2
| http-methods: 
|_  Supported Methods: GET HEAD
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
```
Visiting the Web page , it shows a default page for nginx server. Lets perform dir busting.

```bash

gobuster dir -u http://10.129.246.247/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.246.247/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/admin.php            (Status: 200) [Size: 999]

```
*Using php as extention*

Visiting that website shows a login form , by checking the default credentials admin:admin , we got logged in and got the flag.


PREIGNITION HAS BEEN PWNED!!!!
-------------------------------------------------------------------------------------------------------------------------------------------

# Machine Name: Preignition
## Platform: HackTheBox
### Difficulty: Easy
------------------------------------------------------------------------------------------------------------------------------------------
# Enumeration

We start with a full TCP port scan:
```bash
nmap -p- -T4 --min-rate=10000 -Pn -vv 10.129.246.247
```
Result: Only port 80 (HTTP) was open.

We run a service/version detection scan on the open port:
```bash
nmap -p80 -A -sV -sC -T4 -Pn -oA agr_scan 10.129.246.247

PORT   STATE SERVICE VERSION
80/tcp open  http    nginx 1.14.2
|_http-title: Welcome to nginx!
|_http-server-header: nginx/1.14.2
| http-methods: 
|_  Supported Methods: GET HEAD
```

The default Nginx welcome page was displayed, indicating a potentially misconfigured web server.
------------------------------------------------------------------------------------------------------------------------------------------
📂 Web Enumeration

We perform directory brute-forcing using Gobuster with PHP extensions:
```bash
gobuster dir -u http://10.129.246.247/ -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php
```
```
Result:

/admin.php            (Status: 200)
```
Navigating to /admin.php, we found a login panel. Trying common default credentials:
```
Username: admin  
Password: admin  
```
It worked! After logging in, the flag was revealed.

🧠 Key Takeaway

💡 When encountering a login panel, always try default credentials like admin:admin.
Misconfigured web applications often expose admin panels with weak or no authentication.

🎉 Preignition has been PWNED!

------------------------------------------------------------------------------------------------------------------------------------------