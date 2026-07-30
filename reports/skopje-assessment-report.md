# Skopje Infrastructure Assessment Report

**Assessment:** Internal Infrastructure Security Assessment  
**Target Location:** Skopje  
**Report Date:** 30 July 2026  
**Classification:** Confidential  

---

## 1. Scope

### 1.1 Assessment Purpose
This report documents the reconnaissance findings for the Skopje subnet identified during the external reconnaissance phase of the Global Company internal infrastructure security assessment. The purpose is to consolidate all evidence collected against Skopje-based assets and provide an evidence-based analysis of the externally visible attack surface.

### 1.2 Target Subnet
The Skopje subnet is defined as:

| Attribute | Value |
|-----------|-------|
| Subnet | `45.156.140.96/29` |
| IP Range | 45.156.140.96 – 45.156.140.103 |
| Total Addresses | 8 |
| Live Hosts Identified | 2 |

### 1.3 Identified Live Hosts
The following hosts were confirmed as alive during host discovery:

| IP Address | Status | Latency |
|------------|--------|---------|
| 45.156.140.97 | Up | 0.0080s |
| 45.156.140.99 | Up | 0.0087s |

*(Source: evidence/host-discovery_45.156.140.96-29.nmap)*

### 1.4 Assessment Phase
This assessment was conducted during the reconnaissance phase (Week 1, Days 2–3) of the Project Execution Plan. The testing perspective was external reconnaissance, meaning all probes were sent from an external network position without prior authentication or internal network access.

### 1.5 Testing Perspective
All scanning and enumeration were performed from an external network position. The results represent the externally visible attack surface only. Internal network access was not available during this phase.

---

## 2. Evidence Sources

The following table lists all Skopje-related evidence files collected during the reconnaissance phase.

| Evidence File | Tool | Purpose | Result Summary |
|---------------|------|---------|----------------|
| `evidence/host-discovery_45.156.140.96-29.nmap` | Nmap | Host discovery (ICMP ping sweep) | 2 hosts up: 45.156.140.97 and .99 |
| `evidence/host-discovery_45.156.140.96-29.gnmap` | Nmap | Host discovery (grepable output) | 2 hosts up |
| `evidence/host-discovery_45.156.140.96-29.xml` | Nmap | Host discovery (XML output) | 2 hosts up |
| `evidence/port-discovery_45.156.140.97-99.nmap` | Nmap | TCP port discovery (top 100 ports) | All 100 ports filtered |
| `evidence/port-discovery_45.156.140.97-99.gnmap` | Nmap | TCP port discovery (grepable output) | All 100 ports filtered |
| `evidence/port-discovery_45.156.140.97-99.xml` | Nmap | TCP port discovery (XML output) | All 100 ports filtered |
| `evidence/skopje-ack-scan.nmap` | Nmap | TCP ACK scan (firewall ruleset mapping) | All 12 ports filtered |
| `evidence/skopje-ack-scan.gnmap` | Nmap | TCP ACK scan (grepable output) | All 12 ports filtered |
| `evidence/skopje-ack-scan.xml` | Nmap | TCP ACK scan (XML output) | All 12 ports filtered |
| `evidence/skopje-fin-scan.nmap` | Nmap | TCP FIN scan (firewall bypass attempt) | All 12 ports open\|filtered |
| `evidence/skopje-fin-scan.gnmap` | Nmap | TCP FIN scan (grepable output) | All 12 ports open\|filtered |
| `evidence/skopje-fin-scan.xml` | Nmap | TCP FIN scan (XML output) | All 12 ports open\|filtered |
| `evidence/skopje-null-scan.nmap` | Nmap | TCP NULL scan (firewall bypass attempt) | All 12 ports open\|filtered |
| `evidence/skopje-null-scan.gnmap` | Nmap | TCP NULL scan (grepable output) | All 12 ports open\|filtered |
| `evidence/skopje-null-scan.xml` | Nmap | TCP NULL scan (XML output) | All 12 ports open\|filtered |
| `evidence/skopje-udp-scan.nmap` | Nmap | UDP scan (common UDP services) | All 8 ports open\|filtered |
| `evidence/skopje-udp-scan.gnmap` | Nmap | UDP scan (grepable output) | All 8 ports open\|filtered |
| `evidence/skopje-udp-scan.xml` | Nmap | UDP scan (XML output) | All 8 ports open\|filtered |
| `masscan_results.xml` | Masscan | Full TCP port scan (1–65535) | 0 open ports found |
| `external_scan.json` | Masscan | External TCP scan (JSON output) | 0 open ports found |
| `discovery.txt` | Masscan | Common ports discovery | 0 open ports found |
| `active-recon.md` | — | Activity log and methodology | Skopje section (lines 55–83) |
| `service-inventory-day3.md` | — | Day 3 service inventory deliverable | Skopje entries in inventory table |
| `masscan.md` | — | Masscan methodology notes | Skopje scanning procedures |
| `targets.md` | — | Target inventory | Skopje host entries |
| `weekly-report-1.md` | — | Week 1 management report | Skopje risk classification |

---

## 3. Reconnaissance Results

### 3.1 Host Discovery

A ping sweep (ICMP echo requests) was performed against the Skopje subnet `45.156.140.96/29` using Nmap with the `-sn` flag.

**Scan Parameters:**
```
nmap -sn -n 45.156.140.96/29
```

**Results:**
- 8 IP addresses scanned
- 2 hosts responded to ICMP probes
- 6 hosts did not respond

| IP Address | Status | Latency |
|------------|--------|---------|
| 45.156.140.97 | Up | 0.0080s |
| 45.156.140.99 | Up | 0.0087s |
| 45.156.140.96 | Down | — |
| 45.156.140.98 | Down | — |
| 45.156.140.100 | Down | — |
| 45.156.140.101 | Down | — |
| 45.156.140.102 | Down | — |
| 45.156.140.103 | Down | — |

**Evidence Reference:** `evidence/host-discovery_45.156.140.96-29.nmap`
**Scan Date:** 20 July 2026

### 3.2 TCP Port Discovery

Following host discovery, a TCP port scan was performed against the two live hosts using Nmap with SYN scan and service version detection.

**Scan Parameters:**
```
nmap -sS -sV -T4 --top-ports 100 45.156.140.97 45.156.140.99
```

**Results:**
- Both hosts: All 100 scanned TCP ports returned **filtered** (no response)
- No open TCP ports were identified

**Service Version Detection:** No banners or service fingerprints were obtained because no ports returned a SYN-ACK response.

**Evidence Reference:** `evidence/port-discovery_45.156.140.97-99.nmap`
**Scan Date:** 21 July 2026

**Additional TCP Scanning (Masscan):**
A full TCP port scan (ports 1–65535) was performed using Masscan at a rate of 5,000 packets per second. A separate common-ports scan was also performed targeting ports 80, 443, 22, 21, 25, 53, 110, 139, 445, 3389, and 8080. Both scans returned zero open ports.

**Evidence References:**
- `masscan_results.xml` — Full TCP port scan
- `discovery.txt` — Common ports scan

### 3.3 Firewall and Filtering Analysis

Because the initial TCP SYN scan and Masscan results indicated that all probes were being dropped, additional scanning techniques were employed to characterize the behavior of the network perimeter filtering. These techniques use different TCP flag combinations or UDP protocols to determine whether the filtering is stateful or stateless.

#### 3.3.1 TCP ACK Scan (`-sA`)

The ACK scan sends packets with the ACK flag set. This technique does not initiate a new connection and is used to map firewall rulesets rather than discover open ports.

**Results:**
| Port | 45.156.140.97 | 45.156.140.99 |
|------|---------------|---------------|
| 21/tcp (ftp) | filtered | filtered |
| 22/tcp (ssh) | filtered | filtered |
| 25/tcp (smtp) | filtered | filtered |
| 53/tcp (domain) | filtered | filtered |
| 80/tcp (http) | filtered | filtered |
| 110/tcp (pop3) | filtered | filtered |
| 139/tcp (netbios-ssn) | filtered | filtered |
| 443/tcp (https) | filtered | filtered |
| 445/tcp (microsoft-ds) | filtered | filtered |
| 3389/tcp (ms-wbt-server) | filtered | filtered |
| 8080/tcp (http-proxy) | filtered | filtered |
| 8443/tcp (https-alt) | filtered | filtered |

All 12 tested ports returned a **filtered** state. No ports returned an **unfiltered** state, which would indicate that the firewall is passing traffic through but no application is listening.

**Evidence Reference:** `evidence/skopje-ack-scan.nmap`
**Scan Date:** 27 July 2026

#### 3.3.2 TCP FIN Scan (`-sF`)

The FIN scan sends packets with only the FIN flag set. According to RFC 793, a closed port should respond with an RST packet, while an open port should drop the packet silently. This technique can sometimes bypass stateless firewalls that only filter SYN packets.

**Results:**
| Port | 45.156.140.97 | 45.156.140.99 |
|------|---------------|---------------|
| 21/tcp (ftp) | open\|filtered | open\|filtered |
| 22/tcp (ssh) | open\|filtered | open\|filtered |
| 25/tcp (smtp) | open\|filtered | open\|filtered |
| 53/tcp (domain) | open\|filtered | open\|filtered |
| 80/tcp (http) | open\|filtered | open\|filtered |
| 110/tcp (pop3) | open\|filtered | open\|filtered |
| 139/tcp (netbios-ssn) | open\|filtered | open\|filtered |
| 443/tcp (https) | open\|filtered | open\|filtered |
| 445/tcp (microsoft-ds) | open\|filtered | open\|filtered |
| 3389/tcp (ms-wbt-server) | open\|filtered | open\|filtered |
| 8080/tcp (http-proxy) | open\|filtered | open\|filtered |
| 8443/tcp (https-alt) | open\|filtered | open\|filtered |

All 12 tested ports returned an **open|filtered** state. Nmap cannot distinguish between open and filtered ports when no response is received. The absence of RST packets from closed ports is consistent with a firewall that silently drops all inbound traffic regardless of TCP flag combination.

**Evidence Reference:** `evidence/skopje-fin-scan.nmap`
**Scan Date:** 27 July 2026

#### 3.3.3 TCP NULL Scan (`-sN`)

The NULL scan sends packets with no TCP flags set. Similar to the FIN scan, this technique may bypass stateless filtering rules that only inspect SYN packets.

**Results:**
All 12 tested ports on both hosts returned an **open|filtered** state. The results are identical to the FIN scan, confirming that the firewall does not respond to any TCP packet that is not part of an established session.

**Evidence Reference:** `evidence/skopje-null-scan.nmap`
**Scan Date:** 27 July 2026

#### 3.3.4 UDP Scan (`-sU`)

A UDP scan was performed against common UDP service ports. UDP scanning is inherently slower and less reliable than TCP scanning because UDP services may not respond to empty probes.

**Results:**
| Port | 45.156.140.97 | 45.156.140.99 |
|------|---------------|---------------|
| 53/udp (domain) | open\|filtered | open\|filtered |
| 161/udp (snmp) | open\|filtered | open\|filtered |
| 162/udp (snmptrap) | open\|filtered | open\|filtered |
| 500/udp (isakmp) | open\|filtered | open\|filtered |
| 514/udp (syslog) | open\|filtered | open\|filtered |
| 1434/udp (ms-sql-m) | open\|filtered | open\|filtered |
| 1900/udp (upnp) | open\|filtered | open\|filtered |
| 4500/udp (nat-t-ike) | open\|filtered | open\|filtered |

All 8 tested UDP ports returned an **open|filtered** state. UDP scans typically produce open|filtered results when no response is received, as the scanner cannot distinguish between an open port that does not respond and a filtered port.

**Evidence Reference:** `evidence/skopje-udp-scan.nmap`
**Scan Date:** 27 July 2026

#### 3.3.5 Filtering Analysis Summary

The following behaviors were consistently observed across all scanning techniques:

| Scan Type | Observed Behavior | Interpretation |
|-----------|-------------------|----------------|
| TCP SYN (-sS) | No response on all ports | Firewall drops unsolicited SYN packets |
| TCP ACK (-sA) | No response on all ports | Firewall drops unsolicited ACK packets |
| TCP FIN (-sF) | No response on all ports | Firewall drops packets regardless of TCP flags |
| TCP NULL (-sN) | No response on all ports | Firewall drops packets regardless of TCP flags |
| UDP (-sU) | No response on all ports | Firewall drops unsolicited UDP datagrams |
| ICMP ping | Response received | ICMP is permitted through the firewall |

The combination of results indicates that the perimeter filtering device does not respond to any unsolicited TCP or UDP probes, regardless of the flags or protocol used. The only permitted traffic type observed was ICMP echo requests (ping). This behavior is consistent with a stateful firewall or a host-based firewall configured to drop all inbound traffic that is not part of an established outbound session.

---

## 4. Technical Findings

### F-001: Inbound Network Filtering on Skopje External Perimeter

**Severity:** Informational

**Description:**
All TCP and UDP probes sent to the Skopje hosts (45.156.140.97 and 45.156.140.99) were silently dropped by the perimeter filtering device. This was confirmed across multiple scanning techniques including SYN, ACK, FIN, NULL, and UDP scans. No RST packets, ICMP unreachable messages, or any other responses were received for any probed port.

**Evidence:**
- `evidence/port-discovery_45.156.140.97-99.nmap` — All 100 top TCP ports filtered
- `masscan_results.xml` — Full TCP port scan (0–65535): 0 open ports
- `evidence/skopje-ack-scan.nmap` — All 12 TCP ports filtered
- `evidence/skopje-fin-scan.nmap` — All 12 TCP ports open|filtered
- `evidence/skopje-null-scan.nmap` — All 12 TCP ports open|filtered
- `evidence/skopje-udp-scan.nmap` — All 8 UDP ports open|filtered

**Impact:**
No externally reachable services were identified. The filtering prevents direct external access to any services that may be running on these hosts. If these hosts provide services to internal users, those services are not accessible from the external testing position.

**Recommendation:**
Internal network access should be obtained to perform service enumeration from an internal vantage point. From an external position, no further service discovery techniques are expected to yield different results.

---

### F-002: Hosts Respond to ICMP Discovery but TCP/UDP Enumeration Did Not Identify Reachable Services

**Severity:** Informational

**Description:**
Both hosts (45.156.140.97 and 45.156.140.99) responded to ICMP echo requests (ping) during host discovery, confirming they are alive and reachable on the network. However, all subsequent TCP and UDP service enumeration probes were dropped by the filtering device. This indicates that the hosts are present on the network but are not exposing any services to the external testing position.

**Evidence:**
- `evidence/host-discovery_45.156.140.96-29.nmap` — ICMP ping responses received
- `evidence/port-discovery_45.156.140.97-99.nmap` — No open TCP ports identified
- `masscan_results.xml` — No open ports identified on full port range

**Impact:**
While the hosts are confirmed alive, their function and role cannot be determined from an external testing position due to the network filtering. Possible roles include internal servers, domain controllers, workstations, or network appliances that only communicate via outbound-initiated connections.

**Recommendation:**
When internal network access becomes available, perform host discovery and service enumeration from the internal network to determine the role and function of these hosts.

---

### F-003: Alternative TCP/UDP Probing Techniques Did Not Reveal Additional Accessible Services

**Severity:** Informational

**Description:**
Four alternative scanning techniques were employed to attempt to bypass or characterize the network filtering:
- TCP ACK scan (firewall ruleset mapping)
- TCP FIN scan (stateless firewall bypass)
- TCP NULL scan (stateless firewall bypass)
- UDP scan (common UDP services)

None of these techniques revealed any accessible services. All ports returned either **filtered** or **open|filtered** states, with no ports returning an **open** or **unfiltered** state.

**Evidence:**
- `evidence/skopje-ack-scan.nmap` — All filtered
- `evidence/skopje-fin-scan.nmap` — All open|filtered
- `evidence/skopje-null-scan.nmap` — All open|filtered
- `evidence/skopje-udp-scan.nmap` — All open|filtered

**Impact:**
The filtering device consistently drops all unsolicited inbound traffic regardless of protocol or TCP flag combination. This demonstrates that the filtering is not a simple SYN-flags-only rule and is likely a stateful inspection mechanism.

**Recommendation:**
No further firewall bypass techniques are recommended from the external position. The filtering behavior is consistent with a properly configured stateful firewall. Internal network access is required for further assessment.

---

### F-004: No Externally Reachable Services Identified During Reconnaissance

**Severity:** Informational

**Description:**
Despite comprehensive scanning across all 65,535 TCP ports, common UDP ports, and multiple scan techniques, no externally reachable services were identified on either Skopje host. The complete list of probed ports and techniques is as follows:

| Scan Type | Scope | Result |
|-----------|-------|--------|
| TCP SYN (Nmap) | Top 100 ports | 0 open |
| TCP SYN (Masscan) | Ports 1–65535 | 0 open |
| TCP SYN (Masscan) | 11 common ports | 0 open |
| TCP ACK (Nmap) | 12 ports | 0 unfiltered |
| TCP FIN (Nmap) | 12 ports | 0 open |
| TCP NULL (Nmap) | 12 ports | 0 open |
| UDP (Nmap) | 8 common ports | 0 open |

**Evidence:**
- `evidence/port-discovery_45.156.140.97-99.nmap`
- `masscan_results.xml`
- `discovery.txt`
- `evidence/skopje-ack-scan.nmap`
- `evidence/skopje-fin-scan.nmap`
- `evidence/skopje-null-scan.nmap`
- `evidence/skopje-udp-scan.nmap`

**Impact:**
The Skopje hosts present zero externally visible attack surface from the current testing position. This does not confirm the absence of internally accessible services, nor does it confirm that the hosts are not vulnerable to attacks that would originate from inside the network.

**Recommendation:**
Document these hosts as requiring internal network access for assessment. No further external reconnaissance is likely to produce additional results.

---

## 5. Attack Surface Summary

### 5.1 Exposed Services
No externally reachable services were identified on either Skopje host. All TCP and UDP ports probed returned either **filtered** or **open|filtered** states.

### 5.2 Inaccessible Services
The following service types were probed but not reachable:

| Service Category | Ports Probed |
|-----------------|--------------|
| Web (HTTP/HTTPS) | 80, 443, 8080, 8443 |
| Remote Access (SSH/RDP) | 22, 3389 |
| File Sharing (SMB) | 139, 445 |
| Email (SMTP/POP3) | 21, 25, 110 |
| DNS | 53 |
| UDP Services | 53, 161, 162, 500, 514, 1434, 1900, 4500 |

### 5.3 Network Exposure Level
The Skopje subnet presents a **minimal external attack surface**. The only observed interaction from the external testing position was ICMP echo response. No application-layer services were reachable.

### 5.4 Perimeter Filtering Observations
The filtering device consistently drops all unsolicited inbound TCP and UDP traffic. The following observations are based on the scan results:

- ICMP echo requests are permitted through the filter
- TCP packets with any flag combination (SYN, ACK, FIN, NULL) are dropped
- UDP datagrams are dropped
- The firewall does not respond with ICMP unreachable or TCP RST packets
- The behavior is consistent across both hosts, suggesting a shared filtering device or identical configuration

These observations are consistent with a stateful firewall configured to permit only established-connection traffic and ICMP echo requests.

---

## 6. Evidence Mapping

The following table maps each finding or observation to the specific evidence file that supports it.

| Finding / Observation | Evidence File | Reference |
|----------------------|---------------|-----------|
| 2 hosts alive in Skopje subnet | `evidence/host-discovery_45.156.140.96-29.nmap` | Lines 2–5 |
| 6 hosts did not respond to ICMP | `evidence/host-discovery_45.156.140.96-29.nmap` | Lines 2–5 |
| Top 100 TCP ports filtered on both hosts | `evidence/port-discovery_45.156.140.97-99.nmap` | Lines 2–10 |
| Full TCP port scan (0–65535) found 0 open ports | `masscan_results.xml` | Scan output |
| Common ports masscan found 0 open ports | `discovery.txt` | Scan output |
| TCP ACK scan: all 12 ports filtered | `evidence/skopje-ack-scan.nmap` | Lines 5–17, 22–34 |
| TCP FIN scan: all 12 ports open\|filtered | `evidence/skopje-fin-scan.nmap` | Lines 5–17, 22–34 |
| TCP NULL scan: all 12 ports open\|filtered | `evidence/skopje-null-scan.nmap` | Lines 5–17, 22–34 |
| UDP scan: all 8 ports open\|filtered | `evidence/skopje-udp-scan.nmap` | Lines 5–13, 17–25 |
| Hosts identified as firewalled in assessment documentation | `active-recon.md` | Lines 55–83 |
| Skopje hosts classified as Low risk | `weekly-report-1.md` | Section 3 |
| Skopje hosts listed in target inventory | `targets.md` | Infrastructure Targets section |
| Skopje hosts listed in service inventory | `service-inventory-day3.md` | Service Inventory Table |

---

## 7. Limitations

### 7.1 External Testing Perspective
All scanning and enumeration documented in this report was performed from an external network position. The results represent the externally visible attack surface of the Skopje hosts only.

### 7.2 Absence of Discovered Services
The absence of discovered services does not confirm the absence of services running on these hosts. Services may be accessible from internal network segments, from specific source IPs, or through VPN tunnels that were not available during this assessment phase.

### 7.3 ICMP Permitted Through Filter
The observation that ICMP echo requests are permitted does not indicate that other ICMP types (such as ICMP timestamp, ICMP address mask, or ICMP redirect) are permitted. These were not tested.

### 7.4 Limited UDP Coverage
UDP scanning was limited to 8 common UDP ports. UDP scans across a wider range may produce different results, but UDP scanning is inherently unreliable and time-consuming, and the results from the tested ports are consistent with filtering rather than open services.

### 7.5 No Host Fingerprinting
Operating system fingerprinting was not possible because no TCP/IP stack responses were received from the hosts. The operating systems running on these hosts remain unknown.

---

## 8. Next Testing Recommendations

### 8.1 Internal Network Assessment (Recommended)
The primary recommendation is to conduct service enumeration from an internal network position once internal network access is obtained. This should include:
- Full TCP and UDP port scans from the internal network
- Service version detection on any discovered open ports
- Operating system fingerprinting

### 8.2 Authenticated Service Validation
If credentials are obtained for the Skopje hosts (for example, domain credentials if the hosts are joined to Active Directory), authenticated scanning should be performed to identify:
- Missing security patches
- Local misconfigurations
- Exposed sensitive data

### 8.3 Configuration Review
If the firewall or filtering device configuration is available for review, the ruleset should be examined to confirm:
- Whether the filtering is intentional and correctly configured
- Whether there are any allow-listed sources that were not used during testing
- Whether logging is enabled to detect scanning activity

### 8.4 Vulnerability Validation
If internal network access reveals accessible services, those services should be tested for known vulnerabilities using:
- Vulnerability scanning
- Manual verification of critical findings
- Exploitability assessment (non-destructive, within scope)

### 8.5 No Further External Reconnaissance
Based on the consistent filtering behavior observed across multiple scanning techniques, no further external reconnaissance against the Skopje hosts is recommended. Additional scanning from the external position is unlikely to produce different results.

---

**End of Report**

*Prepared as part of the Global Company Internal Infrastructure Security Assessment.*  
*All evidence referenced in this report is available in the `evidence/` directory of the assessment repository.*