# Active Reconnaissance — Internal Infrastructure Assessment
**Target:** Global Company — Internal Infrastructure  
**Date:** 20-21 July 2026  
**Methodology:** Week 1, Day 2 — Network discovery (Project Execution Plan)  

---

## Recon Workflow

Professional engagement sequence:

1. Host discovery
   - Identify which IP addresses are alive.
2. Port discovery
   - Determine which TCP/UDP ports are exposed.
3. Service identification
   - Identify the protocols and applications behind those ports.
4. Technology fingerprinting
   - Determine software stacks, versions, and frameworks.
5. Protocol-specific enumeration
   - Gather configuration details from services such as SMB, DNS, SNMP, or HTTP.
6. Attack surface inventory
   - Produce a table of exposed systems.

---

## Tools Used

| Category            | Tool          | Primary Purpose                                          |
| ------------------- | ------------- | -------------------------------------------------------- |
| Host Discovery      | Nmap          | Discover live hosts and identify open ports and services |
| Host Discovery      | Masscan       | Very fast port discovery on large networks               |
| Service Enumeration | Netcat        | Test connectivity to specific ports and services         |
| Service Enumeration | Socat         | Advanced network connection testing                      |
| SMB Enumeration     | enum4linux-ng | Enumerate Windows shares and domain information          |
| SMB Enumeration     | smbclient     | Browse SMB shares                                        |
| DNS                 | dnsrecon      | DNS enumeration                                          |
| DNS                 | dig           | Query DNS records                                        |
| DNS                 | host          | Simple DNS resolution                                    |
| Web                 | WhatWeb       | Detect web technologies                                  |
| Web                 | Nikto         | Identify common web server issues                        |
| Web                 | Gobuster      | Discover directories and virtual hosts                   |
| Web                 | Feroxbuster   | Fast recursive content discovery                         |
| HTTP                | curl          | Inspect HTTP responses and headers                       |
| HTTP                | wget          | Retrieve web content                                     |
| TLS                 | OpenSSL       | Inspect TLS certificates and protocols                   |
| SNMP                | snmpwalk      | Query SNMP information (when authorized)                 |
| Network Analysis    | Wireshark     | Analyze captured traffic                                 |
| Network Analysis    | tcpdump       | Capture packets from the command line                    |

---

## Scan Results

### 1. Skopje Subnet — `45.156.140.96/29`

**Host Discovery:** `nmap -sn -n 45.156.140.96/29`
- Scan time: 2026-07-20 23:20 EEST
- 8 IPs scanned → **2 hosts up**

| IP Address      | Status | Latency |
|-----------------|--------|---------|
| 45.156.140.97   | Up     | 0.0080s |
| 45.156.140.99   | Up     | 0.0087s |
| 45.156.140.96   | Down   | —       |
| 45.156.140.98   | Down   | —       |
| 45.156.140.100  | Down   | —       |
| 45.156.140.101  | Down   | —       |
| 45.156.140.102  | Down   | —       |
| 45.156.140.103  | Down   | —       |

**Port Discovery:** `nmap -sS -sV -T4 --top-ports 100`
- Scan time: 2026-07-21 11:38 EEST
- Both hosts: **All 100 scanned ports filtered** (no-response)
- **Masscan full port scan (1-65535):** 0 open ports found
- **Masscan common ports scan (80,443,22,21,25,53,110,139,445,3389,8080):** 0 open ports found
- Assessment: Hosts are behind a firewall that drops unsolicited SYN probes. Only replies to established sessions or specific allow-listed sources are permitted.

**Evidence saved:**
- `evidence/host-discovery_45.156.140.96-29.{nmap,gnmap,xml}`
- `evidence/port-discovery_45.156.140.97-99.{nmap,gnmap,xml}`
- `masscan_results.xml`, `external_scan.json`, `discovery.txt`

---

### 2. Sofia Subnet — `82.103.125.0/24`

**Port Scan (common ports):** `sudo masscan -p80,443,22,21,25,53,110,139,445,3389,8080 --rate 1000`

| IP Address      | Open Port | Service |
|-----------------|-----------|---------|
| 82.103.125.235  | 443/tcp   | HTTPS   |
| 82.103.125.239  | 443/tcp   | HTTPS   |

- **Masscan full port scan (0-65535)** was running but still in progress when paused — partial results in `sofia-external_scan.json`

**Evidence saved:**
- `sofia_discovery.txt`
- `sofia-external_scan.json`

---

### 3. Plovdiv — `212.36.12.178/32`, `213.145.118.178/32`

**Port Scan (common ports):** `sudo masscan -p80,443,22,21,25,53,110,139,445,3389,8080 --rate 1000`

| IP Address      | Open Ports |
|-----------------|------------|
| 212.36.12.178   | None found |
| 213.145.118.178 | None found |

- Both hosts: 0 open ports detected on common ports. Likely firewalled or not reachable via SYN scan.

**Evidence saved:**
- `plovdiv-1-discovery.txt`
- `plovdiv-2-discovery.txt`

---

## Host Inventory v1

| IP Address       | Location   | Status | OS (Estimated) | Open Services | Criticality | Notes                                    |
|------------------|------------|--------|----------------|---------------|-------------|------------------------------------------|
| 45.156.140.97    | Skopje     | Up     | Unknown        | None detected | Medium      | All ports filtered; firewall present     |
| 45.156.140.99    | Skopje     | Up     | Unknown        | None detected | Medium      | All ports filtered; firewall present     |
| 82.103.125.235   | Sofia      | Up     | Unknown        | HTTPS (443)   | High        | Web server; requires further enumeration |
| 82.103.125.239   | Sofia      | Up     | Unknown        | HTTPS (443)   | High        | Web server; requires further enumeration |
| 212.36.12.178    | Plovdiv    | Unreachable | Unknown    | None detected | Low         | No response to SYN probes                |
| 213.145.118.178  | Plovdiv    | Unreachable | Unknown    | None detected | Low         | No response to SYN probes                |

---

## Next Steps (Week 1, Day 3 — Service Exposure Mapping)

1. Perform deeper port scan on Sofia HTTPS hosts (82.103.125.235, 82.103.125.239) using full port range
2. Technology fingerprinting on Sofia web servers using WhatWeb and curl
3. Attempt alternative scan techniques on Skopje hosts (e.g., TCP ACK scan, FIN scan, or decoy scans) to bypass firewall filtering
4. DNS resolution on discovered IPs to identify hostnames
5. Comprehensive full port scan on Sofia subnet once masscan completes

---

## Good Practice

Even with authorization:

- Start with low-impact discovery before increasing scan intensity.
- Coordinate any intensive scanning with the agreed Rules of Engagement.
- Record when you scanned, what you scanned, and why, so findings are traceable and reproducible.
- Save scan results in structured formats (XML/JSON where supported) so they can be incorporated into your final report and evidence package.

---

## Evidence Files

| File | Description |
|------|-------------|
| `evidence/host-discovery_45.156.140.96-29.nmap` | Nmap ping sweep - Skopje subnet |
| `evidence/host-discovery_45.156.140.96-29.xml` | XML output for reporting |
| `evidence/port-discovery_45.156.140.97-99.nmap` | Nmap port scan - Skopje live hosts |
| `evidence/port-discovery_45.156.140.97-99.xml` | XML output for reporting |
| `sofia_discovery.txt` | Masscan common ports - Sofia subnet |
| `plovdiv-1-discovery.txt` | Masscan common ports - Plovdiv IP 1 |
| `plovdiv-2-discovery.txt` | Masscan common ports - Plovdiv IP 2 |
| `masscan_results.xml` | Masscan full port scan - Skopje (all filtered) |