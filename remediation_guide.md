# 🔒 DVWA Vulnerability Remediation Guide

**CloudExify Cybersecurity Internship 2026**  
**Project 3: Web Application Penetration Testing**

---

## 1. SQL Injection (SQLi)

### The Problem
User input is directly concatenated into SQL queries, allowing attackers to inject malicious SQL code.

### The Fix: Parameterized Queries

**❌ VULNERABLE (Never do this):**
```php
$query = "SELECT * FROM users WHERE id = '" . $_GET['id'] . "'";
$result = mysqli_query($conn, $query);
```

**✅ SECURE (Always do this):**
```php
// Use prepared statements with parameter binding
$stmt = $conn->prepare("SELECT * FROM users WHERE id = ?");
$stmt->bind_param("i", $_GET['id']);
$stmt->execute();
$result = $stmt->get_result();
```

**Python equivalent:**
```python
import sqlite3
conn = sqlite3.connect('database.db')
cursor = conn.cursor()

# SAFE - parameterized query
cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))
```

### Additional Defenses
- Use ORM frameworks (Django ORM, SQLAlchemy) that handle escaping automatically
- Implement input validation (whitelist approach)
- Use stored procedures where possible
- Apply principle of least privilege (database user should not have DROP permissions)
- Enable SQL injection detection in WAF

---

## 2. Cross-Site Scripting (XSS)

### The Problem
User input is displayed on the page without escaping, allowing JavaScript injection.

### The Fix: Output Encoding

**❌ VULNERABLE:**
```php
echo $_GET['name'];
```

**✅ SECURE:**
```php
// Escape HTML entities
echo htmlspecialchars($_GET['name'], ENT_QUOTES, 'UTF-8');
```

**Python (Flask/Jinja2):**
```python
from markupsafe import escape

@app.route('/user/<name>')
def user(name):
    # Jinja2 auto-escapes by default
    return render_template('user.html', name=name)
```

### Content Security Policy (CSP)
```http
Content-Security-Policy: default-src 'self'; script-src 'self'
```

### Additional Defenses
- Use modern frameworks that escape by default (React, Vue, Django templates)
- Implement CSP headers
- Validate input on server side (not just client side)
- Use HTTP-only cookies to prevent JavaScript cookie theft

---

## 3. Cross-Site Request Forgery (CSRF)

### The Problem
Attackers can forge requests that perform actions on behalf of authenticated users.

### The Fix: CSRF Tokens

**❌ VULNERABLE:**
```html
<form action="/change-password" method="POST">
  <input type="password" name="new_password">
  <button>Change Password</button>
</form>
```

**✅ SECURE:**
```html
<form action="/change-password" method="POST">
  <input type="hidden" name="csrf_token" value="random-generated-token">
  <input type="password" name="new_password">
  <button>Change Password</button>
</form>
```

**Python (Flask-WTF):**
```python
from flask_wtf import FlaskForm
from wtforms import StringField
from flask_wtf.csrf import CSRFProtect

csrf = CSRFProtect(app)

class ChangePasswordForm(FlaskForm):
    new_password = StringField('New Password')
```

### Additional Defenses
- SameSite cookie attribute: `Set-Cookie: session=abc123; SameSite=Strict`
- Validate Referer/Origin headers
- Require re-authentication for sensitive actions

---

## 4. File Upload Vulnerabilities

### The Problem
No validation allows attackers to upload executable files (PHP shells).

### The Fix: Strict Validation

**❌ VULNERABLE:**
```php
$target = "uploads/" . basename($_FILES["file"]["name"]);
move_uploaded_file($_FILES["file"]["tmp_name"], $target);
```

**✅ SECURE:**
```php
// 1. Whitelist allowed extensions
$allowed = ['jpg', 'jpeg', 'png', 'gif'];
$ext = strtolower(pathinfo($_FILES["file"]["name"], PATHINFO_EXTENSION));

if (!in_array($ext, $allowed)) {
    die("Invalid file type");
}

// 2. Validate MIME type
$finfo = finfo_open(FILEINFO_MIME_TYPE);
$mime = finfo_file($finfo, $_FILES["file"]["tmp_name"]);
$allowed_mimes = ['image/jpeg', 'image/png', 'image/gif'];

if (!in_array($mime, $allowed_mimes)) {
    die("Invalid MIME type");
}

// 3. Rename file to prevent execution
$new_name = uniqid() . '.' . $ext;
move_uploaded_file($_FILES["file"]["tmp_name"], "uploads/" . $new_name);

// 4. Store uploads outside web root if possible
// 5. Disable script execution in upload directory (.htaccess)
```

---

## 5. Authentication & Session Security

### The Problem
Weak authentication allows brute force, session hijacking, and credential stuffing.

### The Fix: Multi-Layer Defense

```php
// 1. Strong password hashing (bcrypt)
$hash = password_hash($password, PASSWORD_BCRYPT, ['cost' => 12]);

// 2. Verify with timing-safe comparison
if (password_verify($password, $hash)) {
    // Login successful
}

// 3. Session security
session_start();
ini_set('session.cookie_httponly', 1);
ini_set('session.cookie_secure', 1);
ini_set('session.cookie_samesite', 'Strict');
ini_set('session.use_strict_mode', 1);

// Regenerate ID after login
session_regenerate_id(true);

// 4. Rate limiting
if ($_SESSION['login_attempts'] > 3) {
    sleep(5); // Slow down brute force
}

// 5. Multi-factor authentication (MFA)
// Use TOTP (Google Authenticator) or hardware keys
```

---

## 6. Security Misconfiguration

### Common Issues & Fixes

| Issue | Fix |
|-------|-----|
| Default credentials | Change all defaults immediately |
| Error messages expose info | Use generic error messages |
| Directory listing enabled | Disable in web server config |
| Unnecessary services running | Disable unused services |
| Outdated software | Enable automatic security updates |
| Debug mode in production | Set `debug = false` |

---

## 7. Defense in Depth Checklist

- [ ] **Input Validation:** Validate all input (whitelist approach)
- [ ] **Output Encoding:** Escape all output based on context
- [ ] **Authentication:** Strong passwords, MFA, session security
- [ ] **Authorization:** Principle of least privilege
- [ ] **Cryptography:** Use strong algorithms, proper key management
- [ ] **Logging:** Log security events, monitor for anomalies
- [ ] **Error Handling:** Generic error messages, no stack traces
- [ ] **Rate Limiting:** Prevent brute force and DoS
- [ ] **Security Headers:** CSP, HSTS, X-Frame-Options, X-XSS-Protection
- [ ] **Regular Updates:** Patch management, dependency scanning
- [ ] **WAF:** Web Application Firewall for additional protection
- [ ] **Penetration Testing:** Regular security assessments

---

**Report Generated:** August 2026  
**Classification:** For CloudExify Internship Use Only
