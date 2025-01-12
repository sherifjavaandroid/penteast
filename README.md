# Nikto Web Server Scanner Guide

Nikto is a web server vulnerability scanner that checks for common issues, misconfigurations, and security flaws. Below are the steps to use Nikto for scanning a target, such as `https://makramstores.com`.

---

## **Basic Scan**
Perform a simple scan against the target:
```bash
nikto -host https://makramstores.com
```

---

## **Advanced Options**

### 1. **Specify a Port**
If the target uses a non-standard port:
```bash
nikto -host https://makramstores.com -port 443
```

### 2. **Force SSL Mode**
Ensure SSL is used (if not already specified in the URL):
```bash
nikto -host makramstores.com -ssl
```

### 3. **Save Output**
Save the results to a file in a desired format:
- **Plain text:**
  ```bash
  nikto -host https://makramstores.com -output makramstores_scan.txt
  ```
- **JSON:**
  ```bash
  nikto -host https://makramstores.com -output makramstores_scan.json -Format json
  ```

### 4. **Tuning Tests**
Run specific types of tests based on their category:
- Example: Scan for SQL injection, XSS, and misconfigurations:
  ```bash
  nikto -host https://makramstores.com -Tuning 492
  ```
- Include all tests except for file uploads:
  ```bash
  nikto -host https://makramstores.com -Tuning x0
  ```

### 5. **Pause Between Tests**
Add a pause between each request to avoid overwhelming the server:
```bash
nikto -host https://makramstores.com -Pause 2
```

### 6. **Use Proxy**
Route requests through a proxy:
```bash
nikto -host https://makramstores.com -useproxy http://proxyserver:8080
```

### 7. **Custom User-Agent**
Change the user-agent for the requests:
```bash
nikto -host https://makramstores.com -useragent "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"
```

### 8. **Check for 404 Custom Pages**
Specify HTTP status codes or strings to ignore as negative responses:
```bash
nikto -host https://makramstores.com -404code 302,301
```

### 9. **Virtual Host Header**
Use a specific virtual host for the requests:
```bash
nikto -host https://makramstores.com -vhost www.makramstores.com
```

### 10. **Timeout for Requests**
Set a timeout for each request (default is 10 seconds):
```bash
nikto -host https://makramstores.com -timeout 20
```

---

## **Comprehensive Scan**
To run a detailed scan with verbose output and save the results:
```bash
nikto -host https://makramstores.com -Display V -output makramstores_scan.txt
```

---

## **Notes**
1. **Permissions:** Ensure you have authorization to scan the target to avoid legal consequences.
2. **Intrusion Detection Systems (IDS):** Nikto scans may trigger alerts on IDS or firewalls.
3. **Interpret Results:** Review the output carefully to understand potential vulnerabilities and misconfigurations.

---

Let me know if you need further assistance or help analyzing the results!
