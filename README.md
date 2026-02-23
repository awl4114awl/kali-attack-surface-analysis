# Self Network & Service Enumeration Lab (Kali Linux)

## ⓘ Overview

This lab was performed on a single Kali Linux system to simulate internal reconnaissance, service discovery, and remediation validation using Nmap. The objective was to establish a secure baseline, intentionally introduce a network service, enumerate it, interact with it, and then validate remediation by re-scanning the system.

This exercise demonstrates:

* Network service discovery
* Service fingerprinting
* Banner and header enumeration
* Process-to-port correlation
* Attack surface validation
* Remediation verification

All testing was performed against my own system in a controlled environment.

---

## Environment

| Component      | Details                     |
| -------------- | --------------------------- |
| OS             | Kali Linux                  |
| Interface      | eth0                        |
| Local IP       | 10.10.20.45                 |
| Nmap Version   | 7.91                        |
| Python Version | 3.9.7                       |
| Network Type   | Virtualized Lab Environment |

---

# Phase 1 — Baseline Enumeration

I first performed scans against localhost and the system's assigned IP to establish a baseline attack surface.

### Commands Executed

```bash
ip a
nmap 127.0.0.1
nmap 10.10.20.45
nmap -sV 10.10.20.45
nmap -A 10.10.20.45
sudo ss -tulnp
```

<p align="left">
  <img src="images/Screenshot 2026-02-22 193427.png" width="600">
</p>

### Results

* All 1000 default TCP ports were closed
* No listening services detected
* No active daemons bound to public interfaces
* SSH service not running

This confirmed a minimal exposed attack surface.

---

# Phase 2 — Service Introduction (Intentional Exposure)

To simulate real-world exposure, I launched a simple Python HTTP server bound to port 8000.

### Command Executed

```bash
python3 -m http.server 8000
```

<p align="left">
  <img src="images/Screenshot 2026-02-22 193643.png" width="600">
</p>

This intentionally created a listening web service.

---

# Phase 3 — Service Enumeration

After exposing the service, I re-ran scans to validate detection.

```bash
nmap 10.10.20.45
nmap -sV 10.10.20.45
sudo ss -tulnp
nmap -A -p 8000 10.10.20.45
curl http://10.10.20.45:8000
```

<p align="left">
  <img src="images/Screenshot 2026-02-22 193814.png" width="600">
</p>

---

### Findings

* Port 8000/tcp detected as open
* Service identified as `SimpleHTTPServer 0.6 (Python 3.9.7)`
* HTTP header enumeration confirmed
* Directory listing exposed home directory contents
* Process mapped directly to Python runtime

This demonstrates how quickly exposed services can be identified and fingerprinted.

---

# Phase 4 — Remediation Validation

I terminated the Python HTTP service:

```
Ctrl + C
```

Then re-scanned:

```bash
nmap 10.10.20.45
```

Port 8000 was no longer accessible, confirming remediation success.

<p align="left">
  <img src="images/Screenshot 2026-02-22 193928.png" width="600">
</p>
