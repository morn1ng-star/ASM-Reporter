<div align="center">

```
   █████╗ ███████╗███╗   ███╗
  ██╔══██╗██╔════╝████╗ ████║
  ███████║███████╗██╔████╔██║
  ██╔══██║╚════██║██║╚██╔╝██║
  ██║  ██║███████║██║ ╚═╝ ██║
  ╚═╝  ╚═╝╚══════╝╚═╝     ╚═╝
  ██████╗ ███████╗██████╗  ██████╗ ██████╗ ████████╗███████╗██████╗
  ██╔══██╗██╔════╝██╔══██╗██╔═══██╗██╔══██╗╚══██╔══╝██╔════╝██╔══██╗
  ██████╔╝█████╗  ██████╔╝██║   ██║██████╔╝   ██║   █████╗  ██████╔╝
  ██╔══██╗██╔══╝  ██╔═══╝ ██║   ██║██╔══██╗   ██║   ██╔══╝  ██╔══██╗
  ██║  ██║███████╗██║     ╚██████╔╝██║  ██║   ██║   ███████╗██║  ██║
  ╚═╝  ╚═╝╚══════╝╚═╝      ╚═════╝ ╚═╝  ╚═╝   ╚═╝   ╚══════╝╚═╝  ╚═╝
```

# asm-reporter

#### Bash script for quickly running external [attack surface management](https://en.wikipedia.org/wiki/Attack_surface) recon on an organization and turning the results into one HTML report.

[![Bash](https://img.shields.io/badge/made%20with-bash-1f425f.svg)](https://www.gnu.org/software/bash/)
[![License](https://img.shields.io/badge/license-MIT-_red.svg)](LICENSE)
[![Contributions](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/<your-username>/asm-reporter/issues)
[![Platform](https://img.shields.io/badge/platform-linux-lightgrey.svg)](#installation)
[![X](https://img.shields.io/twitter/follow/star_m0rn1ng?style=social)](https://x.com/star_m0rn1ng)

<p align="center">
  <a href="#features">Features</a> •
  <a href="#installation">Installation</a> •
  <a href="#usage">Usage</a> •
  <a href="#configuring-asm-reporter">Configuring</a> •
  <a href="#running-asm-reporter">Running asm-reporter</a> •
  <a href="#acknowledgements">Acknowledgements</a>
</p>

</div>

---

# Features

<!-- Add a screenshot here: ![asm-reporter](docs/terminal.png) -->

- **WHOIS** and **DNS** records, cleaned to the useful fields only
- **Technology fingerprinting** and **HTTP / security header** checks
- **Port and service scan** (open ports only)
- **Subdomain enumeration** from subfinder, assetfinder, CertSpotter (certificate transparency) and the Wayback Machine
- **Shadow asset detection** (`old`, `dev`, `test`, `staging`, `backup`, ...)
- **ASN mapping** plus **related ASNs** (upstream and downstream) from RIPEstat
- **Alive host probing** with status code, title and technologies
- **Employee names and username formats** from LinkedIn
- **Animated terminal UI** with per-task spinners and live timers
- **Dark HTML report** with every raw output file kept next to it

# Installation

asm-reporter is a single Bash script and needs a Linux system (tested on Kali, Ubuntu and Debian style distributions) with the tools below installed.

```sh
git clone https://github.com/<your-username>/asm-reporter.git
cd asm-reporter
chmod +x asm_reporter.sh
pip install -r requirements.txt
```

### Dependencies

| Tool | Used for | Install |
|------|----------|---------|
| `curl`, `jq` | API calls, JSON parsing | `sudo apt install curl jq` |
| `whois` | WHOIS lookup | `sudo apt install whois` |
| `dig` | DNS records | `sudo apt install dnsutils` |
| [`whatweb`](https://github.com/urbanadventurer/WhatWeb) | Technology fingerprinting | `sudo apt install whatweb` |
| [`nmap`](https://nmap.org) | Port and service scan | `sudo apt install nmap` |
| [`subfinder`](https://github.com/projectdiscovery/subfinder) | Subdomain enumeration | `go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest` |
| [`assetfinder`](https://github.com/tomnomnom/assetfinder) | Subdomain enumeration | `go install github.com/tomnomnom/assetfinder@latest` |
| [`asnmap`](https://github.com/projectdiscovery/asnmap) | ASN and IP range mapping | `go install github.com/projectdiscovery/asnmap/cmd/asnmap@latest` |
| [`httpx-toolkit`](https://github.com/projectdiscovery/httpx) | Alive host probing | Kali: `sudo apt install httpx-toolkit` |
| [`linkedin2username`](https://github.com/initstring/linkedin2username) | Employee names and usernames | `git clone https://github.com/initstring/linkedin2username` |

Go tools need Go 1.21 or later (`sudo apt install golang`) and `~/go/bin` in your `PATH`:

```sh
export PATH=$PATH:~/go/bin
```

On systems other than Kali, install ProjectDiscovery's `httpx` and symlink it as `httpx-toolkit`, or edit the `t_alive` function in the script.

# Usage

```sh
./asm_reporter.sh
```

The script is interactive. It asks for two inputs:

```console
root@asm:~# Enter company name > Example Inc
root@asm:~# Enter domain/IP address > example.com
```

There are no command line flags. Behaviour is controlled with environment variables:

```
ENVIRONMENT VARIABLES:
   LI2U_PATH            path to linkedin2username.py
                        (default: /home/sourabh/Desktop/tools/linkedin2username/linkedin2username.py)
   CERTSPOTTER_TOKEN    CertSpotter API token for higher rate limits (optional)
```

# Configuring asm-reporter

### linkedin2username

The human assets module needs a copy of [linkedin2username](https://github.com/initstring/linkedin2username) and a LinkedIn account. Point the script at it:

```sh
export LI2U_PATH="/path/to/linkedin2username/linkedin2username.py"
```

This is the only interactive step. The spinner is switched off and you log in to LinkedIn when prompted. After the lookup the script keeps just `companyname-rawnames.txt` and `companyname-first.last.txt`.

### CertSpotter

Certificate transparency logs come from the [CertSpotter](https://sslmate.com/certspotter/) API. The free tier works without a key but is limited to about 100 requests per hour. For higher limits set an API token:

```sh
export CERTSPOTTER_TOKEN="your_api_key"
```

### subfinder

subfinder returns far more results with provider API keys. Add them to its config file (`~/.config/subfinder/provider-config.yaml`).

# Running asm-reporter

asm-reporter runs these modules in order, one spinner per task:

| # | Module | Tool / source | Output file |
|---|--------|---------------|-------------|
| 1 | WHOIS | `whois` | `whois.txt` (`whois_raw.txt` has the full output) |
| 2 | DNS records | `dig` | `dns_a.txt`, `dns_aaaa.txt`, `dns_ns.txt`, `dns_mx.txt`, `dns_cname.txt`, `dns_txt.txt`, `dns_soa.txt`, `dns_ptr.txt` |
| 3 | Technology fingerprinting | `whatweb` | `whatweb.txt` |
| 4 | HTTP headers and security headers | `curl` | `headers.txt` |
| 5 | Network services | `nmap` | `nmap_scan.txt` |
| 6 | Subdomain enumeration | `subfinder` | `subfinder.txt` |
| 7 | Subdomain enumeration | `assetfinder` | `assetfinder.txt` |
| 8 | Certificate transparency | CertSpotter API | `ct_logs.txt` |
| 9 | Wayback Machine | Internet Archive CDX API | `wayback.txt` |
| 10 | Merge subdomains | `sort -u` | `final.txt` |
| 11 | Shadow asset detection | `grep` | `shadow_assets.txt` |
| 12 | ASN details | `asnmap` | `asn_raw.json`, `asn_details.txt` |
| 13 | Related ASNs | RIPEstat API, `jq` | `asn_related.txt`, `asn_related_list.txt` |
| 14 | Alive subdomains | `httpx-toolkit` | `subdomains_alive.txt` |
| 15 | Human assets | `linkedin2username` | `human_rawnames.txt`, `human_first.last.txt` |
| 16 | HTML report | built in | `report.html` |

### Default Run

```console
$ ./asm_reporter.sh

root@asm:~# Enter company name > Example Inc
root@asm:~# Enter domain/IP address > example.com

[*] Target locked: example.com  (Example Inc)
[*] Initializing recon modules...

[01/16] ✔ WHOIS scan                          DONE  3s
[02/16] ✔ DNS records                         DONE  1s
[03/16] ✔ Technology fingerprinting           DONE  6s
[04/16] ✔ HTTP headers & security             DONE  2s
[05/16] ✔ Network services (nmap)             DONE  41s
[06/16] ✔ Subfinder enumeration               DONE  58s
...
[16/16] ✔ Building HTML report                DONE  0s

  ╔═════════════════════════════════════════════╗
  ║  [+] ASM report generated successfully!     ║
  ║  HTML report : report.html                  ║
  ║  Output dir  : asm_report_example.com       ║
  ╚═════════════════════════════════════════════╝
```

A task that fails or finds nothing is marked `FAILED/SKIPPED` and the script carries on with the next one.

### Output

Everything is written to `asm_report_<target>/`:

```
asm_report_example.com/
├── report.html              <- open this
├── whois.txt
├── dns_*.txt
├── whatweb.txt
├── headers.txt
├── nmap_scan.txt
├── subfinder.txt
├── assetfinder.txt
├── ct_logs.txt
├── wayback.txt
├── final.txt
├── shadow_assets.txt
├── asn_raw.json
├── asn_details.txt
├── asn_related.txt
├── asn_related_list.txt
├── subdomains_alive.txt
├── human_rawnames.txt
├── human_first.last.txt
└── li2u-output/
```

### HTML report

```sh
xdg-open asm_report_example.com/report.html
```

The report uses a dark, terminal style theme. Long lines scroll sideways and long lists scroll inside their block, so nothing is cut off. The nmap, subdomain, alive host and header sections link to the complete raw files.

### Using with other PD projects

The output files are plain text, so they chain straight into other tools:

- Probe the merged subdomains yourself:

  ```sh
  httpx-toolkit -l final.txt -silent -status-code -title
  ```

- Map the IP ranges of every related ASN:

  ```sh
  cat asn_related_list.txt | asnmap -silent
  ```

- Scan the alive hosts for vulnerabilities with [nuclei](https://github.com/projectdiscovery/nuclei):

  ```sh
  awk '{print $1}' subdomains_alive.txt | nuclei -silent
  ```

# Troubleshooting

| Problem | Fix |
|---------|-----|
| A task shows `FAILED/SKIPPED` | The tool is missing or returned no results. Run its command by hand to see the error |
| `command not found: subfinder` or `asnmap` | Add Go binaries to your path: `export PATH=$PATH:~/go/bin` |
| Certificate transparency fails | CertSpotter rate limit reached. Wait an hour or set `CERTSPOTTER_TOKEN` |
| LinkedIn step produces no files | Check `LI2U_PATH`, complete the login, and make sure the company name matches the LinkedIn page |
| Related ASNs are empty | `asnmap` found no ASN for the target, which is common for domains behind a CDN |

# Legal disclaimer

asm-reporter runs **active scans** (nmap, HTTP probing). Use it **only** on assets you own or have **written permission** to test. Unauthorized scanning may be illegal in your country. You are responsible for following all applicable laws, scope agreements and third-party terms of service, including LinkedIn's. The author accepts no liability for misuse or damage.

# Acknowledgements

- [ProjectDiscovery](https://projectdiscovery.io) for `subfinder`, `asnmap` and `httpx`. This README layout follows their [asnmap](https://github.com/projectdiscovery/asnmap) README.
- [Tom Hudson](https://github.com/tomnomnom) for `assetfinder`.
- [initstring](https://github.com/initstring) for `linkedin2username`.
- [RIPE NCC](https://stat.ripe.net) for the free RIPEstat data API.
- [SSLMate](https://sslmate.com) for the CertSpotter certificate transparency API.
- The [Internet Archive](https://archive.org) for the Wayback Machine CDX API.

---

<div align="center">

**asm-reporter** is made by [m0rning_star](https://x.com/star_m0rn1ng) and distributed under the [MIT License](LICENSE).

[![X](https://img.shields.io/badge/follow-@star__m0rn1ng-000000?logo=x&logoColor=white)](https://x.com/star_m0rn1ng)

</div>
