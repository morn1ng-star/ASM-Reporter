# ASM Reporter - requirements
# Author: m0rning_star | X: @star_m0rn1ng
#
# The main script (asm_reporter.sh) is Bash. Only the LinkedIn step needs
# Python packages. Install them with:  pip install -r requirements.txt
# Note: the dependencies of linkedin2username may change, so also check the
# requirements.txt inside your linkedin2username folder.

# --- Python (for linkedin2username) ---
requests
selenium

# ====================================================================
# System tools (NOT installable with pip - install separately)
# ====================================================================
# Debian/Kali/Ubuntu (apt):
#   bash  curl  jq  whois  dnsutils (dig)  whatweb  nmap  python3  python3-pip
#   assetfinder  httpx-toolkit
#
# Go (ProjectDiscovery tools):
#   subfinder   -> go install -v github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
#   asnmap      -> go install github.com/projectdiscovery/asnmap/cmd/asnmap@latest
#   assetfinder -> go install github.com/tomnomnom/assetfinder@latest
#
# External:
#   linkedin2username -> https://github.com/initstring/linkedin2username
#                        (set its path with LI2U_PATH, see asm_reporter.sh)
#
# Optional:
#   CERTSPOTTER_TOKEN env var for higher certificate-transparency rate limits
