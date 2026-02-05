### &#x1F6A9; HTB Pentester Path <br>
### Web Information Gathering - Fingerprinting Lab <br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>
<br>

IP:
10.129.28.203

vHosts needed for these questions:

- `app.inlanefreight.local`
- `dev.inlanefreight.local`

add these to your /etc/hosts file with the target ip.
 
---

### TTP Question 1:
Determine the Apache version running on app.inlanefreight.local on the target system. (Format: 0.0.0)

	$ curl -I 10.129.28.203
	
		HTTP/1.1 200 OK
		Date: Tue, 02 Sep 2025 06:02:08 GMT
		Server: Apache/2.4.41 (Ubuntu)
		Last-Modified: Mon, 16 Aug 2021 18:15:18 GMT
		ETag: "b8-5c9b12f02857c"
		Accept-Ranges: bytes
		Content-Length: 184
		Vary: Accept-Encoding
		Content-Type: text/html

&#x1F6A9; found **2.4.--edit--**

---
### TTP Question 2:
 Which CMS is used on app.inlanefreight.local on the target system? Respond with the name only, e.g., WordPress.

Added:

	10.129.28.203    app.inlanefreight.local

to my /etc/hosts so the webpage would display correctly. <br>
&#x1F6A9; found default text in post linking to **Joom--edit--**.

---
### TTP Question 3:
On which operating system is the dev.inlanefreight.local webserver running in the target system? Respond with the name only, e.g., Debian.

&#x1F6A9; found **ubu--edit--u** from banner grab.
