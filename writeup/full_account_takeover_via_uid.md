[![Twitter](https://img.shields.io/badge/twitter%20-%231DA1F2.svg?&style=for-the-badge&logo=Twitter&logoColor=white&label=Follow%20%40xbforce)](https://twitter.com/xbforce)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
[![Youtube](https://img.shields.io/badge/Youtube%20-%23FF0000.svg?&style=for-the-badge&logo=YouTube&logoColor=white&label=Subscribe)](http://www.youtube.com/channel/UCadRCMA7BFJ2iwiABKqf0Fg?sub_confirmation=1)

# Full Account Takeover Via UID Parameter

Recently I was working on a project and was able to takeover all accounts on the client's website. This ATO is almost straight-forward and you only need to pay attention to the requests you send.

I tried different techniques to do ATO but non of them opened any channel for further tests and ATO. After some unsuccessful tests I tested the target through the profile page by changing the user information. One of the biggest mistakes that developers make is creating ID numbers or other elements in sequence, which can cause their products to be vulnerable to different vulnerabilities.

:heavy_check_mark: *As always my burpsuite tool is capturing every request and respond from the moment I start working on a program.*

So, I changed my information (for the attacker account) and send the request to the server. Along with the new information a ten character ```uid``` sent to the server: ```RS00681841```. It was the first time that I saw the UID on the program.

Then I guessed that maybe the website makes the UIDs in sequence, so I created another account as **Victim** and checked its UID which was almost similar to the Attacker's UID:

```
Attacker UID: RS00681841
Victim UID: RS00681846
```

681846 is maybe the number of users in that program. To make sure that this is the number of users I needed to find a way to takeover accounts or at least check the profile of users. So I continued the test and tried to change the information of the victim through sending a request with his UID. I came back to my Attacker profile and modified the information again but this time I sent the request to burp repeater and changed the UID from ```RS00681841``` to ```RS00681846```:

```
uid=RS00681846&first_name=Salvador&last_name=BeingHacked&birth_date=01%2f01%2f1985&address=somewhere%20else&phone_number=12345678&email_address=anAttackerMail@gmail.com&csrf_token=blahBLAHblah&update=
```

Then I refreshed the Victim's page and saw that the new information including my email are placed in the victim's profile. Immediately I requested a reset password link for the victim to see if I can takeover the account fully, and no surprise that I was able to do the job successfully. This means that I can do ```full account takeover``` against all users of the program in a short time by writting a code and having a list of email addresses, which is absolutely a nightmare for the company.


**For** changing this huge number of users you need to have enough email addresses, and there are two ways to do so:
1- You can use your own website or server and create many email addresses.
2- You can use a gmail address for different accounts. For doing this you can 
create a gmail with many characters (the maximum number) and put a dot ```.``` between each character: 

```
thisisanemailaddress@gmail.com
t.hisisanemailaddress@gmail.com
th.isisanemailaddress@gmail.com
thi.sisanemailaddress@gmail.com
and so on
```

**I had worked on another program with the same issue months ago before this one, but in that program I couldn't reset the password because there wasn't such a reset password button on the login page! If you cannot reset the password and takeover accounts but you can change the users' information then this is called ```Account Abusement```.**

What I did was enough to report to the program but I was curious about the number of users so that I can mention and bold it in my report. I continued the test and finally I found out that if I use the UID instead of the username on a profile's page I can see the profile of that user. For example the following is the URL for victim's profile:

```
https://redacted.com/profile/?username=victim
```

But I can use the UID instead of the username parameter and see the public profile of the same user:

```
https://redacted.com/profile/?uid=RS00681846
```

I changed the UID number to ```RS1``` but got a 404 error, then I changed it to ```RS00000001``` and it showed me a user's public profile which means there are **681846** users in the website.

Stay safe.

</br>

### You can support me if you found this writeup useful.

**To support me you can buy Hacking courses through my voucher:**

:moneybag:	https://store.7asecurity.com/discount/97C9-28DB-5815-44EE

**Or donate through the following links:**

:moneybag:	https://paypal.me/pools/c/8pL97bLm3r

:moneybag:	https://www.buymeacoffee.com/xbforce
