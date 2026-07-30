# New Targets Assessment — Multi-Day Report
**Date:** 28 July 2026  
**Scope:** All 7 untested domain targets from `targets.md` + previously tested infrastructure hosts

---

## Complete Target Inventory

### Previously Tested (Days 2-3 Complete)

| Target | IP | Services | Status |
|--------|----|----------|--------|
| grow.proxiad.bg | 82.103.125.235 | nginx 1.30.2 — Grow@Proxiad portal | ✅ Complete |
| ddr.proxiad.bg | 82.103.125.239 | nginx 1.28.3 (Ubuntu) — GitLab CE | ✅ Complete |
| 45.156.140.97 | Skopje | Stateful firewall — all filtered | ✅ Complete |
| 45.156.140.99 | Skopje | Stateful firewall — all filtered | ✅ Complete |
| 212.36.12.178 | Plovdiv | ICMP responsive — TCP filtered | ✅ Complete |
| 213.145.118.178 | Plovdiv | ICMP responsive — TCP filtered | ✅ Complete |

### New Targets — Day 1-5 Assessment

| # | Target | IP | Hosting | HTTP | Tech Stack | Risk |
|---|--------|----|---------|------|------------|------|
| 1 | **see.proxiad.com** | 185.80.2.208 | superhosting.bg (dedicated) | 415 | Apache, cPanel/WHM (21 ports detected by masscan) | **High** |
| 2 | **extranet.proxiad.com** | 185.161.45.103 | proxiad-infra.dedie.ate.info | 200 | nginx 1.28.0, Java/Spring, Bootstrap, jQuery 3.7.1 | **Medium** |
| 3 | **successcard.proxiad.com** | 185.161.45.103 | Same as extranet | 302 → Azure AD | nginx 1.28.0, Java/Spring, **Azure AD OAuth2** | **Medium** |
| 4 | **proxiad.bg** | 172.67.162.156 | Cloudflare CDN | 520 Error | Cloudflare (origin unreachable) | **Low** |
| 5 | **trackrecord.proxiad.bg** | 104.21.10.69 | Cloudflare CDN | 200 | Cloudflare, "TrackRecord - Proxiad" app | **Low** |
| 6 | **proxiad.desk-buddy.com** | 104.21.19.175 | Cloudflare CDN | 302 → /lobby | Cloudflare, Java/Spring (JSESSIONID) | **Low** |
| 7 | **aihub.proxiad.bg** | 15.236.125.102 | AWS ALB (eu-west-3) | 403 | awselb/2.0, CNAME: ddr-staging-759100438.eu-west-3.elb.amazonaws.com | **Low** |

---

## Detailed Findings

### 1. see.proxiad.com — cPanel/WHM Server (HIGH RISK)

**Host:** `host-185-80-2-208.superhosting.bg`  
**IP:** 185.80.2.208

**Masscan (0-65535) — 21 open ports detected:**

| Port | Service | Port | Service |
|------|---------|------|---------|
| 21 | FTP | 2080 | cPanel (autodesk-nlm) |
| 25 | SMTP | 2082 | cPanel (infowave) |
| 26 | SMTP (alt) | 2083 | cPanel SSL (radsec) |
| 53 | DNS | 2086 | WHM (gnunet) |
| 80 | HTTP | 2087 | WHM SSL (eli) |
| 110 | POP3 | 2095 | cPanel Webmail (nbx-ser) |
| 143 | IMAP | 2096 | cPanel Webmail SSL (nbx-dir) |
| 443 | HTTPS | 1022 | cPanel (exp2) |
| 465 | SMTPS | 2079 | cPanel (idware-router) |
| 587 | SMTP Submission | 993 | IMAPS |
| 995 | POP3S | | |

**Nmap (-Pn):** All ports show as **filtered** (host blocks direct probes but masscan syn-ack responses indicate services are present)

**WhatWeb:** Apache, returns **415 Unsupported Media Type** on HTTPS

**Assessment:** This is a **cPanel/WHM shared hosting server** with full email stack (SMTP, POP3, IMAP), DNS, web, and management interfaces. The 415 error suggests the web service expects specific content types (API endpoint or configured for specific clients only).

**Risk:** HIGH — exposed management interfaces (cPanel/WHM), email services, DNS. Potential for:
- Brute force on cPanel/WHM login
- Email service abuse (open relay check needed)
- DNS zone transfer (if misconfigured)
- Known cPanel/WHM vulnerabilities

---

### 2. extranet.proxiad.com — Java/Spring Application (MEDIUM RISK)

**Host:** `proxiad-infra.dedie.ate.info`  
**IP:** 185.161.45.103

**Open Ports:**
- 80/tcp — HTTP (redirects to HTTPS)
- 443/tcp — HTTPS (nginx 1.28.0)

**Technology Stack:**
- nginx 1.28.0 (reverse proxy)
- Java/Spring Framework (JSESSIONID + XSRF-TOKEN cookies)
- Bootstrap (frontend framework)
- jQuery 3.7.1
- TLS: Wildcard `*.proxiad.com` (Let's Encrypt, valid to 2027-02-03)

**Security Headers:**
- X-Frame-Options: SAMEORIGIN
- X-Content-Type-Options: nosniff
- X-XSS-Protection: 0 (disabled)
- Cache-Control: no-cache, no-store

**Assessment:** Internal extranet portal with Java/Spring backend. The X-XSS-Protection being disabled (0) is notable.

**Risk:** MEDIUM — potential for:
- Spring Boot actuator endpoints (check /actuator, /health, /env)
- Session hijacking if HTTPS not enforced
- Authentication bypass testing

---

### 3. successcard.proxiad.com — Azure AD OAuth2 (MEDIUM RISK)

**IP:** 185.161.45.103 (same as extranet)  
**Same nginx 1.28.0 + Java/Spring stack**

**Key Finding — Azure AD OAuth2 Flow:**
```
302 Redirect: / → /oauth2/authorization/azure
  → https://login.microsoftonline.com/fc2be715-4a94-42dd-832d-9d7dbb73c754/oauth2/v2.0/authorize
```

**Exposed Azure AD Configuration:**
- **Tenant ID:** `fc2be715-4a94-42dd-832d-9d7dbb73c754`
- **Client ID:** `a23a1380-ab03-4ebd-b6b1-46410e032147`
- **Redirect URI:** `https://successcard.proxiad.com/login/oauth2/code/azure`
- **Scopes:** openid, profile, email

**Security Headers:**
- X-Frame-Options: DENY
- Strict-Transport-Security: max-age=31536000; includeSubDomains
- X-XSS-Protection: 0 (disabled)

**Assessment:** Employee success/recognition card application with Azure AD SSO. The exposed tenant and client IDs are standard for OAuth2 flows but could be used for:
- Phishing campaigns (crafted consent pages)
- Azure AD tenant enumeration
- OAuth2 misconfiguration testing

**Risk:** MEDIUM — Azure AD integration exposed, potential for:
- Consent phishing
- Tenant enumeration
- OAuth2 redirect URI validation testing

---

### 4-6. Cloudflare-Hosted Targets (LOW RISK)

| Target | IP | HTTP | Notes |
|--------|----|------|-------|
| **proxiad.bg** | 172.67.162.156 | 520 Error | Origin server unreachable through Cloudflare |
| **trackrecord.proxiad.bg** | 104.21.10.69 | 200 OK | "TrackRecord - Proxiad" — behind Cloudflare, origin unknown |
| **proxiad.desk-buddy.com** | 104.21.19.175 | 302 → /lobby | Java/Spring app (JSESSIONID), desk booking system |

**Assessment:** All three are behind Cloudflare CDN. Only the Cloudflare edge is visible — origin servers are protected. Cannot perform direct port scanning or vulnerability assessment.

**Risk:** LOW — Cloudflare provides DDoS protection, WAF, and origin IP masking. Would need origin IP discovery or Cloudflare bypass techniques.

---

### 7. aihub.proxiad.bg — AWS ALB (LOW RISK)

**CNAME:** `ddr-staging-759100438.eu-west-3.elb.amazonaws.com`  
**IP:** 15.236.125.102 (AWS ALB in eu-west-3)

**HTTP:** 403 Forbidden  
**Server:** awselb/2.0

**Assessment:** AWS Application Load Balancer with access restrictions. The CNAME suggests this is a **staging environment** (ddr-staging) related to the GitLab instance (ddr.proxiad.bg).

**Risk:** LOW — properly restricted. The staging environment naming convention could be useful for further discovery.

---

## Risk Classification Matrix

| Target | IP | Value | Risk Level | Key Concern |
|--------|----|-------|------------|-------------|
| see.proxiad.com | 185.80.2.208 | cPanel/WHM + email server | **HIGH** | Exposed management interfaces, email services |
| extranet.proxiad.com | 185.161.45.103 | Internal extranet portal | **Medium** | Java/Spring app, XSS protection disabled |
| successcard.proxiad.com | 185.161.45.103 | Azure AD OAuth2 app | **Medium** | Exposed tenant/client IDs, OAuth2 flow |
| proxiad.bg | 172.67.162.156 | Corporate website | **Low** | Cloudflare protected, origin unreachable |
| trackrecord.proxiad.bg | 104.21.10.69 | TrackRecord app | **Low** | Cloudflare protected |
| proxiad.desk-buddy.com | 104.21.19.175 | Desk booking system | **Low** | Cloudflare protected |
| aihub.proxiad.bg | 15.236.125.102 | AI Hub (staging) | **Low** | AWS ALB, 403 Forbidden |

---

## Evidence Files Created

| File | Target | Contents |
|------|--------|----------|
| `new-see-fullscan.json` | see.proxiad.com | Masscan 0-65535 — 21 open ports |
| `evidence/see-proxiad-service-v3.nmap` | see.proxiad.com | Nmap -Pn service scan (all filtered) |
| `evidence/see-proxiad-whatweb.txt` | see.proxiad.com | WhatWeb — Apache, 415 error |
| `new-extranet-fullscan.json` | extranet/successcard | Masscan 0-65535 — 0 ports (firewalled) |
| `evidence/extranet-service-scan.nmap` | extranet/successcard | Nmap — ports 80/443 open, nginx 1.28.0 |
| `evidence/extranet-whatweb.txt` | extranet.proxiad.com | WhatWeb — Java/Spring, Bootstrap, jQuery |
| `evidence/successcard-whatweb.txt` | successcard.proxiad.com | WhatWeb — Azure AD OAuth2 flow captured |
| `evidence/proxiad-bg-whatweb.txt` | proxiad.bg | WhatWeb — Cloudflare 520 error |
| `evidence/trackrecord-whatweb.txt` | trackrecord.proxiad.bg | WhatWeb — TrackRecord app |
| `evidence/desk-buddy-whatweb.txt` | desk-buddy.com | WhatWeb — Java/Spring, desk booking |
| `evidence/aihub-whatweb.txt` | aihub.proxiad.bg | WhatWeb — AWS ALB, 403 Forbidden |

---

## Recommendations for Next Steps

1. **see.proxiad.com (HIGH):** 
   - Test cPanel/WHM login pages (2083, 2087) for default credentials
   - Check FTP (21) for anonymous access
   - Verify SMTP (25, 587) is not an open relay
   - Attempt DNS zone transfer on port 53

2. **extranet/successcard (Medium):**
   - Probe Spring Boot actuator endpoints (/actuator, /health, /env, /beans)
   - Test for directory listing on common paths
   - Check OAuth2 redirect URI validation

3. **Cloudflare targets (Low):**
   - Attempt origin IP discovery via historical DNS, SSL cert transparency logs
   - Monitor for Cloudflare bypass opportunities

4. **aihub.proxiad.bg (Low):**
   - The staging environment naming (ddr-staging) suggests relationship to GitLab — monitor for exposure