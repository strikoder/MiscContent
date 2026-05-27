# Web Requests

**Module Link:** https://academy.hackthebox.com/app/module/35
**YouTube Video:** https://youtu.be/GLNL5QwYH1M

---

## DNS Resolution Chain

**Checking NSSwitch Configuration:**
```bash
cat /etc/nsswitch.conf
# files mdns4_minimal [NOTFOUND=return] dns
```
> Resolution order: local `/etc/hosts` → mDNS (`.local` domains, printers) → public DNS
> `[NOTFOUND=return]` stops `.local` queries from leaking to public DNS

**Checking Hosts File:**
```bash
cat /etc/hosts
```

**Checking Resolver Config:**
```bash
cat /etc/resolv.conf
# Symbolic link → /run/systemd/resolve/stub-resolv.conf
# nameserver 127.0.0.53  (localhost via systemd-resolved)
# OR: nameserver 8.8.8.8 (direct public DNS)
```
> `systemd-resolved` adds: DNS caching, VPN awareness, per-interface DNS — unlike pointing directly to a public DNS server

---

## curl Fundamentals

**Basic Requests:**
```bash
# Simple GET
curl https://app.hackthebox.com/machines/Silentium

# Verbose output (increase v's for more detail)
curl -v https://speed.hetzner.de
curl -vv https://speed.hetzner.de
curl -vvv https://speed.hetzner.de
```

**Downloading Files:**
```bash
# Save with original filename
curl -O https://speed.hetzner.de/100MB.bin

# Silent download (combine with jq for API responses)
curl -s -O https://speed.hetzner.de/100MB.bin
```

---

## curl POST Requests

```bash
# Form-encoded POST
curl -d "username=test&password=1234" https://httpbin.org/post

# JSON POST
curl -H "Content-Type: application/json" -d '{"name":"strikoder"}' https://httpbin.org/post

# Explicit method with form data
curl -X POST -d "username=admin&password=Password123" https://httpbin.org/post

# Explicit method with JSON
curl -X POST -H "Content-Type: application/json" -d '{"username":"strikoder","role":"admin"}' https://httpbin.org/post
```

---

## curl Headers & Inspection

```bash
# Fetch headers only (reveals Apache/Ubuntu version — useful for fingerprinting, 0xdf technique)
curl -I https://example.com

# Custom User-Agent (useful for log poisoning)
curl -s -A "Mozilla/5.0" -H "Accept: application/json" https://httpbin.org/get

# Skip TLS certificate verification
curl -k https://www.strikoder.com

# Silent install pipe (common in setup scripts)
curl -fsSL https://example.com/install.sh | bash

# Check HTTP status code only
curl -s -o /dev/null -w "%{http_code}\n" https://strikoder.com
```

---

## curl Cookies

```bash
# Send multiple cookies inline
curl -b "username=strikoder; role=admin" https://httpbin.org/cookies

# Read cookies from a file
curl -b cookies.txt https://httpbin.org/cookies

# Save cookies received from server to file
curl -c cookies.txt https://httpbin.org/cookies/set/session/strikoder

# Send session cookie (two equivalent methods)
curl -b "PHPSESSID=test123" https://httpbin.org/cookies
curl -H "Cookie: PHPSESSID=test123" https://httpbin.org/cookies
```
