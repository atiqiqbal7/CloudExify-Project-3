# 🌐 CloudExify Project 3 — Web Application Penetration Testing

**Internship:** CloudExify Cybersecurity Summer 2026  
**Project:** Web Application Penetration Testing  
**Difficulty:** Advanced  
**Status:** ✅ Completed

---

## 📋 Overview

This project demonstrates comprehensive web application security testing against **DVWA (Damn Vulnerable Web Application)**. The assessment covers the OWASP Top 10 vulnerabilities including SQL Injection, XSS, CSRF, and file upload vulnerabilities using Burp Suite and manual testing techniques.

---

## 📁 Files in This Repository

| File | Description |
|------|-------------|
| `dvwa_exploitation_guide.py` | Complete Python guide for testing DVWA vulnerabilities |
| `remediation_guide.md` | How to fix each vulnerability (secure code examples) |
| `burp_suite_guide.md` | Burp Suite setup, configuration, and usage |
| `web_pentest_report.pdf` | Professional vulnerability assessment report |
| `screenshots/` | Evidence of all exploitation attempts |
| `README.md` | This documentation file |

---

## 🛠️ Requirements

```bash
# DVWA Setup (on Kali Linux)
sudo apt update
sudo apt install apache2 mysql-server php libapache2-mod-php php-mysql

# Python dependencies
pip install requests

# Burp Suite
# Download from: https://portswigger.net/burp/communitydownload
# Or use pre-installed version on Kali: burpsuite
```

---

## 🚀 Quick Start

### 1. Install DVWA

```bash
# Clone DVWA repository
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
sudo mv DVWA dvwa

# Configure database
sudo mysql -u root
# In MySQL:
# CREATE DATABASE dvwa;
# CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'p@ssw0rd';
# GRANT ALL ON dvwa.* TO 'dvwa'@'localhost';
# FLUSH PRIVILEGES; EXIT;

# Copy and edit config
sudo cp /var/www/html/dvwa/config/config.inc.php.dist \
     /var/www/html/dvwa/config/config.inc.php
sudo nano /var/www/html/dvwa/config/config.inc.php
# Set database credentials

# Set permissions
sudo chown -R www-data:www-data /var/www/html/dvwa
sudo chmod -R 755 /var/www/html/dvwa

# Start services
sudo systemctl start apache2
sudo systemctl start mysql

# Access: http://localhost/dvwa
# Login: admin / password
# Click "Create / Reset Database" on setup page
```

### 2. Configure Burp Suite

1. Start Burp Suite: `burpsuite`
2. Configure Firefox proxy: `127.0.0.1:8080`
3. Import Burp CA certificate in Firefox
4. Intercept is ON — ready to test!

### 3. Run Exploitation Guide

```bash
python3 dvwa_exploitation_guide.py
```

This script documents all testing techniques and payloads.

---

## 🎯 Vulnerabilities Tested

### 1. SQL Injection (SQLi)

| Level | Defense | Bypass Technique |
|-------|---------|-----------------|
| **Low** | None | Direct string concatenation |
| **Medium** | `mysqli_real_escape_string()` | Numeric payloads (no quotes) |
| **High** | LIMIT 1, more filters | Blind SQLi, time-based attacks |
| **Impossible** | Prepared statements | NOT VULNERABLE ✅ |

**Payloads tested:**
```sql
' OR '1'='1                    -- Authentication bypass
' UNION SELECT user,password FROM users --  -- Data extraction
1 OR 1=1                        -- Numeric bypass (medium)
1' AND SLEEP(5) #              -- Time-based blind SQLi
```

### 2. Cross-Site Scripting (XSS)

| Type | Low Level | Medium Level | High Level |
|------|-----------|--------------|------------|
| **Reflected** | `<script>alert('XSS')</script>` | `<ScRiPt>alert('XSS')</ScRiPt>` | `<img src=x onerror=alert('XSS')>` |
| **Stored** | Persistent script injection | Filtered `<script>` tags | More strict filtering |

**Impact:** Session theft, phishing, malware distribution, keylogging

### 3. Cross-Site Request Forgery (CSRF)

**Vulnerability:** No CSRF token validation on password change

**Attack:**
```html
<img src="http://localhost/dvwa/vulnerabilities/csrf/?
     password_new=hacked&password_conf=hacked&Change=Change">
```

**Fix:** Add CSRF tokens to all state-changing forms

### 4. File Upload

**Vulnerability:** No file type validation

**Exploit:** Upload PHP shell for remote code execution

**Fix:** Whitelist extensions, validate MIME type, rename files, store outside web root

### 5. Authentication Bypass

**Techniques tested:**
- SQL Injection in login form
- Brute force with wordlists
- Session hijacking via XSS

---

## 🧪 Testing Checklist

| Test | Expected Result | Status |
|------|----------------|--------|
| DVWA setup | Login successful | ✅ |
| Burp proxy intercept | Requests visible | ✅ |
| SQL injection (low) | Bypass authentication | ✅ |
| SQL injection (medium) | Extract data | ✅ |
| Reflected XSS | JavaScript executes | ✅ |
| Stored XSS | Persistent script | ✅ |
| CSRF | Password changed without token | ✅ |
| File upload | PHP shell uploaded | ✅ |
| Auth bypass | Login without credentials | ✅ |
| Report generation | Professional document | ✅ |

---

## 🛡️ Remediation Summary

| Vulnerability | Priority | Fix |
|--------------|----------|-----|
| SQL Injection | CRITICAL | Parameterized queries |
| Stored XSS | CRITICAL | Output encoding + CSP |
| Reflected XSS | HIGH | htmlspecialchars() |
| File Upload | CRITICAL | Whitelist + MIME validation |
| CSRF | MEDIUM | CSRF tokens + SameSite cookies |
| Auth Bypass | HIGH | Strong passwords + MFA + rate limiting |

See `remediation_guide.md` for complete secure code examples.

---

## 📸 Screenshots Evidence

All screenshots are in the `screenshots/` folder:

| Screenshot | Description |
|-----------|-------------|
| `p3-01-dvwa-homepage.png` | DVWA login and homepage |
| `p3-02-security-level.png` | Security level configuration |
| `p3-03-sqli-low.png` | SQL injection on low security |
| `p3-04-sqli-medium.png` | SQL injection on medium security |
| `p3-05-xss-reflected.png` | Reflected XSS alert box |
| `p3-06-xss-stored.png` | Stored XSS in guestbook |
| `p3-07-csrf-payload.png` | CSRF attack payload |
| `p3-08-file-upload.png` | Malicious file upload |
| `p3-09-burp-intercept.png` | Burp Suite proxy intercept |
| `p3-10-burp-repeater.png` | Burp Repeater testing |
| `p3-11-remediation-code.png` | Secure code examples |

---

## 📚 What I Learned

1. **OWASP Top 10** are the most critical web vulnerabilities — every developer must know them
2. **SQL Injection** is still #1 because developers use string concatenation instead of parameterized queries
3. **XSS** is dangerous because it runs in the victim's browser, bypassing server-side defenses
4. **CSRF** exploits the browser's automatic cookie sending — tokens are essential
5. **File uploads** are dangerous if not properly validated — never trust file extensions
6. **Burp Suite** is the industry standard for web penetration testing
7. **Defense in depth** — multiple layers of security are needed, not just one

---

## 🔗 Related Projects

- **Project 1:** Network Penetration Testing — [CloudExify-Project-1](https://github.com/atiqiqbal7/CloudExify-Project-1)
- **Project 2:** Cryptography & Password Security — [CloudExify-Project-2](https://github.com/atiqiqbal7/CloudExify-Project-2)

---

**Report Generated:** August 2026  
**Classification:** For CloudExify Internship Use Only
