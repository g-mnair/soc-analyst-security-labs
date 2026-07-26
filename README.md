# SOC Analyst Security Labs

A collection of hands-on security labs built to practice core SOC analyst skills — network reconnaissance, traffic analysis, and vulnerability assessment — against an intentionally vulnerable target environment. Each lab is self-contained with its own methodology, findings, and evidence, and together they reflect the early stages of a real-world security assessment workflow.

## About

I'm an aspiring SOC Analyst with an EC-Council CSA certification, building practical, hands-on experience through self-directed lab work. These labs were built from scratch in a local VirtualBox environment to apply and demonstrate skills beyond what certifications alone can show — actual tool usage, real findings, and defender-focused analysis.

## Lab Environment

| Component | Details |
|---|---|
| Attacker machine | Kali Linux (VirtualBox VM) |
| Target machine | Metasploitable2 (VirtualBox VM) |
| Network mode | Bridged Adapter |

## Labs

| # | Lab | Tools | Status | Link |
|---|---|---|---|---|
| 01 | Network Reconnaissance | Nmap, arp-scan | ✅ Complete | [01-nmap-recon](./01-nmap-recon) |
| 02 | Network Traffic Analysis | Wireshark | 🔜 In Progress | [02-wireshark-analysis](./02-wireshark-analysis) |
| 03 | Vulnerability Assessment | OpenVAS | 🔜 Planned | [03-openvas-vuln-assessment](./03-openvas-vuln-assessment) |

### 01 — Network Reconnaissance (Nmap)
Performed host discovery, port scanning, service/version detection, OS fingerprinting, and vulnerability-revealing script scans against a target host. Identified and documented multiple real CVEs (vsftpd backdoor, UnrealIRCd backdoor, distccd RCE) along with misconfigurations like disabled SMB signing and anonymous FTP access.
→ [Full writeup](./01-nmap-recon/README.md)

### 02 — Network Traffic Analysis (Wireshark)
Captured and analyzed packet-level traffic to identify attack patterns such as brute-force login attempts and unencrypted credential transmission.
→ Writeup coming soon

### 03 — Vulnerability Assessment (OpenVAS)
Ran a full vulnerability scan against the target, prioritized findings by CVSS severity, and authored remediation recommendations.
→ Writeup coming soon

## Why These Labs

These three labs mirror the natural first phase of a SOC/security assessment workflow — knowing what's on the network, watching what crosses it, and knowing what's exposed on it. They're designed to build toward a combined end-to-end exercise tying reconnaissance, detection, and vulnerability management together.

## Skills Demonstrated

-Network scanning (Nmap, arp-scan)
-Traffic analysis (Wireshark)
-Vulnerability scanning (OpenVAS)
-Security documentation

## Contact

**Gayathri M Nair**
[LinkedIn](https://www.linkedin.com/in/gayathri-m-nair-2074a6204) | mnairg83@gmail.com
