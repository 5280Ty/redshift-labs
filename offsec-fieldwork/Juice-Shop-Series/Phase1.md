# Juice Shop Series
*Phase 1*

<p align="center">
<img src="https://github.com/5280Ty/5280Ty/blob/main/Score-board.png" alt="Redshift" width="900"/>
</p>

This assessment started as a straightforward black box test against OWASP Juice Shop. It didn’t stay that way. As soon as I got deeper into the application, the score board and the internal challenge system turned it into a hybrid pentest and CTF, powered almost entirely by what I found on my own. I kept my approach realistic: no guessing solutions, no relying on writeups. If it’s in here, I either discovered it manually or accidentally triggered it while testing attack paths.

I ran almost everything by hand. Only one request was modified in Burp, and that was to give a zero-star product review. Everything else came from manual enumeration, observation, and the application giving me more than it should have.

---

# 1. Scope and Rules of Engagement
This was a local lab engagement. My constraints:

- Treat it like a real black box pentest no external guides  
- Follow normal ethical boundaries  
- Solve what I naturally discover  
- Document thought process, not just outcomes

Target: OWASP Juice Shop, local instance  
Tools: Browser, DevTools, Notes. Burp Suite Community for one edited request.

---

# 2. Recon and Enumeration

My initial goal was simple: map out what existed without brute forcing or directory blasting. I stuck to visible navigation, contextual clues, and anything the frontend leaked.

## Pages Discovered (Manual Only)
These are the pages I found or interacted with directly:

- `/` (Home)
- `/login`
- `/register`
- `/contact`
- `/search`
- `/basket`
- `/address`
- `/account`
- `/logout`
- `/about`
- `/track-result`
- `/recycle`
- `/score-board`

If I didn’t see it in the UI or stumble into it naturally, it’s not listed.

No ffuf or automated fuzzing was used.

## User and OSINT Notes
I also gathered user references from the UI and challenge triggers. These weren’t the result of enumeration tools. The app leaks quite a bit through labels, fields, and workflow logic.

| Username Found | Related Info | Notes |
|----------------|--------------|-------|
| Several internal example users | Names and roles exposed throughout app, OSINT, and logic | Kept for appendix |
| Admin account | Visible through reviews and accessable through SQL injecftion | Details left for future insertion |
| MC Safe Search | OSINT revealed insecure password | Account takeover completed |


---

# 3. Exploitation Highlights

This section covers the challenges I solved through natural testing, not guesswork.

## 1-Star Challenges Solved

### 1. Score Board
Triggered when I manually entered  (/score-board) after seeing a reference to "the scoreboard" in the dev tools. 

### 2. & 3. Error Handling & Confidential Document 
Triggered when opening files discovered (/ftp).

### 4. Privacy Policy
Triggered under Mc Safe Search account while mapping the app (/profile)

### 5. DOM XSS
In the search bar I injected "<iframe src="javascript:alert(`xss`)"> .  

### 6. Bonus Payload
In the search bar i inputted "<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/771984076&color=%23ff5500&auto_play=true&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe>"  

### 7. Mass Dispel
Found in Juice Shop official documents.  

### 8. Admin Section
Manual fuzz /administration

### 9. Exposed Metrics  
Found in the documentation for Prometheus. https://prometheus.io/docs/introduction/overview/  

### 10. Missing Encoding
Discovered using developer tools (Inspector), encoded with Cyber Chef.

### 11. Repetitive Registration
Tampered with password and repeat password fields during registration

### 12. Outdated Allowlist
Discovered redirect using Developer tools (Debugger).

### 13. Web3 Sandbox
Manual Fuzz specifically for Web3 Sandbox

### 14. Zero Stars
Exploited using Burp Intercepter to tamper with the packet

### 15. Bully Chatbot
I demanded a coupon repetedly (Then apologized)


## Accidental Multi‑Star Challenge Solves  
These weren’t intentional, but they happened while exploring real app behavior:

### 1. Login Admin (2 stars)
Triggered when I authenticated as the admin user through unintended access behavior found during normal testing flow.

### 2. Admin Section (2 stars)
Unlocked by navigating into parts of the admin area after gaining the above access.

### 3. Login MC SafeSearch (2 stars)
Triggered when I authenticated as the “MC SafeSearch” account through in‑app logic exposed during exploration.

All three were byproducts of looking for broken access control issues.

---

# 4. Notable Exploits

## 1. Zero‑Star Review Submission  
**Technique:** Burp Suite request modification  
**Why:** The UI enforces star minimums. The backend didn’t.  
**Outcome:** Server accepted a rating outside the intended bounds.  
**Impact:** Validation bypass.  
**Thoughts:** Classic frontend trust issue.

This was the only point where I used Burp. Everything else was done straight through the browser.

## 2. Login MC SafeSearch
**Technique:** OSINT  
**Why:** Search for user information lead to credential leak via video.  
**Outcome:** Account breached  
**Impact:** Account takeover and info change completed.  
**Thoughts:** No PII or credit information found. Possible honeypot.

---

# 5. Methodology Notes

My approach stayed consistent:

1. Identify visible functionality  
2. Poke at inputs and look for weak assumptions  
3. Pay attention to odd behavior  
4. Follow anything that feels “off”  
5. If the app reacts strangely, push harder  
6. If something triggers a challenge, take note but stay focused on the pentest.

This kept the engagement realistic even though Juice Shop’s gamified elements kept trying to steal the spotlight.

---

# 6. Lessons Learned

- Manual testing catches logic issues automated tools skip  
- Even simple UI interactions can leak user and role data  
- Access control flaws often reveal themselves if you just follow weird behavior instead of jumping to payloads  
- Juice Shop mixes web pentesting and CTF logic, but a disciplined approach stops it from becoming a guessing game  
- Keeping Burp out of the picture except where necessary forces better observational habits

---

# 7. Appendix

## OSINT Table
| Username | Name | Additional info |
|---|---|---|
| accountant@juice-sh.op | | |
| admin@juice-sh.op | | access via SQL ' or 1=1;-- |
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
| anon | | Support Team? Forgotten username? |
| unknown | unknown |username: evmrox (dewormer) |

## ## Pages Discovered (Manual Only)
These are the pages I found or interacted with directly:

| Type Path | Extension Context | Notes |
|---|---|---|
| Endpoint | /score-board  | Found via Dev Tools Inspector. |
| Endpoint | /#recycle | Found via product review (Apple Pomace). |
| Directory | /ftp/ | Root of the exposed file directory. |
| File | /ftp/legal.md | Initial link discovered from the /about page. |
| File | /ftp/coupons_2013.md.bak | Exposed file (403 error due to .bak extension). |
| File | /ftp/incident-support.kdbx | Exposed KeyPass password safe file (downloaded). |
| File | /ftp/package.json.bak | Exposed file (403 error due to .bak extension). |
| File | /ftp/acquisitions.md | Confidential Document (flag triggered). |
| File | /ftp/eastere.gg | Exposed file (403 error due to non-.md or .pdf extension). |
| File | /ftp/suspicious_errors.yml | Exposed file (403 error due to non-.md or .pdf extension). |
| File | /ftp/announcement_encrypted.md | Exposed file (encrypted content). |
| File | /ftp/encrypt.pyc | Exposed file (403 error due to non-.md or .pdf extension). |
| File | /ftp/package-lock.json.bak | Exposed file (403 error due to .bak extension). |
| Directory | /ftp/quarantine/ | Exposed subdirectory containing malware URLs. |
| Endpoint | /#/about | URL segment for the About page. |
| Endpoint | /#/contact | URL segment for the Contact page. |
| Endpoint | /#/forgot-password | URL segment for password recovery. |
| Endpoint | /#/login | URL segment for the login page. |
| Endpoint | /#/photo-wall | URL segment for the Photo Wall page (discovered via reviews). |
| Endpoint | /#/register | URL segment for new user registration. |
| Endpoint | /#/search?q= | URL segment for search function (vulnerable to XSS). |
| Endpoint | /#/complain | URL segment for customer complaints (file upload available). |
| Endpoint | /#/chatbot | URL segment for the support chatbot. |
| Endpoint | /#/deluxe-membership | URL segment for the deluxe membership page. |
| Endpoint | /#/profile | URL segment for the user profile. |
| Endpoint | /#/basket | URL segment for the shopping cart. |
| Endpoint | /#/wallet | URL segment for the digital wallet. |
| Endpoint | /#/administration | Admin Section (found via manual fuzzing). |
| Endpoint | /robots.txt | Found via OWASP-ZAP (points to /ftp). |
| Endpoint | /sitemap.xml | Found via OWASP-ZAP. |
| Endpoint | /metrics | Exposed Metrics endpoint (Prometheus standard). |
| Asset Path | assets/public/images/uploads/ | URL path used for the Missing Encoding challenge image. |
| Endpoint | /redirect?to= | Endpoint used for the Outdated Allowlist challenge. |
| Endpoint | /#/web3-sandbox | Web3 Sandbox endpoint (found via direct navigation). |

## Payloads
### 1. DOM XSS 
```
<iframe src="javascript:alert(`xss`)">
```

### 2. Bonus Payload 
```
<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/771984076&color=%23ff5500&auto_play=true&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe>
```
### 3. SQL Injection
```SQL
' or 1=1;--
```

---

If you’re reading this as part of the full six‑project series, this walkthrough represents the starting point: a target, a clean methodology, and a set of findings based entirely on what I observed and exploited myself.
