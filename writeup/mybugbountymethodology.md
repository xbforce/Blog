
# My Bug Bounty methodology


This is how I work on bug bounty programs. Please let me know via my twitter if I should do anything else to make my methodology more effective.

<br/>

:heavy_check_mark: **Recon**

Gathering subdomains by using different tools and techniques like:

```
amass, subfinder, assetfinder, cert.sh, source codes
```

After gathering subdomains, I check the alive ones manually through google chrome (I don't like screenshots!), and I prioritize subdomains when I'm checking them:

```
#Check them immediately
some subdomains here

#Fuzz them with ffuf
some subs here

#Send them to 404 file
some 404 subs here to check them some time later.
.
.
etc
```

Use dmitry, whatweb, google dork and github to gather more information about the target.

After gathering some information I start working on the target by getting familiar with its features and clicking on every possible button and capturing the traffic with burp.

As I'm checking the target I take notes about different pages, for example if a pages has some inputs I take a note to ```try XSS``` on that page, or to ```test the upload``` feature etc.

Then, I look at my notes and check those pages first, and after that I test the target for different vulnerabilities like the ones I mentioned below: (depends on the target I may test in different orders.)

<br/>

:heavy_check_mark: **Google Dorking**

1. Dork open redirection

2. Dork different extensions like .py, .php, .asp, xml


<br/>

:heavy_check_mark: **Github Dorking**

1. Dork api keys: api-key, api_key, api.key, apikey, access_token

2. Dork password, secret, username

<br/>


:heavy_check_mark: **Broken Authentication**

1. Request a password reset link as ATTACKER, then change the email address and password as VICTIM, and after that try to reset the password with the link as ATTACKER.

2. Check if you can use password reset link after 24 hours.

<br/>


:heavy_check_mark: **Cross Site Scripting (XSS)**

1. Click on every function and capture the traffic with burpsuite and copy URLs which contain parameters then use kooxss tool to find XSS.

2. Use waybackurl to gather URLs, then modify URLs and test them with kooxss.

```
$ cat target_domain.txt | waybackurl > wbu_target.com.txt
$ kooxss wbu_target.com.txt ATTACKER.com
```

*kooXSS is a tool that I wrote in bash, it checkes parameters if their values reflect in the response.

*I look for ```" > <``` via kooxss. Of course I look at different parameters manually for XSS, but as the first step and to speed up the test, I use this tool first.


3. Filter burp proxy history for possible DOM XSS events or the followings:

```
- [url]
- value=""
- value=''
- window.location.host
- window.location.href
- location.search
```

<br/>


:heavy_check_mark: **Open Redirection (openR)**
1. Use the URLs that you gathered from burpsuite and waybackurl to check them for openR.


<br/>

:heavy_check_mark: **Server Side Request Forgery (SSRF)**
1. If you got any Open Redirection then check the target for SSRF.
2. Look at burpsuite Dashboard for HTTP Forwarding.

3- Try this:
```http://169.254.169.254/latest/meta-data```

<br/>


:heavy_check_mark: **Web Cache Poisoning**

1. Filter proxy history on burpsuite to find "HIT" or "MISS" values.

- Use the followings:

```
X-Forwarded-Host: myServer.com
X-Forwarded-Host: myServer.com%22%2522%25%32%32
X-Forwarded-Host: myServer.com">
X-Forwarded-Host: myServer.com%0d%0a%09kzw
X-Host: myServer.com
X-Forwarded-For: myServer.com
X-Rewrite-Url: myServer.com
X-Original-Url: myServer.com
X-Requested-By: myServer.com
X-Requested-With: myServer.com
```

<br/>


:heavy_check_mark: **Cross Origin Resource Sharing (CORS)**

1- If the endpoint contains sensitive information try CORS:

- Use these Request headers to see if they reflect in the Response header:

```
Origin: attacker.com
Origin: attackertarget.com
Origin: target.com.attacker.com
```

<br/>



:heavy_check_mark: **XML External Entities (XML)**

1. Filter proxy history on burpsuite to find xml Requests.


:heavy_check_mark: **Server Side Template Injection (SSTI)**

1. Use URLs from burpsuite and waybackurl to check them form SSTI.

2. Don't forget to check POST Method paramters.

3. Use this payload to get an error:

```
${{<%[%'"}}%\
! Check the following file for more payloads:
$ cat ssti_cheatsheet.txt
```

<br/>


:heavy_check_mark: **ACCOUNT Takeover**

1. Go for Account Takeover and IDOR.


<br/>

:heavy_check_mark: **Cross Site Request Forgery (CSRF)**

1. Check if you can change emails, passwords usernames or any sensitive info with CSRF.

<br/>


:heavy_check_mark: **oAuth Misconfiguration**

1. Check oAuth.

<br/>


:heavy_check_mark: **Subdomain Takeover**

1. Use goldigger tool to check new subdomains for this vulnerability.

2. Look at old subdomains time to time for Subdomain takeover.

*Goldigger is a simple bash script to check subdomains to see if they have CNAME.

<br/>


:heavy_check_mark: **HTTP Request Smuggling**

1. Use smuggler.py tool to check new alive subdomains for this vulnerability.


<br/>

:heavy_check_mark: **Remote Code Execution (RCE)**

1. Test for RCE.


<br/>

:heavy_check_mark: **SQL Injection**

1. Test for SQLi with sleep command.

! Payloads:

```
' or sleep(15)
1=1# ' or sleep(15)#
' union select sleep(15),null# keywordhere' and sleep(15)# 
```

<br/>


:heavy_check_mark: **Clickjacking**

1. If the program allows, then check it for Clickjacking vulnerability.

<br/>


:heavy_check_mark: **Read .js files**

1. Read .js files for sensitive information or finding different vulnerabilites like XSS.

- Check the followings:

```
staging,
stg,
dev,
prod,
swagger,
access_token,
sectoken,
secret,
api,
apisecret,
x-api-key,
apidocs,
/api/,
/internal/api,
key,
```

<br/>
