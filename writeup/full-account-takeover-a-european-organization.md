[![Twitter](https://img.shields.io/badge/twitter%20-%231DA1F2.svg?&style=for-the-badge&logo=Twitter&logoColor=white&label=Follow%20%40xbforce)](https://twitter.com/xbforce)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
[![Youtube](https://img.shields.io/badge/Youtube%20-%23FF0000.svg?&style=for-the-badge&logo=YouTube&logoColor=white&label=Subscribe)](http://www.youtube.com/channel/UCadRCMA7BFJ2iwiABKqf0Fg?sub_confirmation=1)

# Full Account Takeover on a European organization


```
Bug Type: 	IDOR; Full Account Takeover
Affected URL: 	https://www.redacted.eu/
```


**Summary:**

Full Account Takeover happens when the hacker has full access to any account on the targeted web application. He is able to access accounts, read any Private Information of the victims he wants, change email addresses and passwords, and even delete accounts.

**How did I start finding bugs on the targeted website?**

If you already read my bug hunting methodology you know that one of the techniques that I use during the recon phase is google dorking. It is sometimes possible to find juicy files or even bugs during dorking on google. I already found some URLs on other targets which lead to bugs like XSS and Open Redirection which I chained it to SSRF. 

I did the same for ```redacted.eu``` and found an open redirection on google. I ususally start my tests with account takeover (After finishing recon phase), but because I found an open redirection on the target I started testing the website with openR and tried to escalate it to something more important like SSRF but it wasn’t possible. 

After being unsuccessful on chaining openR to another vulnerability I started looking for ways to takeover accounts. One important thing that I do when I try to takeover accounts is **getting familiar with the target, its pages and services**, for this reseaon I read the source code of different pages, check the URLs carefully and change values and parameters to see what will happen. This way I can find out what the best way for taking over accounts is and even if it worths to spend time on or not. 

On the target, I visited my profile but there was nothing special in the URL: ```https://redacted.com/profile```

By looking at my profiles source code I found a hidden ```name``` with my email address as the value. Something like this: 

```type=”hidden” name=”dashboard_user” value=”myAddress@gmail.com”```

So I decided to add ```dashboard_user``` as a parameter and my email address as the value to the URL but it redirected me to the profile page again:

```https://redacted.eu/profile/?dashboard_user=myAddress@gmail.com``` Redirected to: ```https://redacted.eu/profile```


I also put my second email address which I used to register the second account, but I redirected to the first accounts profile again. After trying different ways to takeover accounts I decided to move on to test the target for another vulnerability, maybe I can find something that helps to chain bugs and takeover accounts.

**Reset Password Link**

When I register accounts, I request a reset password link, then change the email address and password as a victim and wait for 24 hours before I use the ```reset password link```. This way I start testing for **Broken Authentication** vulnerabilities.

When I checked the ```reset password link``` an ID parameter caught my eyes:

```
https://redacted.eu/reset_password/forgot?token=SoMeRanDomCharaCTerS&rt=1602210146&id=413767
```

After I finished my test for Broken Authentication successfully, I requested another ```reset password link``` and again the ID number was the same as the first one but the ```token``` and ```rt``` were different. Actually ```rt``` was the epoch time. This time I requested another link for my second account to examine the ID number, and surprisingly it was near to the first account’s ID:

```
My first account ID:	413767
My second account ID:	413769
```

To be sure that ID numbers are created in sequence I registered two more accounts, one after the other immediately:

```
My third account ID:	413780
My fourth account ID:	413781
```

At this moment I was sure that the user IDs are chosen in sequence. For example if the first user has ```ID 100001``` then the second user’s ID is ```100002``` and so on and so forth. This way I was able to examine the exact number of users on the target website, which was ```313781``` at the time.
After examining the ID numbers, I thought that maybe it is possible to use the ID number as a value for ```dashboard_user=```, and I was right! I was logged in with my first account, so after using the following URL I redirected to the profile page of the first account: 

```
https://redacted.eu/profile/?dashboard_user=413767
```

But the URL didn’t change to ```https://redacted.eu/profile```. So I put the second account’s ID number (I was still logged in to the first testing account):

```
https://redacted.eu/profile/?dashboard_user=413769
```

To my surprise I got access to the second testing user’s account and were able to see private information, private uploaded files, to change the name, email address and the password.

I wanted to complete the job with deleting the second user’s account, but when I hit the delete button my first account (attacker account) was deleted and I logged out of the account. Actually to delete a victim’s account I had to change the password of the victim first, then log into his account with the new password and finally delete the account.

After doing the job successfully I prepared a report and sent it to the affected organization and they fix the issue immediately.

**Account Takeover** is more than checking the login page. Be creative and read the source codes and URLs carefully if you are going to takeover accounts.

</br>

### You can support me if you found this writeup useful.

**To support me you can buy Hacking courses through my voucher:**

:moneybag:	https://store.7asecurity.com/discount/97C9-28DB-5815-44EE

**Or donate through the following links:**

:moneybag:	https://paypal.me/pools/c/8pL97bLm3r

:moneybag:	https://www.buymeacoffee.com/xbforce

