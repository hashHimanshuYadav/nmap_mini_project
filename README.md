# Network Footprinting & Vulnerability Scanning Assessment

A structured internal network footprinting, host discovery, service enumeration, and web vulnerability assessment conducted against a target Linux node within an authorized laboratory environment.

---

## 📌 Executive Summary

* **Author:** Himanshu Yadav
* **Source Node (Attacker):** `192.168.64.18/24` (Kali Linux ARM64)
* **Target Node (Victim):** `192.168.64.8/24` (Ubuntu Server ARM64)
* **Scope:** Authorized Internal Lab Infrastructure
* **Session Log:** `assessment_kali_to_ubuntu.log`

This repository documents the execution of network footprinting, port mapping, service fingerprinting, and targeted web vulnerability scanning. Complete session execution was recorded using `script` to ensure verifiable artifacts for security auditing.

---

## 🛠️ Infrastructure & Setup

To establish the target surface, OpenSSH, Apache2, and a test PHP application were configured on the target host (`192.168.64.8`).

```bash
# Install and enable SSH & Apache HTTP services
sudo apt update && sudo apt install -y openssh-server apache2 php libapache2-mod-php
sudo systemctl enable --now ssh apache2

# Deploy test script
echo '<?php echo "Search result for: " . $_GET["q"]; ?>' | sudo tee /var/www/html/search.php

🚀 Execution Workflow
Phase 1: Logging & Interface Verification
script -a assessment_kali_to_ubuntu.log
ip a

Phase 2: Host Discovery
nmap -sn -PS22,80,443 -oA 01_host_discovery 192.168.64.8

⚬ Findings: Target online at 192.168.64.8 (Latency: 0.00091s | MAC: C2:D7:37:DC:41:DC).
Phase 3: Full Port Scanning
nmap -sS -p- --min-rate 1000 -oA 02_full_port_scan 192.168.64.8

⚬ Findings: Identified 2 open TCP ports: 22/tcp (SSH) and 80/tcp (HTTP).
Phase 4: Service & OS Fingerprinting
nmap -sV -O --version-intensity 5 -oA 03_service_os_detection 192.168.64.8

⚬ Findings: Identified OpenSSH daemon, Apache HTTP server, and Linux kernel 6.x.
Phase 5: Web Enumeration & Vulnerability Scanning
nmap --script http-enum,http-headers,http-methods -p 80 -oA 04_vuln_scan 192.168.64.8


⚬ Findings: Detailed banner disclosure (Apache/2.4.63 (Ubuntu)); supported methods: GET, HEAD, POST, OPTIONS.
📊 Summary of Findings
ID	Service	        Finding / Issue	                            Risk Level	                        Mitigation Recommendation
01	SSH (22/tcp)	Exposed management interface	                Low	                                Restrict via IP whitelisting; enforce public key authentication.
02	HTTP (80/tcp)	Detailed banner disclosure in HTTP header	    Low	                                Set ServerTokens Prod and ServerSignature Off in Apache config.

📂 Repository Layout
.
├──nmap_assessments
  └── logs/
      ├── assessment_kali_to_ubuntu.log     # Complete raw terminal session recording
      ├── 01_host_discovery.nmap           # Host discovery results
      ├── 02_full_port_scan.nmap           # Full 65,535 TCP port scan output
      ├── 03_service_os_detection.nmap     # OS and service fingerprinting log
      └── 04_vuln_scan.nmap                # NSE script evaluation output
