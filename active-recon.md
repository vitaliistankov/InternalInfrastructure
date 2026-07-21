For an authorized internal penetration test, Kali Linux includes a wide range of tools that can be used during the active reconnaissance phase. Active reconnaissance means you are sending traffic to the target to identify hosts, services, technologies, or configurations.

Here's a practical breakdown by purpose.

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


------------------------------------

# Recon Workflow

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
- Produce a table of exposed syst
------------------------------------------------------

# Example Deliverable

Rather than simply listing open ports, a useful deliverable is an inventory like:

| IP Address | Hostname | OS (Estimated) | Open Services       | Criticality | Notes                       |
| ---------- | -------- | -------------- | ------------------- | ----------- | --------------------------- |
| 10.x.x.x   | SERVER01 | Windows Server | SMB, LDAP, Kerberos | High        | Domain Controller candidate |
| 10.x.x.x   | WEB01    | Linux          | HTTPS               | Medium      | Internal web application    |
| 10.x.x.x   | FILE01   | Windows        | SMB                 | High        | File server                 |


# Good practice during an enterprise assessment

Even with authorization:

- Start with low-impact discovery before increasing scan intensity.
- Coordinate any intensive scanning with the agreed Rules of Engagement.- 
- Record when you scanned, what you scanned, and why, so findings are traceable and reproducible.
- Save scan results in structured formats (XML/JSON where supported) so they can be incorporated into your final report and evidence package.