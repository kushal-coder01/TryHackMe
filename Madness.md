# TryHackMe Walkthrough 
Room link: https://tryhackme.com/r/room/madness

## Nmap:
```
nmap -sV 10.10.11.214 
```
<pre>
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-24 05:36 EDT
Nmap scan report for 10.10.11.214
Host is up (0.15s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.81 seconds
</pre>

## Opening the IP in browser:
### The page is default web page 
![Screenshot 2024-07-24 150831](https://github.com/user-attachments/assets/2958e89c-7ebc-42da-8d3c-4b32bbb6a392)
## Looking at source code:
### Found a image link in there 
![Screenshot 2024-07-24 151125](https://github.com/user-attachments/assets/fdc4ebf2-5558-4c91-95cd-d6efcf9d0d43)
### Downloaded the image using the wget command:
```
wget http://10.10.11.214/thm.jpg
```
<pre>
--2024-07-24 05:44:05--  http://10.10.11.214/thm.jpg
Connecting to 10.10.11.214:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 22210 (22K) [image/jpeg]
Saving to: ‘thm.jpg’

thm.jpg                                                     100%[========================================================================================================================================>]  21.69K  --.-KB/s    in 0.1s    

2024-07-24 05:44:06 (151 KB/s) - ‘thm.jpg’ saved [22210/22210]
</pre>
### But the image doesn't open due to some error:
![Screenshot 2024-07-24 151604](https://github.com/user-attachments/assets/15495840-57cb-467d-925e-4db4f7015b94)

## Looking at the signatures of the image using head command:
```
head thm.jpg
```
<pre>
�PNG
�
��C











��C		
�����

���}!1AQa"q2��#B��R��$3br�	
�%&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz���������������������������������������������������������������������������

���w!1AQaq"2B����	#3R�br�
$4�%��&'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz��������������������������������������������������������������������������
                                           ?���P�A@

</pre>
### The signature is of png image and not jpg, searching for jpg file signatures online and changing it in the thm.jpg using the hexeditor.
![Screenshot 2024-07-24 151906](https://github.com/user-attachments/assets/4bda1188-79fa-4589-af96-6cf686ddf67b)

```
hexeditor thm.jpg
```
![Screenshot 2024-07-24 152107](https://github.com/user-attachments/assets/ab0a1551-5e6e-45eb-87f3-4da393af532a)
#### Save and exit the editor and try opening the image and found the hidden directory:
![thm](https://github.com/user-attachments/assets/85e25db4-f012-4403-825c-5becc9f90f77)

## Navigating to the directory:
![Screenshot 2024-07-24 152502](https://github.com/user-attachments/assets/cec4e828-4b02-4b00-8e73-c1a4014b9d70)
### Viewing the source code:
![Screenshot 2024-07-24 152550](https://github.com/user-attachments/assets/5fcaeef5-6e5d-493a-adf1-30b23df9bdbe)
#### The page says secret entered and also its between 0-99 that means we need to try entering request numbers from 0-99:
![Screenshot 2024-07-24 152850](https://github.com/user-attachments/assets/63ced300-cc85-4a0c-8db5-1dd4e36e793a)
### Using burp suite to automate the requests:
#### Setting the Attack type to sniper:
![Screenshot 2024-07-24 153039](https://github.com/user-attachments/assets/e050e136-a557-4746-b10f-7491ebf56712)
#### Setting the payload to numbers from 0-99:
![Screenshot 2024-07-24 153341](https://github.com/user-attachments/assets/ba89beb4-a17d-4a28-b141-9da8d3111280)
#### Completed the attack and the request with the highest length is our secret number:
![Screenshot 2024-07-24 154259](https://github.com/user-attachments/assets/d42cc51e-b7da-437b-b709-b87ff2ce7c0b)
#### We got a password, but as we have no username we can't use it for the ssh login, so we need to used it for stegnography:
![Screenshot 2024-07-24 154820](https://github.com/user-attachments/assets/90dc5453-af31-4d29-9eb5-cde10d4a1ab6)
#### We got a username but have no password looking for the password. Downlaoding the room image to get the password:
![Screenshot 2024-07-24 160743](https://github.com/user-attachments/assets/aedf289a-a1a6-414c-b8d0-6879d591f403)
### Logging in using the username and password:
![Screenshot 2024-07-24 155357](https://github.com/user-attachments/assets/86c93900-6dc0-4376-b9b5-0b4c8a713f32)
#### But that doesn't work, Then I looked at the hint and saw that it says "There's something ROTten about this guys name!" then decrypted the username:
![Screenshot 2024-07-24 155912](https://github.com/user-attachments/assets/afbe940b-b499-4da8-846c-77576ca16783)
#### Successfully logged in:
![Screenshot 2024-07-24 160004](https://github.com/user-attachments/assets/834c5b79-a8c3-412b-8941-45a4a9fb297e)

### Unfortunately Joker does not have any sudo privileges, so we will have to enumerate further. 
```
find / -type f -perm -4000 2>/dev/null
```
<pre>
  /usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/bin/vmware-user-suid-wrapper
/usr/bin/gpasswd
/usr/bin/passwd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/sudo
/bin/fusermount
/bin/su
/bin/ping6
/bin/screen-4.5.0
/bin/screen-4.5.0.old
/bin/mount
/bin/ping
/bin/umount
</pre>
### Here the /bin/screen-4.5.0 looks intresting so serached for it on the exploit db and got a exploit code I then copied it to the target machine
![Screenshot 2024-07-24 160427](https://github.com/user-attachments/assets/7540dd8c-7787-4dc5-8f3b-b91405af4118)
### Giving it the excuting permissions and running it:
![image](https://github.com/user-attachments/assets/5817402e-0094-47f3-9343-1a6369e0d5d4)














