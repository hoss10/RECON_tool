
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
```

---

## 🚀 Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/hanfy-recon.git
   cd hanfy-recon
   ```

2. **Install Python dependencies**
   ```bash
   pip install requests
   ```

3. **Install external tools** (see table above). Make sure `$GOPATH/bin` is in your `$PATH`.

4. **Add your VirusTotal API keys** in `hanfy_recon.py`.

---

## 🖥️ Usage

```bash
python3 hanfy_recon.py -d target.com [options]
```

### Basic Examples

| Command | Description |
|---------|-------------|
| `python3 hanfy_recon.py -d target.com` | Subdomain enum + status codes |
| `python3 hanfy_recon.py -d target.com --full-recon` | Everything (subs, VT, secrets, 403, unified report) |
| `python3 hanfy_recon.py -d target.com --subs-only` | Only enumerate subdomains |
| `python3 hanfy_recon.py -d target.com --vt --vt-delay 10` | Run VT scan with 10s delay |
| `python3 hanfy_recon.py -d target.com --vt-only` | Only VT + secrets on existing subdomains |
| `python3 hanfy_recon.py -d target.com --bypass` | Enable 403 bypass |
| `python3 hanfy_recon.py -d target.com --unified-report` | Generate unified subdomain+status+VT report |

### All Options

```
-d, --domain          Target domain (required)
-o, --output          Output directory (default: hanfy-recon)
--subs-only           Only enumerate subdomains
--subs-deep           Use amass deep passive
--subs-recursive      Recursive subdomain enum
--subs-limit          Max live subdomains (0 = all)
--subs-threads        Subdomain probe threads (default: 100)
--url-secrets         Scan VT URLs for secrets
--min-score           Minimum severity score (default: 50)
--vt                  Run built-in VT API scan
--vt-only             Only VT + secrets on existing subs
--vt-delay            Delay between VT requests (default: 15s)
--vt-limit            Max subdomains to scan with VT (0 = all)
--bypass              Attempt 403 bypass
--unified-report      Generate unified subdomain+status+VT report
--full-recon          Run everything
-t, --threads         General threads (default: 50)
--delay               Request delay
```

---

## 📁 Output Structure

After a full run, the output directory looks like this:

```
hanfy-recon/
└── target.com/
    ├── subs/
    │   ├── target.com_subs_all.txt          # Merged master list
    │   ├── target.com_probe_input.txt       # http:// / https:// for httpx
    │   └── target.com_subs_live.txt         # httpx full output
    ├── vt/
    │   ├── virus.txt                        # Clean subdomains for VT
    │   ├── vt.txt                           # Raw VT output
    │   └── vt_results.json                  # Structured VT data
    ├── reports/
    │   ├── target.com_status_codes.txt      # Grouped by status code
    │   ├── target.com_unified_report.txt    # Subdomain + status + VT
    │   ├── target.com_url_secrets.txt       # VT-only secrets scan
    │   ├── target.com_403_bypass.txt        # nomore403 results
    │   ├── target.com_master_ranked.txt     # Severity-ranked combined
    │   └── target.com_bug_bounty_ready.txt  # Clean BB report (score ≥ 60)
    ├── bypassed/
    │   └── 403_bypass_results.txt
    └── logs/
        └── target.com_recon.log
```

---

## 🧪 Example Full Run

```bash
python3 hanfy_recon.py -d example.com --full-recon --vt-delay 12 --threads 80
```

This will:
1. Enumerate subdomains via `subfinder`, `assetfinder`, `crt.sh`, and `amass`.
2. Pre‑resolve with `dnsx` (if available).
3. Probe with `httpx` and collect status codes, titles, tech.
4. Scan all subdomains with VirusTotal (using your API keys).
5. Generate a unified report.
6. Scan VT URLs for secrets.
7. Attempt 403 bypass on high‑value paths.
8. Produce master ranked and bug bounty ready reports.

---

## ⚠️ Disclaimer

This tool is intended for **authorized security testing and educational purposes only**.  
Do not use it against any system you do not own or have explicit permission to test.  
The author is not responsible for any misuse or damage caused by this program.

---

## 📜 License

This project is licensed under the MIT License – see the [LICENSE](LICENSE) file for details.

---

## 🙏 Credits

- Built with ❤️ by **Hanfy**
- Uses open‑source tools from ProjectDiscovery, TomNomNom, OWASP Amass, and Devploit.
- VirusTotal API for reputation data.

---
**Happy hunting!** 🎯
```
