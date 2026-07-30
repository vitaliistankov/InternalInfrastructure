# Service Exposure Mapping — Day 3 Deliverable
**Date:** 27 July 2026  
**Objective:** Week 1, Day 3 — Identify open ports + services per host  
**Methodology:** Full TCP port scan (0-65535) → Nmap service detection → Technology fingerprinting → Alternative scan techniques for firewalled hosts

---

## Scan Parameters

| Activity | Command | Target |
|----------|---------|--------|
| Full TCP scan (.235) | `masscan 82.103.125.235 -p0-65535 --rate 2000` | Sofia |
| Full TCP scan (.239) | `masscan 82.103.125.239 -p0-65535 --rate 2000` | Sofia |
| Service detection | `nmap -sS -sV -sC -p 443 -T4` | Both Sofia hosts |
| Technology detection | `whatweb -a 3` | Both Sofia hosts |
| HTTP headers | `curl -sS -k -I` | Both Sofia hosts |
| TLS certificates | `openssl s_client` | Both Sofia hosts |
| ACK scan | `nmap -sA` (bypass firewall) | Skopje hosts |
| FIN scan | `nmap -sF` (bypass firewall) | Skopje hosts |
| NULL scan | `nmap -sN` (bypass firewall) | Skopje hosts |
| UDP scan | `nmap -sU -p53,161,162,500,514,1434,1900,4500` | Skopje hosts |
| Plovdiv scan | `nmap -sS -sV -Pn -p common ports` | Plovdiv hosts |

---

## Service Inventory Table

| IP Address | Location | Open Ports | Service (Version) | Technology Stack | Hostname | Notes |
|------------|----------|------------|--------------------|------------------|----------|-------|
| 82.103.125.235 | Sofia | 443/tcp | nginx 1.30.2 | Bootstrap 4.4.1, jQuery, CSP, HSTS | grow.proxiad.bg | Grow@Proxiad portal — Azure AD/Office 365 integration (connect-src: login.microsoftonline.com) |
| 82.103.125.239 | Sofia | 443/tcp | nginx 1.28.3 (Ubuntu) | GitLab Community Edition, Webpack assets, ActionCable, OpenSearch | ddr.proxiad.bg | Self-managed GitLab instance — public projects accessible, `/explore` and `/public` available anonymously |
| 45.156.140.97 | Skopje | All filtered | Unknown | Unknown | — | Stateful firewall — all TCP/UDP probes dropped. No ports discoverable externally |
| 45.156.140.99 | Skopje | All filtered | Unknown | Unknown | — | Stateful firewall — all TCP/UDP probes dropped. No ports discoverable externally |
| 212.36.12.178 | Plovdiv | All filtered | Unknown (ICMP responsive) | Unknown | — | Host responds to ping. All common TCP ports filtered |
| 213.145.118.178 | Plovdiv | All filtered | Unknown (ICMP responsive) | Unknown | — | Host responds to ping. All common TCP ports filtered |

---

## Attack Surface Map v1

```
[Sofia Network: 82.103.125.0/24]
│
├── 82.103.125.235 ── 443/tcp ── grow.proxiad.bg ── Grow@Proxiad Portal
│   │   nginx 1.30.2
│   │   ├── Azure AD / Office 365 SSO integration detected
│   │   ├── Content-Security-Policy configured
│   │   └── Static content (single-page app)
│   │
└── 82.103.125.239 ── 443/tcp ── ddr.proxiad.bg ── GitLab CE (Self-Managed)
        nginx 1.28.3 (Ubuntu)
        ├── GitLab Community Edition (version unknown, requires auth)
        ├── Public projects accessible at /explore and /public
        ├── API v4 at /api/v4 (401 without auth token)
        ├── WebSocket endpoint at /-/cable (ActionCable)
        ├── OpenSearch at /search/opensearch.xml
        └── CSRF-protected login at /users/sign_in

[Skopje Network: 45.156.140.96/29]
│
├── 45.156.140.97 ── [All filtered] ── Firewalled host
│   │   ├── Stateful firewall: SYN, ACK, FIN, NULL, UDP probes all blocked
│   │   └── No services externally discoverable
│   │
└── 45.156.140.99 ── [All filtered] ── Firewalled host
        ├── Stateful firewall: SYN, ACK, FIN, NULL, UDP probes all blocked
        └── No services externally discoverable

[Plovdiv]
│
├── 212.36.12.178 ── [All filtered] ── ICMP responsive, TCP filtered
│   └── Host alive but no exposed services detected
│
└── 213.145.118.178 ── [All filtered] ── ICMP responsive, TCP filtered
    └── Host alive but no exposed services detected
```

---

## Key Findings

### High Value — GitLab Instance (82.103.125.239)
- **Self-managed GitLab** with public project browsing enabled
- Source code, CI/CD pipelines, issue trackers potentially accessible
- WebSocket endpoint active (`/-/cable`) — potential for real-time attacks
- OpenSearch available — may leak indexed content
- Ubuntu + nginx stack — check for known CVEs against nginx 1.28.3

### Medium Value — Grow@Proxiad Portal (82.103.125.235)
- **Azure AD integration** — SSO via Microsoft 365
- CSP with `unsafe-inline` and `unsafe-eval` in script-src
- Static HTML/JS frontend — likely an HR/training portal
- nginx 1.30.2 — check for CVEs

### Firewalled Hosts (Skopje + Plovdiv)
- **4 hosts** total are behind a stateful firewall that drops all unsolicited inbound traffic
- These likely initiate outbound connections (VPN, site-to-site tunnels, client-side agents)
- Cannot be assessed externally without additional source-IP whitelisting or internal network access

---

## Risk Classification

| Asset | IP | Value | Risk Level | Notes |
|-------|----|-------|------------|-------|
| GitLab CE | 82.103.125.239 | Source code, credentials, CI/CD pipelines | **High** | Public project browsing enabled — potential data leak |
| Grow@Proxiad Portal | 82.103.125.235 | HR portal with Azure AD SSO | **Medium** | Possible user enumeration via Azure AD |
| Skopje Hosts | 45.156.140.97, .99 | Unknown | **Medium** | Firewalled — could be AD domain controllers |
| Plovdiv Hosts | 212.36.12.178, 213.145.118.178 | Unknown | **Low** | ICMP responsive but no services detected |

---

## Evidence File Manifest

| File | Contents |
|------|----------|
| `sofia-fullscan-235.json` | Masscan full port scan — only port 443 open |
| `sofia-fullscan-239.json` | Masscan full port scan — only port 443 open |
| `evidence/sofia-service-scan.nmap` | Nmap service version detection (both Sofia hosts) |
| `evidence/sofia-235-whatweb.txt` | WhatWeb technology fingerprinting — grow.proxiad.bg |
| `evidence/sofia-239-whatweb.txt` | WhatWeb technology fingerprinting — ddr.proxiad.bg (GitLab) |
| `evidence/sofia-235-headers.txt` | HTTP response headers — grow.proxiad.bg |
| `evidence/sofia-239-headers.txt` | HTTP response headers — ddr.proxiad.bg (GitLab) |
| `evidence/sofia-235-cert.txt` | TLS certificate — Let's Encrypt, CN: grow.proxiad.bg |
| `evidence/sofia-239-cert.txt` | TLS certificate — Let's Encrypt, CN: ddr.proxiad.bg |
| `evidence/sofia-239-gitlab-login.html` | GitLab login page source (CSRF token, Webpack bundles) |
| `evidence/skopje-ack-scan.nmap` | ACK scan — all ports filtered on Skopje hosts |
| `evidence/skopje-fin-scan.nmap` | FIN scan — all ports open\|filtered on Skopje hosts |
| `evidence/skopje-null-scan.nmap` | NULL scan — all ports open\|filtered on Skopje hosts |
| `evidence/skopje-udp-scan.nmap` | UDP scan — all ports open\|filtered on Skopje hosts |
| `evidence/plovdiv-service-scan.nmap` | Service scan — all ports filtered on Plovdiv hosts |

---

## Observations for Week 2 (AD / Internal Security)

1. **GitLab** should be a priority for Week 2 — enumerate projects, users, and check for exposed CI/CD variables
2. **Azure AD integration** on grow.proxiad.bg suggests Microsoft 365 is in use — potential for cloud-based enumeration
3. **Skopje firewall** behavior (stateful, no response) is consistent with:
   - VPN tunnel endpoints or site-to-site gateways
   - Hosts that only communicate via outbound-initiated sessions
   - Internal-only servers with no public-facing services
4. **Plovdiv hosts** alive but firewalled — may be accessible from a different network segment or require internal routing
5. **No SMB, LDAP, DNS, or AD-typical services** detected on any external-facing host — AD environment is likely fully internal