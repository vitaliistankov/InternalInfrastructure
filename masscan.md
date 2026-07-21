Skopie 45.156.140.97/29 (8 IP addresses, usually 6 usable hosts)


# 1. Basic TCP port discovery (common ports)
sudo masscan 45.156.140.97/29 -p1-1000 --rate 1000
-p1-1000 → scan ports 1–1000
--rate 1000 → send 1000 packets/sec (safe for small networks)
---------------------------------------------------------------
┌──(vstankov㉿PC-1429-Kali)-[~/SecurityProjects/Assessments/GlobalCompany/InternalInfrastructure]
└─$ sudo masscan 45.156.140.97/29 -p1-1000 --rate 1000                                                              
[sudo] password for vstankov: 
Starting masscan 1.3.2 (http://bit.ly/14GZzcT) at 2026-07-20 19:52:32 GMT
Initiating SYN Stealth Scan
Scanning 8 hosts [1000 ports/host]
                                                                             
┌──(vstankov㉿PC-1429-Kali)-[~/SecurityProjects/Assessments/GlobalCompany/InternalInfrastructure]
└─$     

----------------------------------------------------

# 2. Full TCP port scan
sudo masscan 45.156.140.97/29 -p0-65535 --rate 5000

For a /29, this is only 8 hosts × 65535 ports ≈ 524k probes, so it completes quickly.


-----------------------------------------------
# 3. Save results in Nmap-compatible format
sudo masscan 45.156.140.97/29 \
-p0-65535 \
--rate 5000 \
-oX masscan_results.xml

Then import into Nmap:

nmap -iL <(grep "addr" masscan_results.xml)

or use the discovered ports for targeted enumeration.

-------------------------------------------

# 4. Recommended professional workflow

- Phase 1 — Fast discovery
    sudo masscan 45.156.140.97/29 \
    -p80,443,22,21,25,53,110,139,445,3389,8080 \
    --rate 1000 \
    -oL discovery.txt

- Phase 2 — Validate with Nmap

Example:

    sudo nmap -sS -sV -sC \
    -p80,443,22,445 \
    192.168.10.1-6

Masscan is optimized for speed; Nmap provides:

- service detection
- banner grabbing
- OS detection
- NSE vulnerability checks

--------------------------------------------------------------------

# 5. If scanning an external /29 (public IP range)

Example:

203.0.113.0/29

I would avoid maximum speed and use:

    sudo masscan 45.156.140.97/29 \
    -p0-65535 \
    --rate 1000 \
    --wait 10 \
    -oJ external_scan.json

Options:

--wait 10 → wait for delayed responses
-oJ → JSON output for reporting/automation

# 6. Add source IP/interface (common in professional engagements)

    sudo masscan 192.168.10.0/29 \
    -p0-65535 \
    --rate 5000 \
    --source-ip 192.168.10.50 \
    --interface eth0

-- Real pentest report workflow:

Masscan → Nmap validation → Service enumeration → Vulnerability assessment → Exploitation validation

For a /29, the bottleneck is not scanning speed; the important part is accurate validation and evidence collection.