
# My Bug Bounty methodology


Depends on the target and its scopes I may work 2 days to 10-15 days on the target.

This is how I work on bug bounty programs. Please let me know if I should do anything else to make my methodology more effective.

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

:heavy_check_mark: **Host Header Injection**

After gathering information I check the target for Host header injection.

I play with Host header to see if I can get any response on my server.

- e.g:
```
Host: target.com
Host: evil.com
```

OR
```
 Host: target.com
Host: evil.com
```

OR

```
Host: target.com
X-Host: evil.com
```

I use burp param miner and intruder to test different host headers and internal IPs or encoded localhost for SSRF.

<br/>

:heavy_check_mark: **Code review #webcache poisoning**

I open different pages and intercept traffics via burpsuite, then look at the codes to know the target better.

Check to see if there is any js page which I can use for web cache poisoning.

I also Look for comments and the backend language.

<br/>

:heavy_check_mark: **http request smuggling**

I try different techniques on the target for Http request smuggling, like the following:

```
POST / HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

5e
POST /404 HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length 15

x=1
0
``` 

<br/>

:heavy_check_mark: **server-side template injection**

I try to get error on different paramters to see if there is any server-side template injection vulnerability and to recognize the framework.

```
{{%= 3 * '5'}
```

<br/>

:heavy_check_mark: **Account takeover**

I work on login pages to takeover accounts or bypass 2FAs.

Create two accounts one as the attacker and one as the victim.

I intercept all traffics and look at the URLs, Requests and Responses carefully to see if there is anything suspicious.

<br/>

:heavy_check_mark: **CSRF**

Look at the headers and Requests to see if I can do CSRF attack.

<br/>

:heavy_check_mark: **SQL injection**

I use sqlmap to check sql injection vulnerabilities.

<br/>

:heavy_check_mark: **XSS**

Finally I put time on parameters and try to inject XSS payloads.

I use Inspect elements feature on firefox or chrome browsers. After injecting payloads I click on "Edit as HTML" to see if I was able to bypass special chars like:
```
< > ' " :
```
