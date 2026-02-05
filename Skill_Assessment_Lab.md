### &#x1F6A9; HTB Pentester Path <br>
### Web Information Gathering - Skill Assessment Lab <br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>

To complete the skills assessment, answer the questions below. You will need to apply a variety of skills learned in this module, including:

- Using `whois`
- Analysing `robots.txt`
- Performing subdomain bruteforcing
- Crawling and analysing results

Demonstrate your proficiency by effectively utilizing these techniques. Remember to add subdomains to your `hosts` file as you discover them.

---

IP:
83.136.250.64:45520

vHosts needed for these questions:
inlanefreight.htb


### Added ip and inlanefreight.htb to the /etc/hosts file

---

### TTP Question 1:
What is the IANA ID of the registrar of the inlanefreight.com domain?

	$ whois inlanefreight.com

&#x1F6A9; found **46--edit--**.

---
### TTP Question 2:
What http server software is powering the inlanefreight.htb site on the target system? Respond with the name of the software, not the version, e.g., Apache.

	$ curl -I inlanefreight.htb:56045
	
		HTTP/1.1 200 OK
		Server: nginx/1.26.1
		Date: Thu, 04 Sep 2025 06:58:33 GMT
		Content-Type: text/html
		Content-Length: 120
		Last-Modified: Thu, 01 Aug 2024 09:35:23 GMT
		Connection: keep-alive
		ETag: "66ab56db-78"
		Accept-Ranges: bytes

&#x1F6A9; found **ngi--edit--nx**.

---
### TTP Question 3:
What is the API key in the hidden admin directory that you have discovered on the target system?

Enumerated subdomains with ffuf and some crafted flags:

|   |   |
|---|---|
|**-u**|Target URL to send requests [](https://classroom.anir0y.in/post/tryhackme-ffuf/)|
|**-w**|Wordlist for subdomain/vhost fuzzing [](https://classroom.anir0y.in/post/tryhackme-ffuf/)|
|**-mc**|Match only selected status codes [](https://classroom.anir0y.in/post/tryhackme-ffuf/)|
|**-t**|Threads (parallel requests) [](https://codingo.com/posts/2020-08-29-everything-you-need-to-know-about-ffuf/)|
|**-H**|Custom Host header for vhost detection [](https://classroom.anir0y.in/post/tryhackme-ffuf/)|
|**-ac**|Autocalibrate for filtering responses [](https://codingo.com/posts/2020-08-29-everything-you-need-to-know-about-ffuf/)|


	$ ffuf -u http://inlanefreight.htb:56045 -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt -mc 200,403 -t 60 -H "Host: FUZZ.inlanefreight.htb" -ac
	
		
		        /'___\  /'___\           /'___\       
		       /\ \__/ /\ \__/  __  __  /\ \__/       
		       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
		        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
		         \ \_\   \ \_\  \ \____/  \ \_\       
		          \/_/    \/_/   \/___/    \/_/       
		
		       v2.1.0-dev
		________________________________________________
		
		 :: Method           : GET
		 :: URL              : http://inlanefreight.htb:56045
		 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt
		 :: Header           : Host: FUZZ.inlanefreight.htb
		 :: Follow redirects : false
		 :: Calibration      : true
		 :: Timeout          : 10
		 :: Threads          : 60
		 :: Matcher          : Response status: 200,403
		 
		 web1337                 [Status: 200, Size: 104, Words: 4, Lines: 2, Duration: 77ms]
		:: Progress: [114441/114441] :: Job [1/1] :: 777 req/sec :: Duration: [0:02:27] :: Errors: 0 ::


Found 200 msg on *web1337*, adding this domain to my hosts file before continuing:

```
sudo sh -c "echo '94.237.61.242 web1337.inlanefreight.htb' >> /etc/hosts"
```

Now try FUZZing the path after the port in the url, with the '/Web-Content/common.txt' Seclist:

	$ ffuf -u http://web1337.inlanefreight.htb:56045/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt -t 60
			
			        /'___\  /'___\           /'___\       
			       /\ \__/ /\ \__/  __  __  /\ \__/       
			       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
			        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
			         \ \_\   \ \_\  \ \____/  \ \_\       
			          \/_/    \/_/   \/___/    \/_/       
			
			       v2.1.0-dev
			________________________________________________
			
			 :: Method           : GET
			 :: URL              : http://web1337.inlanefreight.htb:35684/FUZZ
			 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt
			 :: Follow redirects : false
			 :: Calibration      : false
			 :: Timeout          : 10
			 :: Threads          : 60
			 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
			________________________________________________

		index.html              [Status: 200, Size: 104, Words: 4, Lines: 2, Duration: 76ms]
		robots.txt              [Status: 200, Size: 99, Words: 6, Lines: 6, Duration: 77ms]



Found two pages...lets curl the robots file:

	$ curl -i web1337.inlanefreight.htb:45520/robots.txt
	
		HTTP/1.1 200 OK
		Server: nginx/1.26.1
		Date: Thu, 04 Sep 2025 21:23:32 GMT
		Content-Type: text/plain
		Content-Length: 99
		Last-Modified: Thu, 01 Aug 2024 09:34:59 GMT
		Connection: keep-alive
		ETag: "66ab56c3-63"
		Accept-Ranges: bytes
		
		User-agent: *
		Allow: /index.html
		Allow: /index-2.html
		Allow: /index-3.html
		Disallow: /admin_h1dd3n

Found */admin_h1dd3n*, lets curl that path:

	$ curl -i web1337.inlanefreight.htb:45520/admin_h1dd3n/
		
		HTTP/1.1 200 OK
		Server: nginx/1.26.1
		Date: Thu, 04 Sep 2025 21:25:25 GMT
		Content-Type: text/html
		Content-Length: 255
		Last-Modified: Thu, 01 Aug 2024 09:35:23 GMT
		Connection: keep-alive
		ETag: "66ab56db-ff"
		Accept-Ranges: bytes
		
		<!DOCTYPE html><html><head><title>web1337 admin</title></head><body><h1>Welcome to web1337 admin site</h1><h2>The admin panel is currently under maintenance, but the API is still accessible with the key e963d863ee0e82ba7080fbf558ca0d3f</h2></body></html>

&#x1F6A9; found **e963d863ee0--edit--0fbf558ca0d3f** flag.


---

### TTP Question 4:
After crawling the inlanefreight.htb domain on the target system, what is the email address you have found? Respond with the full email, e.g., mail@inlanefreight.htb.

Tried ReconSpider on the web1337.inlanefreight.htb domain with no luck, so lets refuzz with ffuf:

	$ ffuf -u http://web1337.inlanefreight.htb:45520 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -mc 200,403 -t 60 -H "Host: FUZZ.web1337.inlanefreight.htb" -ac
		
		        /'___\  /'___\           /'___\       
		       /\ \__/ /\ \__/  __  __  /\ \__/       
		       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
		        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
		         \ \_\   \ \_\  \ \____/  \ \_\       
		          \/_/    \/_/   \/___/    \/_/       
		
		       v2.1.0-dev
		________________________________________________
		
		 :: Method           : GET
		 :: URL              : http://web1337.inlanefreight.htb:45520
		 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
		 :: Header           : Host: FUZZ.web1337.inlanefreight.htb
		 :: Follow redirects : false
		 :: Calibration      : true
		 :: Timeout          : 10
		 :: Threads          : 60
		 :: Matcher          : Response status: 200,403
		________________________________________________
		
		dev                     [Status: 200, Size: 123, Words: 5, Lines: 1, Duration: 76ms]

found dev, lets concat and add the new domain to the hosts file:

	83.136.250.64   dev.web1337.inlanefreight.htb

Now lets run ReconSpider again on the new domain:

	$ python3 ReconSpider.py http://dev.web1337.inlanefreight.htb:45520
	
		2025-09-04 16:52:41 [scrapy.utils.log] INFO: Scrapy 2.13.3 started (bot: scrapybot)
		2025-09-04 16:52:41 [scrapy.utils.log] INFO: Versions:
		{'lxml': '5.3.0',
		 'libxml2': '2.12.9',
		 'cssselect': '1.3.0',
		 'parsel': '1.10.0',
		 'w3lib': '2.3.1',
		 'Twisted': '25.5.0',
		 'Python': '3.11.2 (main, Nov 30 2024, 21:22:50) [GCC 12.2.0]',
		 'pyOpenSSL': '24.0.0 (OpenSSL 3.2.2 4 Jun 2024)',
		 'cryptography': '42.0.8',
		 'Platform': 'Linux-6.11+parrot-amd64-x86_64-with-glibc2.36'}
	-SNIP-

Remember ReconSpider outputs to Results.json in the CWD. Let's have a look-see:

	{
	    "emails": [
	        "1337testing@inlanefreight.htb"
	    ],
	    "links": [
	        "http://dev.web1337.inlanefreight.htb:45520/index-204.html",
	        "http://dev.web1337.inlanefreight.htb:45520/index-635.html",
	        "http://dev.web1337.inlanefreight.htb:45520/index-80.html",
	        "http://dev.web1337.inlanefreight.htb:45520/index-944.html",
	        "http://dev.web1337.inlanefreight.htb:45520/index-737.html",
	        "http://dev.web1337.inlanefreight.htb:45520/index-302.html",
	        
	        -SNIP-
	        
	        "http://dev.web1337.inlanefreight.htb:45520/index-785.html",
	        "http://dev.web1337.inlanefreight.htb:45520/index-166.html",
	        "http://dev.web1337.inlanefreight.htb:45520/index-631.html",
	        "http://dev.web1337.inlanefreight.htb:45520/index-585.html",
	        "http://dev.web1337.inlanefreight.htb:45520/index-798.html",
	    
	        "http://dev.web1337.inlanefreight.htb:45520/index-933.html",
	        "http://dev.web1337.inlanefreight.htb:45520/index-862.html",
	        "http://dev.web1337.inlanefreight.htb:45520/index-350.html"
	    ],
	    "external_files": [],
	    "js_files": [],
	    "form_fields": [],
	    "images": [],
	    "videos": [],
	    "audio": [],
	    "comments": [
	        "<!-- Remember to change the API key to ba988b835be4aa97d068941dc852ff33 -->"
	    ]
	}

&#x1F6A9; found email/flag **1337te--edit--g@inlanefreight.htb**

---

### TTP Question 5:
What is the API key the inlanefreight.htb developers will be changing too?

&#x1F6A9; found **ba988b835be4a--edit--dc852ff33** in ReconSpider output.
