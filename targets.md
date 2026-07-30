# Targets

## Domain Targets

| # | Target | IP(s) | Status | HTTP |
|---|--------|-------|--------|------|
| 1 | see.proxiad.com | 185.80.2.208 | ❌ Not tested (new) | 200 |
| 2 | proxiad.desk-buddy.com | 104.21.19.175, 172.67.187.7 (Cloudflare) | ❌ Not tested (new) | 302 |
| 3 | proxiad.bg | 172.67.162.156, 104.21.10.69 (Cloudflare) | ❌ Not tested (new) | 301 |
| 4 | extranet.proxiad.com | 185.161.45.103 | ❌ Not tested (new) | 200 |
| 5 | successcard.proxiad.com | 185.161.45.103 (same as extranet) | ❌ Not tested (new) | 302 |
| 6 | grow.proxiad.bg | 82.103.125.235 | ✅ Tested (Days 2-3) | 200 |
| 7 | ddr.proxiad.bg | 82.103.125.239 | ✅ Tested (Days 2-3: GitLab) | 200 |
| 8 | trackrecord.proxiad.bg | 172.67.162.156, 104.21.10.69 (Cloudflare) | ❌ Not tested (new) | 200 |
| 9 | aihub.proxiad.bg | 13.36.179.26, 15.236.125.102, 15.224.141.159 (AWS ALB) | ❌ Not tested (new) | 200 |

## Infrastructure Targets

| # | Target IP | Location | Status |
|---|-----------|----------|--------|
| 1 | 45.156.140.97 | Skopje | ✅ Tested (firewalled) |
| 2 | 45.156.140.99 | Skopje | ✅ Tested (firewalled) |
| 3 | 212.36.12.178 | Plovdiv | ✅ Tested (ICMP only) |
| 4 | 213.145.118.178 | Plovdiv | ✅ Tested (ICMP only) |

**Remaining:** 7 domain targets need Day 1-5 testing