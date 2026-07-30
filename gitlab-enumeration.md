# GitLab Enumeration — ddr.proxiad.bg (82.103.125.239)
**Date:** 27 July 2026  
**Objective:** Enumerate self-managed GitLab instance for information disclosure, leaked credentials, and exposed CI/CD configuration

---

## Summary

| Attribute | Value |
|-----------|-------|
| **Hostname** | ddr.proxiad.bg |
| **IP** | 82.103.125.239 |
| **Service** | GitLab Community Edition (self-managed) |
| **Web Server** | nginx 1.28.3 (Ubuntu) |
| **TLS** | Let's Encrypt (valid until 2026-09-09) |
| **Registration** | Disabled (`allow_signup: false`) |
| **Authentication** | LDAP + OmniAuth configured |
| **Public Projects** | None found |
| **Public Groups** | None found |
| **Public Snippets** | None found |
| **API Access** | Requires authentication (401/403) |
| **Version** | Not exposed (hardened) |

---

## Endpoint Accessibility

| Endpoint | HTTP Status | Accessible? | Notes |
|----------|-------------|-------------|-------|
| `/` | 302 → `/users/sign_in` | ✅ Public | Redirects to login |
| `/users/sign_in` | 200 | ✅ Public | Login page with CSRF token |
| `/users/sign_up` | 302 → login | ❌ Disabled | Registration not allowed |
| `/users/password` | 301 | ✅ Public | Password reset page (potential user enumeration) |
| `/explore` | 200 | ✅ Public | No public projects listed |
| `/explore/projects` | 200 | ✅ Public | Empty (no public projects) |
| `/explore/groups` | 200 | ✅ Public | Empty (no public groups) |
| `/explore/snippets` | 200 | ✅ Public | Empty (no public snippets) |
| `/public` | 200 | ✅ Public | Empty |
| `/help` | 200 | ✅ Public | Help documentation |
| `/help/instance_configuration` | 200 | ✅ Public | Instance settings (SSH keys, limits) |
| `/api/v4/projects` | 200 | ✅ Public | Returns `[]` (no public projects) |
| `/api/v4/groups` | 200 | ✅ Public | Returns `[]` (no public groups) |
| `/api/v4/users` | 403 | ❌ Restricted | Forbidden |
| `/api/v4/version` | 401 | ❌ Auth required | Unauthorized |
| `/api/v4/snippets/public` | 401 | ❌ Auth required | Unauthorized |
| `/-/health` | 404 | ❌ Not available | Health endpoint disabled |
| `/-/user` | 302 | ❌ Auth required | Redirects to login |
| `/-/cable` | — | ✅ Public | WebSocket (ActionCable) endpoint active |
| `/search/opensearch.xml` | 200 | ✅ Public | OpenSearch description |

---

## Security Findings

### 1. LDAP Authentication Configured
The login page references LDAP authentication (`ldap` and `omniauth` detected in page source). This indicates:
- Integration with Active Directory or OpenLDAP
- Potential for LDAP injection testing (requires authenticated session)
- Possible user enumeration via LDAP search

### 2. SSH Host Keys Exposed
The instance configuration page exposes SSH host key fingerprints:

| Algorithm | MD5 | SHA256 |
|-----------|-----|--------|
| ECDSA | `47:d3:ed:76:d4:78:b4:c3:92:26:bb:25:ee:5f:23:69` | `SHA256:qXAtJYTuxszx7gXQmupu1Y9Rq4RzlNKOrpxmSk3xfCA` |
| ED25519 | `f5:36:4b:c3:b7:27:8a:20:22:f3:e5:bf:05:24:da:d1` | `SHA256:ClhsYrs6tEYGVmuR30aWsbpKcjrjflkgTSRL1hklzzM` |
| RSA | `27:42:fb:0d:0b:8e:54:86:0e:94:2c:de:32:c0:8d:07` | `SHA256:nWij00FqBUMgSNbsGcFLaRpjHKvboNOCXunihSSrn9Y` |

### 3. WebSocket Endpoint Active
ActionCable WebSocket at `/-/cable` is publicly accessible. This could be used for:
- Real-time event monitoring
- Potential WebSocket-based attacks if vulnerabilities exist
- Connection fingerprinting

### 4. OpenSearch Available
OpenSearch description at `/search/opensearch.xml` — search functionality is exposed even without authentication.

### 5. CSRF Protection
All forms include CSRF tokens — standard GitLab security.

### 6. Security Headers
- `Strict-Transport-Security: max-age=63072000` (2 years HSTS)
- `X-Frame-Options: SAMEORIGIN`
- `X-Content-Type-Options: nosniff`
- `X-XSS-Protection: 1; mode=block`
- `Content-Security-Policy` configured
- `Referrer-Policy: strict-origin-when-cross-origin`

### 7. No Public Projects
The instance appears to have no public projects, groups, or snippets. All content requires authentication.

---

## Risk Assessment

| Finding | Severity | Exploitability | Notes |
|---------|----------|----------------|-------|
| LDAP authentication configured | **Medium** | Low (requires auth) | Potential for AD user enumeration if credentials obtained |
| SSH host keys exposed | **Low** | Very Low | Standard GitLab behavior; useful for host fingerprinting |
| WebSocket endpoint public | **Low** | Low | Requires authenticated session for meaningful interaction |
| No public projects | **None** | N/A | Instance properly configured to restrict public access |
| Registration disabled | **None** | N/A | Prevents unauthorized account creation |
| Version not exposed | **None** | N/A | Security hardening in place |

---

## Recommendations for Week 2

1. **LDAP integration** — If credentials are obtained (e.g., via phishing or other means), test LDAP injection and user enumeration
2. **Password reset page** — Monitor for user enumeration vulnerabilities (timing attacks, error message differences)
3. **GitLab API** — If an access token is discovered, use it to enumerate projects, users, and CI/CD variables
4. **CI/CD pipeline review** — If access is gained, check for exposed secrets in pipeline configurations
5. **Runner configuration** — Check if shared runners are enabled (potential for pipeline execution attacks)

---

## Evidence Files

| File | Description |
|------|-------------|
| `evidence/sofia-239-gitlab-login.html` | Login page HTML (CSRF token, Webpack bundles) |
| `evidence/sofia-239-gitlab-version.html` | Help page HTML (GitLab CE confirmed) |
| `evidence/sofia-239-gitlab-config.html` | Instance configuration (SSH keys, limits, settings) |
| `evidence/sofia-239-headers.txt` | HTTP response headers |
| `evidence/sofia-239-whatweb.txt` | WhatWeb technology fingerprinting |
| `evidence/sofia-239-cert.txt` | TLS certificate details |
| `evidence/sofia-service-scan.nmap` | Nmap service version detection |