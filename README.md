# 🇳🇬 Nigerian Phishing Kit Hunter v3.0

> **AI-Powered • PhaaS-Aware • Nigeria-Focused**  
> Real-time detection of phishing kits targeting Nigerian financial institutions, government agencies, and critical infrastructure.

---

## What This Is

Nigeria is under active cyber siege. In early 2026 alone:

- **ByteToBreach** breached Sterling Bank via RCE, exfiltrated Remita's entire KYC database (588GB), ransomwared Ikeja Electric, and dumped 25 million CAC corporate records
- **NullSec Nigeria** leaked EFCC law enforcement operative data including names, phone numbers, and password hashes
- Global PhaaS kits (Tycoon2FA, Xiū gǒu, Salty 2FA, EvilTokens) are actively evolving to target Nigerian banks and fintech

This tool was built to fight back. It hunts phishing kits impersonating Nigerian brands — **before victims get hit**.

---

## How It Works

You give it a legitimate brand domain (e.g. `gtbank.com`). It finds every fake domain attackers built to impersonate it.

```
INPUT: gtbank.com (legitimate brand)
         ↓
DISCOVERY
  ├── 1,900+ typosquat variants generated (homoglyphs, keyword combos, PhaaS patterns)
  ├── Certificate Transparency logs (crt.sh + certspotter) — new certs = new phishing domains
  ├── Favicon hash (MMH3) hunted across Shodan + FOFA — finds clones with NO brand in domain
  ├── URLScan.io + PhishTank + OpenPhish feed checks
  ├── 100-thread DNS resolution — only live domains proceed
  └── Reverse IP hunting on confirmed phishing servers
         ↓
6-STAGE CLASSIFICATION (on every live fake domain)
  ├── Stage 1: Domain heuristics — TLD, entropy, brand signals, keyword patterns
  ├── Stage 2: Favicon hash comparison — cloned favicon = confirmed impersonation
  ├── Stage 3: Behavioral — login forms, Nigerian brand words, trampoline JS unwrapper
  ├── Stage 4: AiTM detection — WebSocket relay, iframe proxy, cookie theft, OTP interception
  ├── Stage 5: Kit confirmation — ZIP archive download, YARA scanning, PHP deobfuscation
  └── Stage 6: Exfiltration mapping — Telegram bot tokens (live verified), Discord webhooks, crypto wallets
         ↓
OUTPUT
  ├── Confidence score (0–100%) + severity (critical/high/suspicious)
  ├── Full IOC extraction (emails, Telegram tokens, BTC/ETH wallets, Nigerian phone numbers)
  ├── STIX2 bundle + MISP event export
  └── React dashboard with live scan log, alerts, IOC feeds
```

---

## Threat Intelligence Context

From the [Deep Research Report](./deep-research-report.md):

| Date | Target | Actor | Method | Impact |
|------|--------|-------|--------|--------|
| Mar 2026 | Sterling Bank | ByteToBreach | CVE-2025-55182 RCE | BVN/NIN data exfiltrated |
| Mar 2026 | Remita | ByteToBreach | AWS S3 misconfiguration | 588GB KYC + HSM keys for 46 banks |
| Apr 2026 | Ikeja Electric | ByteToBreach | PHP webshell upload | Ransomware on 50 hosts |
| Apr 2026 | CAC | ByteToBreach | Unknown exploit | 25M corporate records |
| Apr 2026 | EFCC | NullSec Nigeria | Data scraping | Operative names + password hashes |

**Key finding:** ByteToBreach explicitly uses no phishing — pure technical exploitation. But global PhaaS operators ARE targeting Nigerian brands. This tool focuses on that threat.

---

## Targets Monitored

**Banking:** Access Bank, GTBank, Zenith Bank, First Bank, UBA, Fidelity, Stanbic IBTC, Wema, Union, Ecobank

**Fintech:** Flutterwave, Paystack, OPay, PalmPay, Moniepoint, Kuda, Interswitch, Monnify

**Government:** CBN, NIMC, JAMB, INEC, NNPC, CAC, FIRS

**Telco:** MTN Nigeria, Airtel Nigeria, Glo, 9mobile

---

## Quick Start

```bash
# Clone
git clone https://github.com/iampopg/Nigeria-APT-Hunt
cd Nigeria-APT-Hunt

# Install dependencies
pip install -r requirements.txt

# Copy and fill in your API keys
cp .env.example .env
nano .env

# Hunt phishing kits targeting Access Bank
python main.py single --domain accessbankplc.com

# Hunt all built-in Nigerian targets
python main.py multi

# Investigate a suspicious domain you found
python main.py investigate --domain login-gtbank-ng.xyz --brand GTBank

# Extract IOCs from a suspicious URL
python main.py ioc --url https://suspicious-site.com

# Start the full dashboard
python backend/main.py &
cd frontend && npm install && npm run dev
# Open http://localhost:5173
```

---

## CLI Commands

| Command | Description |
|---------|-------------|
| `single --domain gtbank.com` | Hunt fakes impersonating one brand |
| `multi` | Hunt all 29 built-in Nigerian targets |
| `multi --file targets.txt` | Hunt from your own target list |
| `company --name "GTBank"` | Auto-match and hunt by company name |
| `actor --actor ByteToBreach` | Hunt targets known to be attacked by this actor |
| `investigate --domain evil.xyz --brand GTBank` | Classify a specific suspicious domain |
| `ioc --url https://site.com` | Extract IOCs from any URL |
| `favicon --domain gtbank.com` | Get favicon hash + hunt clones on Shodan/FOFA |
| `groq --domain evil.com` | Groq AI deep analysis |
| `telegram --seeds channel1,channel2` | Telegram snowball channel discovery |
| `darkweb --onion http://forum.onion` | Dark web scan via Tor |

---

## API Keys Needed

All have free tiers. Add to `.env`:

| Service | Purpose | Free Tier | Link |
|---------|---------|-----------|------|
| Groq | AI analysis (llama3-8b) | 14,400 req/day | [console.groq.com](https://console.groq.com) |
| Shodan | Favicon hash hunting | Limited | [account.shodan.io](https://account.shodan.io) |
| Censys | CT log + TLS scanning | 250 queries/mo | [search.censys.io](https://search.censys.io/account/api) |
| URLScan.io | URL behavioral analysis | 5,000 scans/mo | [urlscan.io](https://urlscan.io/user/profile/) |
| VirusTotal | Domain reputation | 500 req/day | [virustotal.com](https://www.virustotal.com/gui/my-apikey) |
| AbuseIPDB | IP reputation | 1,000 checks/day | [abuseipdb.com](https://www.abuseipdb.com/account/api) |
| Telegram | Snowball monitoring | Free | [my.telegram.org/apps](https://my.telegram.org/apps) |

---

## Architecture

```
Nigeria-APT-Hunt/
├── main.py                    # CLI entry point
├── backend/main.py            # FastAPI server (connects to frontend)
├── frontend/                  # React dashboard (Vite)
├── src/
│   ├── config/settings.py     # API keys, Nigerian targets, threat actors
│   ├── input/modes.py         # 4 input modes: single, multi, company, actor
│   ├── discovery/             # Domain gen, CT logs, favicon hunt, Shodan, Censys, URLScan
│   ├── classification/        # 6-stage pipeline (prefilter → visual → behavioral → AiTM → kit → exfil)
│   ├── extraction/            # YARA scanner, PHP deobfuscator, IOC extractor
│   ├── ai/                    # Groq client, URL classifier (sklearn RF), domain predictor
│   └── output/                # STIX2, MISP exporter, JSON reports
├── rules/nigerian_phishing.yar  # YARA rules for 6 kit families
└── .env.example               # API key template
```

---

## YARA Rules

Six rules covering known kit families targeting Nigeria:

- `EvilTokens_Nigeria` — AiTM kit with Telegram exfiltration targeting Nigerian banks
- `Salty2FA_Nigeria` — Trampoline scripts with Nigerian brand targeting
- `Generic_Nigerian_Phishing` — Broad Nigerian financial service targeting
- `PhaaS_Backdoor` — Developer backdoors in phishing kits
- `Tycoon2FA_Kit` — CAPTCHA bypass + AiTM
- `XiuGou_FastFlux` — CDN fast-flux hosting (Cloudflare Workers, Glitch)

---

## Dashboard

The React frontend connects to the FastAPI backend and shows:

- **Command Center** — live metrics, detection history chart, kit family distribution, pipeline funnel
- **Alerts** — real-time phishing alerts with severity, confidence, kit family, exfil method
- **Targets** — scan launcher with live log console and results table
- **IOC Feeds** — extracted Telegram tokens, emails, crypto wallets, phone numbers, C2 domains
- **Settings** — API key configuration

---

## Contributing / Joining the Project

This project is actively being built and I am **looking for collaborators**.

If you are a:
- Threat intelligence analyst
- Malware researcher
- Python / security tooling developer
- Nigerian cybersecurity professional
- Anyone who cares about protecting Nigerian digital infrastructure

**Please reach out: [iampopg@proton.me](mailto:iampopg@proton.me)**

I am serious about this. Nigeria's institutions are being hit hard and the defensive tooling is years behind the attackers. If you want to help build something that actually matters, let's talk.

Areas where help is most needed:
- Training data for the visual classifier (phishing page screenshots)
- Nigerian-specific YARA rule development
- Threat actor tracking and IOC correlation
- Integration with MISP community feeds
- Testing against real phishing kits

---

## Research

The [Deep Research Report](./deep-research-report.md) documents the 2026 Nigerian APT campaign in detail — attack vectors, MITRE TTPs, IOCs, lateral movement techniques, and recommended mitigations.

The [Phishing Research Report](./phishing-deep-research-report.md) covers the PhaaS ecosystem targeting Nigerian brands.

---

## Legal

This tool is for **defensive security research only**. Use it to protect organizations you are authorized to defend. Do not use it to attack systems you do not own.

---

## License

MIT

---

*Built with urgency. Nigeria's infrastructure cannot wait.*
