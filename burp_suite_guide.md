# 🛠️ Burp Suite Setup & Usage Guide

**CloudExify Cybersecurity Internship 2026**  
**Project 3: Web Application Penetration Testing**

---

## 1. Installation

### Download Burp Suite Community (Free)
1. Go to: https://portswigger.net/burp/communitydownload
2. Download for Linux (`.sh` installer)
3. Or use Kali's pre-installed version:
```bash
burpsuite
```

---

## 2. Firefox Proxy Configuration

### Step 1: Open Firefox Settings
- Go to: `about:preferences`
- Scroll down to **Network Settings**
- Click **Settings**

### Step 2: Configure Manual Proxy
```
HTTP Proxy: 127.0.0.1
Port: 8080
```
- Check **"Use this proxy server for all protocols"**
- Click **OK**

### Step 3: Install Burp CA Certificate
1. In Burp Suite: **Proxy → Options → Import/export CA certificate**
2. Export certificate to file
3. In Firefox: `about:preferences#privacy`
4. Click **View Certificates → Authorities → Import**
5. Select Burp CA file, check **"Trust this CA to identify websites"**

---

## 3. Burp Suite Interface

### Main Tabs

| Tab | Purpose | You'll Use It For |
|-----|---------|-------------------|
| **Proxy** | Intercept and modify requests | Testing SQLi, XSS payloads |
| **Repeater** | Modify and resend requests | Fine-tuning attacks |
| **Intruder** | Automated attacks | Brute force, fuzzing |
| **Scanner** | Automated vulnerability scan | Finding issues (Pro only) |
| **Decoder** | Encode/decode data | URL encoding, Base64 |
| **Comparer** | Compare two requests/responses | Finding differences |
| **Sequencer** | Analyze session tokens | Testing randomness |

---

## 4. Testing Workflow

### Step 1: Intercept a Request
1. In Burp: Go to **Proxy → Intercept**
2. Click **"Intercept is on"**
3. In Firefox: Navigate to DVWA (http://localhost/dvwa)
4. The request appears in Burp — you can modify it before sending

### Step 2: Send to Repeater
1. Right-click the intercepted request
2. Click **"Send to Repeater"**
3. Go to **Repeater** tab
4. Modify parameters and click **"Go"** to resend

### Step 3: Test SQL Injection
```
Original: id=1
Test: id=1' OR '1'='1
Test: id=1 UNION SELECT user,password FROM users--
```

### Step 4: Test XSS
```
Original: name=John
Test: name=<script>alert('XSS')</script>
Test: name=<img src=x onerror=alert('XSS')>
```

---

## 5. Intruder Attack Types

### Sniper Attack
- One payload set
- Tests one position at a time
- Use for: Single parameter fuzzing

### Battering Ram
- One payload set
- Tests all positions with same payload
- Use for: Testing same value in multiple fields

### Pitchfork
- Multiple payload sets
- Tests positions in parallel
- Use for: Username + password combinations

### Cluster Bomb
- Multiple payload sets
- Tests all combinations
- Use for: Brute force username AND password

---

## 6. Common Use Cases

### Brute Force Login
1. Intercept login request
2. Send to Intruder
3. Set payload positions on username and password
4. Load wordlists
5. Start attack

### Fuzzing Parameters
1. Intercept request with parameter
2. Send to Intruder
3. Set payload position on parameter value
4. Load fuzzing wordlist
5. Start attack

### Testing CSRF Protection
1. Intercept state-changing request (password change)
2. Check for CSRF token in request
3. Remove token and resend
4. If successful → CSRF vulnerability

---

## 7. Tips & Tricks

| Tip | Why It Helps |
|-----|-------------|
| Use **Ctrl+Shift+T** | Send to Repeater quickly |
| Enable **URL encoding** | Prevents payload corruption |
| Check **Response** tab | See if exploit worked |
| Use **Match/Replace** | Automatically modify all requests |
| Save **Project** | Don't lose your work |

---

## 8. Common Issues

| Problem | Solution |
|---------|----------|
| "Certificate error" in Firefox | Import Burp CA certificate |
| "Intercept not working" | Check proxy settings match |
| "Request timeout" | Check Burp is running |
| "Too many requests" | Add delays in Intruder |

---

**Report Generated:** August 2026  
**Classification:** For CloudExify Internship Use Only
