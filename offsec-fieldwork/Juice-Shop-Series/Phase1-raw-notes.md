# RAW Notes

## Walking the main page
I opened the Juice Shop and was greeted with a cookie pop up. "This website uses fruit cookies to ensure you get the juiciest tracking experience."

I clicked the "but me wait" tab and was taken to a YouTube video   https://www.youtube.com/watch?v=9PnbKL3wuH4  "Sesame Street: Me Want It (But Me Wait)"

I then accepted the cookie to track my progress (presumably)

I opened the side panel and saw there is a scoreboard. Dev tools "Inspector" revealed link. I manually entered the scoreboard (/score-board)  

Flag triggered. "Find the carefully hidden 'Score Board' page."  

On this page I saw "17 challenges are unavailable on Docker due to security concerns or technical incompatibility!" There is also a coding challenge and an interactive hacking tutorial for this challenge.

I navigated back to the main page and began looking through the products. Some products have reviews with associated email addresses.

| Product | user | comment intel |
|---|---|---|
|Apple Juice | admin@juice-sh.op |
|Apple Pomace |  |Points to Request Recycling Box |
|Banana Juice | bender@juice-sh.op | Mentions Fry (Futurama) | 
|Best Juice Shop Salesman Artwork | stan@juice-sh.op |
|Carrot Juice | uvogin@juice-sh.op | Leetspeak comment 0 st4rs f0r 7h3 h0rr1bl3 s3cur17y (Hacker?) |
|Green Smoothie | jim@juice-sh.op | replicator reference (StarTrek) | 
|Juice Shop "Permafrost" 2020 Edition | mc.safesearch@juice-sh.op | Frozen song reference |
|OWASP Juice Shop Card (non-foil) | accountant@juice-sh.op | |
|OWASP Juice Shop Holographic Sticker | | jim & Bender ID confirmed |
|OWASP Juice Shop Iron-Ons (16pcs) | https://www.stickeryou.com/products/owasp-juice-shop/794 | |
|OWASP Juice Shop LEGO™ Tower | bjoern@owasp.org | /#/photo-wall |
|OWASP SSL Advanced Forensic Tool (O-Saft) | https://www.owasp.org/index.php/O-Saft | OWASP SSL advanced forensic tool / OWASP SSL audit for testers |
|OWASP Snakes and Ladders - Mobile Apps | https://steamcommunity.com/sharedfiles/filedetails/?id=1970691216 | |
|OWASP Snakes and Ladders - Web Applications | bjoern@owasp.org | https://steamcommunity.com/sharedfiles/filedetails/?id=1969196030 | 
|Pwning OWASP Juice Shop | morty@juice-sh.op | |

### Users Discovered

| username | Key words/phrases |
|---|---|
| accountant@juice-sh.op | |
| admin@juice-sh.op | |
| bender@juice-sh.op | **Fry** liked it too. Just when my opinion of humans couldn't get any lower...  Will put one on the **Planet Express** ship's bumper! |
| bjoern@owasp.org | Check out the /#/photo-wall for some impressions of the assembly process! |
| jim@juice-sh.op  | Fresh out of a **replicator** Looks spacy on **Bones'** new **tricorder!** Looks so much better on my **uniform** than the boring **Starfleet symbol**.|
| mc.safesearch@juice-sh.op | This thang would look phat on **Bobby's jacked fur coat!** |
| morty@juice-sh.op | Even more interesting than watching **Interdimensional Cable!** |
| stan@juice-sh.op | I'd **stand on my head** to make you a deal for this piece of art |
| uvogin@juice-sh.op | 0 st4rs f0r 7h3 h0rr1bl3 s3cur17y |

### OSINT Search for users
A simple search of the usernames and keywords above revealed the names associated with the accounts for several users and a video for mc.safesearch @ https://imvdb.com/video/mc-safesearch/protect-ya-passwordz "Protect Ya Passwordz (2014)"  
(**mcsafes creditials: MrN00dles / N3wPassword**)

| Username | Name | Additional info |
|---|---|---|
| accountant@juice-sh.op | | |
| admin@juice-sh.op | | |
| bender@juice-sh.op | Bender Bending Rodríguez | Employer: Planet Express |
| bjoern@owasp.org | 
| jim@juice-sh.op | James Tiberius Kirk | Employer: Starfleet |
| mc.safesearch@juice-sh.op |  | Discovered Passwords: MrN00dles / N3wPassword |
| morty@juice-sh.op | Mortimer Chauncey Smith (Sr.?) | |
| stan@juice-sh.op | | |
| uvogin@juice-sh.op | | Hacker|

### Pages Discovered
- login.php#/about
- login.php#/contact
- login.php#/forgot-password
- login.php#/login
- login.php#/photo-wall
- login.php#/register
- login.php#/score-board
- login.php#/search?q=
- /#recycle
- /ftp/legal.md (link from /about)

The legal link exposes the /ftp directory. I navigated to /ftp and discovered multiple files and a subdirectory **/ftp/quarantine** containing 4 malware urls.

Break Time

---

## /ftp files
1. **coupons_2013.md.bak** - 403 Error: Only .md and .pdf files are allowed!
2. **incident-support.kdbx** - keypass password safe (downloaded for brute force decryption)
3. **package.json.bak** - 403 Error: Only .md and .pdf files are allowed!
4. **acquisitions.md** - This document is **confidential!** Do not distribute!
5. **eastere.gg** - 403 Error: Only .md and .pdf files are allowed! (Google Desktop Gadget,Sega Game Gear,Gridgen,Koala Paint???)
6. **legal.md** - Legal Information & Terms of Service
7. **suspicious_errors.yml** - 403 Error: Only .md and .pdf files are allowed!
8. **announcement_encrypted.md** - encrypted but not protected
9. **encrypt.pyc** - 403 Error: Only .md and .pdf files are allowed! (compiled python file)
10. **package-lock.json.bak** - 403 Error: Only .md and .pdf files are allowed!

I opened the .md files, downloaded the incident-support.kdbx  
Flags Triggered.  
You successfully solved a challenge: Error Handling (Provoke an error that is neither very gracefully nor consistently handled.)  
You successfully solved a challenge: Confidential Document (Access a confidential document.)
## Login as McSafesearch
I logged in using user info discovered during user OSINT search.  
Login: mc.safesearch@juice-sh.op Password: **Mr. N00dles** > Access granted  
Side panel shows three new fields Complaints (**login.php#/complain**), Support Chat (**login.php#/chatbot**), & Delux Membership (**/#/deluxe-membership**)  

Flag Triggered.  
You successfully solved a challenge: Login MC SafeSearch (Log in with MC SafeSearch's original user credentials without applying SQL Injection or any other bypass.)

I opened files under this user.  
Order History, address, payment options, and digital wallet are empty. Recycling boxes can be purchased from here as well.  
Opening the Privacy policy triggered a flag  
Flag triggered. 
You successfully solved a challenge: Privacy Policy (Read our privacy policy.)
**/profile**

I changed McSafeSearch password to **Pwn3dH4rd**  
I changed McSafeSearch username to **UnsafeSearch**  

I navigated to Delux Membership (**/#/deluxe-membership**)  
Enjoy amazing benefits as a deluxe customer of OWASP Juice Shop. Check out what is included with your membership.
49¤  
Then on deluxe-membership I clicked the link **49¤ Become a member** > http://10.10.10.41:1001/#/payment/deluxe  
**/#/payment/deluxe**. Payment accepted by credit card, wallet, coupon code, or other.  
Note: As a deluxe member, you get access to exclusive deals and irresistible offers. Enjoy unrestricted purchase of your favourite products.  

**Complaint** http://10.10.10.41:1001/#/complain allows for file upload
I navigated to the chatbot and attempted code injection for coupons and digital assets (FAILED).  
Logged out as Unsafesearch AKA mc.safesearch 
Note: **/#/complain** is complaint page

## Logging in as admin (SQL injection)
email: ' or 1=1;--  
passwod: any  
Access granted. Flag triggered.  
You successfully solved a challenge: Login Admin (Log in with the administrator's user account.)  

Order History contains delivered and in transit orders (ex Order ID
#5267-5c57028d20c0b3f9).  
Reviews can be written from here.  
Admin address is **0815 Test Street, Test, Test, 4711**  
credit cards are on file (PCI attempted card number masked)  
Digital wallet (/#/wallet) is empty  
Basket has items in it (/#/basket)  
Photo-wall upload is available.  
Chatbot code injection attempted. (Failed)  

I finished manually walking the webpage.  
I consulted the /score-board to use as a guide for further targets.

## Challenge: DOM XSS
In the search bar i inputted "<iframe src="javascript:alert(`xss`)">."  
Flag triggered.  
You successfully solved a challenge: DOM XSS (Perform a DOM XSS attack with <iframe src="javascript:alert(`xss`)">.) (x2)  

## Challenge: Bonus Payload
In the search bar i inputted "<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/771984076&color=%23ff5500&auto_play=true&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe>"  
OWASP Juice Shop Jingle by username braimee. https://soundcloud.com/braimee  
Note: braimee name is Brian Johnson.

## Challenge: Mass Dispel
For the mass dispel flag I had to read the Juice Shop official documents.  
According to the documents, holding shift and closing one tab closes them all.

Flag triggered.  
You successfully solved a challenge: Mass Dispel (Close multiple "Challenge solved"-notifications in one go.)

Break

---
## Creating a user database
There were usernames (and a broken link) in the photo wall so I navigated there and wanted to log user names for reference.  
Logged in as admin (SQL injection)
Photo-wall picture shows bee haven (**/#/bee-haven**) by **evmrox**  
**bkimminich** also posted pictures  
I love going hiking here... **(© j0hNny)**  
My old workplace... **(© E=ma²)**  

Reading through the comments I realized a don't see an admin link.  
Manual fuzz admin (failed), administration (success)  

Flag triggered.  
You successfully solved a challenge: Admin Section (Access the administration section of the store.)  

### **User database discovered.**

***Users updated***  

| Username | Name | Additional info |
|---|---|---|
| accountant@juice-sh.op | | |
| admin@juice-sh.op | | |
| amy@juice-sh.op
| bender@juice-sh.op | Bender Bending Rodríguez | Employer: Planet Express |
| bjoern@owasp.org | | |
| ciso@juice-sh.op | | |
| demo | | |
| emma@juice-sh.op |  | username: E=ma²(?) |
| ethereum@juice-sh.op | | Customer feedback: Please send me the juicy chatbot NFT in my wallet at **/juicy-nft**  |
| J12934@juice-sh.op | | |
| jim@juice-sh.op | James Tiberius Kirk | Employer: Starfleet |
| john@juice-sh.op | | username: j0hNny(? ) |
| mc.safesearch@juice-sh.op |  | Known Passwords: MrN00dles / N3wPassword |
| morty@juice-sh.op | Mortimer Chauncey Smith (Sr.?) | |
| stan@juice-sh.op | | |
| support@juice-sh.op | | |
| testing@juice-sh.op | | |
| uvogin@juice-sh.op | | Hacker|
| wurstbrot@juice-sh.op | | |
| anon | | Support Team: Sorry, only order confirmation PDFs can be attached to complaints! (anonymous) |
| unknown | unknown |username: evmrox |

Break

---
## Enumeration
Running OWASP-ZAP
Spider discoveries  
/robots.txt - points to /ftp  
/sitemap.xml

~~Running FFUF - ffuf -u http://10.10.10.41:1001/FUZZ -w /home/pv/Wordlists/ultimate-discovery.txt -fc 404~~

Note: Move wordlists up a dir

Break

---

I navigated to the score-board for ideas on where to go next.

## Challenge: Exposed Metrics  
Find the endpoint that serves usage data to be scraped by a popular monitoring system.

I navigated to the documentation for Prometheus. https://prometheus.io/docs/introduction/overview/  
Under "First Steps" "Configuring Prometheus" **"Prometheus expects metrics to be available on targets on a path of /metrics."**  
I navigated to http://10.10.10.41:1001/metrics.  

Flag triggered.  
You successfully solved a challenge: Exposed Metrics (Find the endpoint that serves usage data to be scraped by a popular monitoring system.)

## Challenge: Missing Encoding.  
Retrieve the photo of Bjoern's cat in "melee combat-mode".  

I remember seeing a misconfigured picture entitled "who needs four legs (or something similar"  
I navigated to /photo-wall and saw the missing picture entitled "😼 #zatschi #whoneedsfourlegs".   
I opened developer tools (Inspector). Element = 😼 #zatschi #whoneedsfourlegs  

"img _ngcontent-ng-c1771354057="" class="image" src="assets/public/images/uploads/ᓚᘏᗢ-#zatschi-#whoneedsfourlegs-1572600969477.jpg" alt="😼 #zatschi #whoneedsfourlegs"

Hashtages (fragments) in a url source are likely to blame.  
I navigated to Cyberchef for URL encode/decode.

URL Decode returned: <"img _ngcontent-ng-c1771354057="" class="image" src="assets/public/images/uploads/ááá¢-#zatschi-#whoneedsfourlegs-1572600969477.jpg" alt="ð¼ #zatschi #whoneedsfourlegs">  

I encoded the file:  
%3C%22img%20_ngcontent-ng-c1771354057=%22%22%20class=%22image%22%20src=%22assets/public/images/uploads/%E1%93%9A%E1%98%8F%E1%97%A2-#zatschi-#whoneedsfourlegs-1572600969477.jpg%22%20alt=%22%F0%9F%98%BC%20#zatschi%20#whoneedsfourlegs%22%3E

I edited the url = failed

I removed unneeded information from the line and encoded all special characters.

assets/public/images/uploads/ᓚᘏᗢ-#zatschi-#whoneedsfourlegs-1572600969477.jpg

assets/public/images/uploads/%E1%93%9A%E1%98%8F%E1%97%A2-#zatschi-#whoneedsfourlegs-1572600969477.jpg

The cat is in the way. Breaking the code down further to #zatschi-#whoneedsfourlegs-1572600969477.jpg  

returns  
%23zatschi%2D%23whoneedsfourlegs%2D1572600969477%2Ejpg  
I entered "http://10.10.10.41:1001/assets/public/images/uploads/%E1%93%9A%E1%98%8F%E1%97%A2-%23zatschi-%23whoneedsfourlegs-1572600969477.jpg"  
Success (cat picture found).  

Flag triggered.  
You successfully solved a challenge: Missing Encoding (Retrieve the photo of Bjoern's cat in "melee combat-mode".)

## Challenge: Repetitive Registration.
Follow the DRY principle while registering a user.

I am not familiar with the DRY principle, so I searched "Dry principle registration" and navigated to https://en.wikipedia.org/wiki/Don%27t_repeat_yourself .  
The DRY principle is stated as "Every piece of knowledge must have a single, unambiguous, authoritative representation within a system". The principle has been formulated by Andy Hunt and Dave Thomas in their book The Pragmatic Programmer.  

I think i need to repeat "something" during a registration, or register twice using the same username and different passwords.  
I logged out as admin, and navigated to the account login > Not yet a customer? > registration page.  http://10.10.10.41:1001/#/register  

The user registration page asks for email, password, **repeat password**, security question, and answer. DRY I believe reffers to the repeat password.  

I started BurpSuite > Proxy > Intercept. I opened the Burp Browser. and attempted to navigate to http://10.10.10.41:1001/#/register. Nothing happened. I can see the GET request, but no response.  
Note: No response because I left Intercept on (UGH).

I attempted to register using  
Email - noway@fuggetaboutit.com  
Password - Testing123+  
Repeat password - none  
Security question  Last name of dentist when you were a teenager - Yankem  

Returned: Passwords do not match
 
So I entered the Password and Repeat password to Testing123+ , then I removed and replaced the last character from the Password field  
 
Email - noway@fuggetaboutit.com  
Password - Testing123?  
Repeat password - Testing123+  
Security question  Last name of dentist when you were a teenager - Yankem

Flag triggered.  
You successfully solved a challenge: Repetitive Registration (Follow the DRY principle while registering a user.)  
Note: There should be a new user added to the database.

## Challenge: Outdated Allowlist
Let us redirect you to one of our crypto currency addresses which are not promoted any longer.

I navigated to /juicy-nft and opened Developer tools > Debugger.  
I searched for redirect and found /redirect?to=https://blockchain.info/address/1AbKfgvw9psQ41NbLi8kufDQTezwG8DRZm

I was redirected to https://www.blockchain.com/explorer/addresses/btc/1AbKfgvw9psQ41NbLi8kufDQTezwG8DRZm  

On this page is Bitcoin wallet 1AbKf-8DRZm  
Bitcoin address 1AbKfgvw9psQ41NbLi8kufDQTezwG8DRZm  
Bitcoin Balance = 0.00005997• $5.61  

Flag triggered.  
You successfully solved a challenge: Outdated Allowlist (Let us redirect you to one of our crypto currency addresses which are not promoted any longer.)

## Challenge: Web3 Sandbox. 
Find an accidentally deployed code sandbox for writing smart contracts on the fly.
I added web3-sandbox to the end of the home url. http://10.10.10.41:1001/#/web3-sandbox

Flag triggered.  
You successfully solved a challenge: Web3 Sandbox (Find an accidentally deployed code sandbox for writing smart contracts on the fly.)

I opened the burp suite browser. Navigated to http://10.10.10.41:1001/#/contact .  
I  filled out the review. Lowest available rating is 1 star. I inspected the review through Burpsuite. I changed the value from 1 to 0 and forwarded the request.

Flag triggered.  
You successfully solved a challenge: Zero Stars (Give a devastating zero-star feedback to the store.)

## Challenge: Bully Chatbot. (Last 1 star flag)
Receive a coupon code from the support chatbot.  

I navigated back to http://10.10.10.41:1001/#/chatbot

Chatbot output:  Hi, I can't recognize you. Sign in to talk to Juicy 

I "logged in" as admin (' or 1=1;--)  
I asked for a coupon. Output unreadable. I can barely make out something about the network. As my lab has no external gateway, I restored the gateway. and requested a coupon multiple times.

Chatbot Output: Oooookay, if you promise to stop nagging me here's a 10% coupon code for you: pes[Ch7ZKp

Flag triggered.  
You successfully solved a challenge: Bully Chatbot (Receive a coupon code from the support chatbot.)

**All 1 star challenges completed.**

Note: Investigte O-Saft is an easy to use tool to show informations about SSL certificate and tests the SSL connection according given list of ciphers and various SSL configurations.  https://owasp.org/www-project-o-saft/
