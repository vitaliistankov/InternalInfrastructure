# 📊 Weekly Report #1 — Reconnaissance & Attack Surface Mapping
**Week 1:** Days 2-5 | 20-28 July 2026  
**Engagement:** Global Company — Internal Infrastructure Assessment  
**Classification:** Confidential

---

## 1. Executive Summary

### What Was Tested
During Week 1, the assessment team identified and mapped the external-facing attack surface of Global Company's infrastructure across multiple geographic locations (Sofia, Skopje, Plovdiv) and cloud providers (Cloudflare, AWS). The team performed network discovery, full port scans, service identification, technology fingerprinting, and firewall/bypass testing on all identified targets.

### High-Level Findings
- **13 total assets** identified across the engagement scope
- **4 accessible web applications** with identifiable technologies
- **6 firewalled hosts** where no external services could be detected
- **1 critical discovery:** Self-managed GitLab instance (ddr.proxiad.bg) with LDAP authentication
- **1 high-risk discovery:** cPanel/WHM server (see.proxiad.com) with 21 exposed ports including management interfaces
- **Azure AD integration** detected on 2 applications (grow.proxiad.bg, successcard.proxiad.com)

### Risk Overview
| Severity | Count |
|----------|-------|
| 🔴 Critical | 0 |
| 🟠 High | 1 |
| 🟡 Medium | 4 |
| 🟢 Low | 8 |

---

## 2. Technical Summary

### 2.1 Assets Discovered

#### Sofia — 82.103.125.0/24 (2 accessible hosts)

| Asset | IP | Service Version | Technology Stack | Risk |
|-------|----|-----------------|------------------|------|
| **grow.proxiad.bg** | 82.103.125.235 | nginx 1.30.2 | Bootstrap 4.4.1, jQuery, Azure AD SSO, CSP, HSTS | 🟡 Medium |
| **ddr.proxiad.bg** | 82.103.125.239 | nginx 1.28.3 (Ubuntu) | **GitLab CE**, LDAP auth, WebSocket (ActionCable), OpenSearch | 🟡 Medium |

#### Skopje — 45.156.140.96/29 (2 hosts, firewalled)

| Asset | IP | Status | Risk |
|-------|----|--------|------|
| Skopje Host 1 | 45.156.140.97 | Stateful firewall — all TCP/UDP probes dropped | 🟢 Low |
| Skopje Host 2 | 45.156.140.99 | Stateful firewall — all TCP/UDP probes dropped | 🟢 Low |

#### Plovdiv (2 hosts, firewalled)

| Asset | IP | Status | Risk |
|-------|----|--------|------|
| Plovdiv Host 1 | 212.36.12.178 | ICMP responsive — all TCP ports filtered | 🟢 Low |
| Plovdiv Host 2 | 213.145.118.178 | ICMP responsive — all TCP ports filtered | 🟢 Low |

#### New Domain Targets (7 assets)

| Asset | IP | Service | Risk |
|-------|----|---------|------|
| **see.proxiad.com** | 185.80.2.208 | **Apache + cPanel/WHM** — 21 open ports (FTP, SMTP, DNS, cPanel, WHM, Webmail) | 🟠 **High** |
| **extranet.proxiad.com** | 185.161.45.103 | nginx 1.28.0 — Java/Spring portal, Bootstrap, jQuery 3.7.1 | 🟡 Medium |
| **successcard.proxiad.com** | 185.161.45.103 | nginx 1.28.0 — Java/Spring — **Azure AD OAuth2** | 🟡 Medium |
| **proxiad.bg** | 172.67.162.156 | Cloudflare CDN — origin unreachable (520 error) | 🟢 Low |
| **trackrecord.proxiad.bg** | 104.21.10.69 | Cloudflare CDN — "TrackRecord" application | 🟢 Low |
| **proxiad.desk-buddy.com** | 104.21.19.175 | Cloudflare CDN — Java/Spring desk booking app | 🟢 Low |
| **aihub.proxiad.bg** | 15.236.125.102 | AWS ALB (awselb/2.0) — 403 Forbidden, staging environment | 🟢 Low |

### 2.2 Evidence Snapshots

| Finding | Evidence |
|---------|----------|
| GitLab CE (self-managed) | `evidence/sofia-239-gitlab-login.html`, `gitlab-enumeration.md` |
| GitLab instance configuration | `evidence/sofia-239-gitlab-config.html` (SSH keys, rate limits, LDAP) |
| Full port scans (Sofia) | `sofia-fullscan-235.json`, `sofia-fullscan-239.json` |
| Full port scan (see.proxiad) | `new-see-fullscan.json` (21 ports) |
| Nmap service detection | `evidence/sofia-service-scan.nmap`, `evidence/extranet-service-scan.nmap` |
| Firewall bypass tests (Skopje) | `evidence/skopje-{ack,fin,null,udp}-scan.nmap` |
| WhatWeb fingerprinting | 11 files in `evidence/*-whatweb.txt` |
| TLS certificates | `evidence/sofia-235-cert.txt`, `evidence/sofia-239-cert.txt` |
| HTTP headers | `evidence/sofia-235-headers.txt`, `evidence/sofia-239-headers.txt` |
| Azure AD tenant ID | `evidence/successcard-whatweb.txt` (Tenant: fc2be715-4a94-42dd-832d-9d7dbb73c754) |

### 2.3 Notable Findings

#### 🔴 see.proxiad.com — cPanel/WHM Server (High Risk)
Full port scan revealed **21 open ports** including:
- **cPanel** (2082, 2083) — Web hosting control panel
- **WHM** (2086, 2087) — Web Host Manager
- **Webmail** (2095, 2096) — Email access
- **FTP** (21), **SMTP** (25, 26, 465, 587), **POP3** (110, 995), **IMAP** (143, 993)
- **DNS** (53)
- Hosted at `superhosting.bg`

Exposed management interfaces on a shared hosting server present a significant attack surface.

#### 🟡 ddr.proxiad.bg — Self-Managed GitLab (Medium Risk)
- GitLab Community Edition with **LDAP authentication** (potential AD integration)
- Public endpoints: `/explore`, `/public`, `/help` accessible anonymously
- **WebSocket** endpoint at `/-/cable` active
- **OpenSearch** at `/search/opensearch.xml` exposed
- Registration disabled — no unauthorized account creation
- Version not exposed (security hardening active)
- SSH host keys fingerprinted (ECDSA, ED25519, RSA)

#### 🟡 successcard.proxiad.com — Azure AD OAuth2 Exposure (Medium Risk)
- Full OAuth2 flow captured with:
  - **Tenant ID:** `fc2be715-4a94-42dd-832d-9d7dbb73c754`
  - **Client ID:** `a23a1380-ab03-4ebd-b6b1-46410e032147`
  - **Redirect URI:** `/login/oauth2/code/azure`
  - **Scopes:** openid, profile, email
- X-XSS-Protection disabled (0) on Java/Spring backend

#### 🟡 grow.proxiad.bg — Azure AD SSO Integration (Medium Risk)
- CSP header reveals `login.microsoftonline.com` in `connect-src`
- Azure AD / Office 365 authentication
- `unsafe-inline` and `unsafe-eval` in CSP script-src (potential XSS surface)

---

## 3. Risk Breakdown

| Severity | Count | Description |
|----------|-------|-------------|
| 🔴 **Critical** | 0 | No critical vulnerabilities identified in Week 1 |
| 🟠 **High** | **1** | see.proxiad.com — exposed cPanel/WHM with 21 open ports |
| 🟡 **Medium** | **4** | ddr.proxiad.bg (GitLab), grow.proxiad.bg (Azure AD), successcard.proxiad.com (Azure AD OAuth2), extranet.proxiad.com (Java/Spring) |
| 🟢 **Low** | **8** | All firewalled hosts (Skopje x2, Plovdiv x2) + Cloudflare-hosted (proxiad.bg, trackrecord, desk-buddy) + aihub.proxiad.bg (AWS ALB) |
| **Total** | **13** | |

### Risk by Category

| Category | Count | Assets |
|----------|-------|--------|
| **Web Applications** | 4 | grow.proxiad.bg, ddr.proxiad.bg, extranet.proxiad.com, successcard.proxiad.com |
| **Web Servers (Cloudflare)** | 3 | proxiad.bg, trackrecord.proxiad.bg, proxiad.desk-buddy.com |
| **Hosting/Infrastructure** | 2 | see.proxiad.com (cPanel), aihub.proxiad.bg (AWS) |
| **Firewalled (no services)** | 4 | Skopje (x2), Plovdiv (x2) |

---

## 4. Network Topology Draft

```
┌─────────────────────────────────────────────────────────────────┐
│                    GLOBAL COMPANY ATTACK SURFACE                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  [Sofia 82.103.125.0/24]                    [Skopje 45.156.140.96/29]
│   ├── grow.proxiad.bg (443)                    ├── 45.156.140.97
│   │   └── nginx 1.30.2 + Azure AD              └── 45.156.140.99
│   └── ddr.proxiad.bg (443)                     [Stateful Firewall]
│       └── nginx 1.28.3 + GitLab CE         ┌── all ports filtered
│                                              │
│  [Plovdiv]                                   │
│   ├── 212.36.12.178 (ICMP only)              │
│   └── 213.145.118.178 (ICMP only)            │
│                                              │
│  [External Cloud Hosting]                    │
│   ├── see.proxiad.com ─── superhosting.bg    │
│   │   └── cPanel/WHM + Email                 │
│   ├── extranet + successcard ── dedie.ate.info
│   │   └── nginx 1.28.0 + Java/Spring         │
│   ├── proxiad.bg ─── Cloudflare              │
│   ├── trackrecord.proxiad.bg ─── Cloudflare  │
│   ├── desk-buddy.com ─── Cloudflare           │
│   └── aihub.proxiad.bg ─── AWS (eu-west-3)    │
│                                              │
└─────────────────────────────────────────────────────────────────┘
```

---

## 5. Initial Risk Observations

1. **GitLab Instance (ddr.proxiad.bg)** — Self-managed GitLab with LDAP integration. No version exposed, but if an older version is running, known CVEs (e.g., critical auth bypasses, SSRF) could apply. LDAP integration suggests potential Active Directory link.

2. **Azure AD Integration** — Both grow.proxiad.bg and successcard.proxiad.com use Azure AD authentication. The exposed tenant ID enables tenant enumeration and targeted phishing.

3. **cPanel/WHM Exposure (see.proxiad.com)** — Full hosting control panel accessible from the internet. Shared hosting environment with email, DNS, and FTP — any compromise could affect multiple hosted sites.

4. **Firewalled Hosts (Skopje + Plovdiv)** — 4 hosts completely invisible from external scans. These are likely internal infrastructure (potential domain controllers, file servers, or VPN endpoints) that will become visible once internal network access is obtained in Week 2.

5. **Cloudflare Protection** — Three targets are behind Cloudflare CDN. Only the origin server IP can be scanned, which is currently masked. SSL certificate transparency logs could reveal origin IPs.

6. **AWS Staging Environment (aihub.proxiad.bg)** — The staging DNS (ddr-staging) follows the same naming as the production GitLab (ddr.proxiad.bg). This naming convention could help in discovering other staging/test environments.

7. **No AD Services Detected Externally** — No SMB, LDAP, DNS, or Kerberos services found on any external target. The Active Directory environment is fully internal, confirming that internal network access is required for AD assessment (Week 2).

---

## 6. Next Week Plan (Week 2 — AD & Internal Security)

| Day | Objective | Activities |
|-----|-----------|------------|
| **Day 6** | AD discovery | Identify domains, DCs, trust boundaries |
| **Day 7** | Identity & access review | Enumerate users, groups, policies |
| **Day 8** | Service trust analysis | Identify shared services, dependencies |
| **Day 9** | Security posture review | Identify misconfigurations (SMB, LDAP, auth) |
| **Day 10** | Risk validation | Confirm exploitability potential (non-destructive) |

### Required for Week 2
- **Internal network access or VPN** must be established to assess Skopje/Plovdiv hosts and AD environment
- GitLab access (if credentials obtained) for deeper source code and CI/CD review

### Blockers
- No internal network access available yet — Skopje/Plovdiv hosts unreachable
- GitLab credentials not yet obtained — LDAP integration requires AD access

---

## 7. Evidence File Complete Manifest

| File | Contents |
|------|----------|
| `service-inventory-day3.md` | Full Day 3 deliverable with attack surface map v1 |
| `gitlab-enumeration.md` | GitLab CE detailed enumeration report |
| `new-targets-report.md` | All 7 new domain targets assessed |
| `active-recon.md` | Day 2 network discovery report |
| `sofia-fullscan-235.json` | Masscan full port scan — grow.proxiad.bg |
| `sofia-fullscan-239.json` | Masscan full port scan — ddr.proxiad.bg |
| `new-see-fullscan.json` | Masscan full port scan — see.proxiad.com (21 ports) |
| `evidence/` | 30+ files (Nmap, WhatWeb, headers, certs, configs) |

---

**Prepared by:** Penetration Testing Team  
**Date:** 28 July 2026  
**Classification:** Confidential — For authorized recipients only