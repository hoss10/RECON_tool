•	Developed a Python-based reconnaissance automation tool that performs subdomain enumeration, live host detection, VirusTotal enrichment, secret discovery, and automated reporting. 

•	Integrated multiple open-source tools to automate bug bounty reconnaissance, reducing manual effort and improving target coverage. 

•	Implemented automated 403 bypass testing against restricted endpoints to identify potential access control weaknesses. 


# HANFY RECON v7.0

> **Unified Recon Tool with Built-in VirusTotal**  
> Subdomain enumeration · Status codes · VirusTotal API · URL secrets · 403 bypass · Unified reports

![Version](https://img.shields.io/badge/version-7.0-blue)
![Python](https://img.shields.io/badge/python-3.6%2B-green)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

---

## 📖 Description

**HANFY RECON** is an all-in-one reconnaissance pipeline for bug bounty hunters, penetration testers, and red teamers.  
It automates subdomain discovery, live host probing, VirusTotal reputation checks, secret scanning in URLs, and 403 bypass attempts — then generates clean, ranked reports ready for submission.

The tool is designed to be fast, modular, and easy to use. It integrates popular community tools (`subfinder`, `assetfinder`, `httpx`, `dnsx`, `amass`, `nomore403`) and adds a **built-in VirusTotal API scanner** with multi-key rotation so you don’t need external VT utilities.

---

## ✨ Features

- **Subdomain Enumeration** – `subfinder`, `assetfinder`, `crt.sh`, optional `amass` deep passive.
- **DNS Pre‑resolution** – Auto‑detects and uses `dnsx` to filter live DNS records.
- **Live Host Probing** – `httpx` with status code, title, and technology detection.
- **Built‑in VirusTotal Scanner** – Multi‑API‑key rotation, caching, and structured JSON output.
- **URL Secrets Scanning** – Scans VT‑discovered URLs for sensitive parameters (tokens, passwords, API keys, etc.) with severity scoring.
- **403 Bypass** – Uses `nomore403` on high‑value paths (admin, api, config, etc.).
- **Unified Report** – Combines subdomain, status, and VT data in one file.
- **Master Ranked Report** – Severity‑ranked findings (Critical → Low).
- **Bug Bounty Ready Report** – Clean, submission‑ready findings with score ≥ 60.
- **Wildcard Detection** – Alerts you to wildcard DNS to avoid false positives.
- **Logging & Caching** – Detailed logs and VT cache to avoid re‑scanning.

---

## ⚙️ Requirements

### Python
- Python 3.6 or higher
- `requests` library (`pip install requests`)

### External Tools (must be in `$PATH`)
| Tool | Purpose | Install |
|------|---------|---------|
| `subfinder` | Passive subdomain enumeration | `go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest` |
| `assetfinder` | Additional passive subdomain source | `go install github.com/tomnomnom/assetfinder@latest` |
| `httpx` | Probing live hosts & status codes | `go install -v github.com/projectdiscovery/httpx/cmd/httpx@latest` |
| `dnsx` | DNS pre‑resolution (optional) | `go install -v github.com/projectdiscovery/dnsx/cmd/dnsx@latest` |
| `amass` | Deep passive enum (optional) | `go install -v github.com/owasp-amass/amass/v3/...@master` |
| `nomore403` | 403 bypass (optional) | `go install github.com/devploit/nomore403@latest` |

> **Note:** If `dnsx` is not installed, all subdomains are passed directly to `httpx`.  
> If `httpx` is missing, the tool returns resolved subdomains without probing.

### VirusTotal API Keys
The script contains a placeholder list `VT_API_KEYS`.  
Replace the `"xxx"` values with your own **free VirusTotal API keys** (you can use multiple for rotation).

```python
VT_API_KEYS = [
    "YOUR_VT_API_KEY_1",
    "YOUR_VT_API_KEY_2",
    # ...
]
