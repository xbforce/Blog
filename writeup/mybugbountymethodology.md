
# My Bug Bounty methodology


This is how I work on bug bounty programs. Please let me know via my twitter if I should do anything else to make my methodology more effective.

<br/>

:heavy_check_mark: **Recon**

Gathering subdomains by using different tools and techniques like:
```
amass, subfinder, assetfinder, cert.sh, source codes
```

Check the results with curl (I hate screenshoters) and bookmark alive domains with 200 OK status codes on firefox.

Fuzz directories with ffuf based on needs.

Use dmitry, whatweb, google dork and github to gather more information about the target.

<br/>

:heavy_check_mark: **Google Dorking**
1. Dork for open redirection
2. Dork for different extensions like .py, .php, .asp, xml


<br/>

:heavy_check_mark: **Github Dorking**
1. Dork for api keys: api-key, api_key, api.key, apikey, access_token
2. Dork for password, secret, username

<br/>


:heavy_check_mark: **Broken Authentication**
1. Request a password reset link as ATTACKER, then change the email address and password as
! VICTIM, and after that try to reset the password with the link as ATTACKER.
2. Check if you can use password reset link after 24 hours.

<br/>


:heavy_check_mark: **Cross Site Scripting (XSS)**
1. Click on every function and intercept them with burp and copy URLs which contain parameters
! then use kooxss tool to find XSS.
2. Use waybackurl to gather URLs, then modify URLs and test them with kooxss.
++
$ cat target_domain.txt | waybackurl > wbu_target.com.txt
$ kooxss wbu_target.com.txt ATTACKER.com
++

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
3- Try it:
```http://169.254.169.254/latest/meta-data```

<br/>


:heavy_check_mark: **Web Cache Poisoning**
1. Filter proxy history on burpsuite to find "HIT" or "MISS" values.
- Use the followings:
```
X-Forwarded-Host: hello.com
X-Forwarded-Host: hello.com%22%2522%25%32%32
X-Forwarded-Host: hello.com">
X-Forwarded-Host: hello.com%0d%0a%09kzw
X-Host: hello.com
X-Forwarded-For: hello.com
X-Rewrite-Url: hello.com
X-Original-Url: hello.com
X-Requested-By: hello.com
X-Requested-With: hello.com
```

<br/>


:heavy_check_mark: **Cross Origin Resource Sharing (CORS)**
1- If the endpoint contains sensitive information try CORS:
- Put these Request headers to see if the reflects in the Response:
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
