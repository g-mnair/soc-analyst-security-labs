# Network Traffic Analysis Lab (Wireshark)

## Objective

Capture and analyze live network traffic to understand what different types of activity — normal traffic, port scanning, plaintext logins, and repeated failed authentication — actually look like at the packet level. This lab builds directly on the [Nmap Reconnaissance Lab](../01-nmap-recon), using Wireshark to observe and validate several of those earlier findings as real traffic on the wire.

## Lab Environment

| Component | Details |
|---|---|
| Attacker machine | Kali Linux (VirtualBox VM) |
| Target machine | Metasploitable2 (VirtualBox VM) |
| Network mode | Bridged Adapter |
| Target IP | 192.168.220.6 |
| Attacker IP | 192.168.220.5 |
| Capture tool | Wireshark |

---

## Methodology

### Step 1: Interface Setup

Launched Wireshark and identified the active capture interface.

```
sudo wireshark
```

**Screenshot:**
![Step 1 - Interface List](01-interface-list.png)

**Findings:**
`eth0` was confirmed as the active interface, matching the interface used throughout the Nmap lab.

---

### Step 2: Baseline Capture (ICMP)

Started a live capture on `eth0`, then generated a known traffic pattern to confirm the capture was working correctly.

**Command (separate terminal):**
```
ping -c 4 192.168.220.6
```

**Wireshark filter:**
```
icmp
```

**Screenshot:**
![Step 2 - ICMP Baseline](02-icmp-baseline.png)

**Findings:**
Four Echo Request/Reply pairs were observed between the attacker (192.168.220.5) and target (192.168.220.6), confirming the capture setup was functioning correctly before moving to more complex traffic.

---

### Step 3: Capturing an Nmap Scan

Re-ran an earlier scan from the Nmap lab while capturing, to observe what port-scanning traffic looks like at the packet level.

**Command (separate terminal):**
```
nmap -sV 192.168.220.6
```

**Wireshark filter:**
```
ip.addr == 192.168.220.6 && tcp
```

**Screenshot:**
![Step 3a - Scan Traffic](03a-scan-traffic.png)

![Step 3b - TCP Flags Detail](03b-tcp-flags-detail.png)

**Findings:**
A large number of SYN packets were sent to many destination ports within a few seconds. The target system responded with either SYN-ACK or RST-ACK, depending on whether each port was open or closed. This type of rapid activity across multiple ports is a common sign of a port scan and would usually be flagged by a SOC analyst or SIEM for investigation.

---

### Step 4: Capturing Plaintext Telnet Credentials

Connected to the target via Telnet, an inherently unencrypted protocol, to observe credential exposure at the packet level.

**Command (separate terminal):**
```
telnet 192.168.220.6
```
Logged in using Metasploitable2's default credentials (`msfadmin` / `msfadmin`).

**Wireshark filter:**
```
telnet
```

**Screenshot:**
![Step 4a - Telnet Session](04a-telnet-session.png)

Right-clicked a Telnet packet → **Follow → TCP Stream** to reconstruct the full session.

![Step 4b - Telnet Cleartext Credentials](04b-telnet-cleartext-credentials.png)

**Findings:**
The reconstructed TCP stream showed the entire Telnet session in plain text, including the username and password exactly as they were entered. This shows why Telnet should never be used for systems with sensitive data, because anyone who can capture the network traffic can easily see the login credentials.

---

### Step 5: Capturing an Anonymous FTP Login

Connected to the target's FTP service. Only anonymous login was allowed, while msfadmin/msfadmin could not be used to log in. This confirms the ftp-anon result found during the Nmap scan.

**Command (separate terminal):**
```
ftp 192.168.220.6
```
Logged in with username `anonymous`.

**Wireshark filter:**
```
ftp
```

**Screenshot:**
![Step 5a - FTP Session](05a-ftp-session.png)

Followed the TCP stream to view the full session.

![Step 5b - FTP Cleartext Session](05b-ftp-cleartext-session.png)

**Findings:**
The captured traffic showed the USER anonymous command followed by the PASS anonymous command and a 230 Login successful response. This confirms that the FTP server allowed anonymous access, matching the ftp-anon finding from the Nmap scan.

---

### Step 6: Capturing a Simulated SSH Brute-Force Attempt

Attempted to connect via SSH and deliberately entered incorrect passwords multiple times to simulate a brute-force pattern.

**Connection note:** The SSH connection failed at first because the Metasploitable2 server uses older SSH host key types (ssh-rsa and ssh-dss) that modern SSH clients disable by default. To connect successfully, the legacy host key algorithm had to be enabled manually.
```
ssh -oHostKeyAlgorithms=+ssh-rsa -oPubkeyAcceptedAlgorithms=+ssh-rsa msfadmin@192.168.220.6
```


**Terminal (failed login attempts):**
![Step 6b - Failed SSH Logins](06b-failed-ssh-logins.png)

**Wireshark filter:**
```
tcp.port == 22
```

**Screenshot:**
![Step 6c - SSH Connection Pattern](06c-ssh-connection-pattern.png)

**Findings:**
Unlike Telnet and FTP, SSH encrypts all communication, so the username and password could not be seen in the packet capture. However, the network traffic still showed many short connections to port 22, each with a TCP handshake, an encrypted key exchange, and then the connection closing. This repeated connection pattern is a common sign of a brute-force attack and is something a SOC analyst or SIEM can detect even without seeing the actual credentials.

---

### Step 7: Saved Capture File

The full session was saved as `metasploitable-capture.pcapng` and included in this repository under `captures/`, allowing the complete capture to be reviewed directly in Wireshark rather than relying on screenshots alone.

---

## Summary of Key Findings

| Finding | Protocol/Port | Risk |
|---|---|---|
| Port scan signature (SYN bursts across ports) | TCP (various) | Confirms reconnaissance activity is visible and detectable at the traffic level |
| Plaintext credentials recoverable | Telnet (23) | Full username/password exposed to anyone observing traffic |
| Anonymous FTP access confirmed via traffic | FTP (21) | Validates Nmap's `ftp-anon` finding at the packet level |
| Brute-force connection pattern (behavioral only) | SSH (22) | Credentials protected by encryption, but repeated connection attempts remain visible and detectable |
| Legacy SSH host key algorithms required | SSH (22) | Target runs a deprecated SSH version incompatible with modern client defaults |

## Conclusion

This lab showed how the same activity, such as port scanning or logging in, looks different on the network depending on the protocol used. Telnet and FTP exposed login credentials because they send data in plain text, while SSH encrypted the credentials but still showed suspicious connection patterns. Along with the Nmap Reconnaissance Lab, this demonstrates an important SOC concept: encryption protects the data being sent, but network traffic patterns can still be used to detect malicious activity.

## Tools Used

- Wireshark
- Kali Linux
- Metasploitable2 (Virtual Box)
