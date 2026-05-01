# 🇳🇬 Nigeria APT Hunt

> **Tracking Advanced Persistent Threats Targeting Nigerian Infrastructure**  
> AI-Powered • Multi-Vector • Nigeria-Focused

---

## What This Is

Nigeria is under active cyber siege. This project tracks, documents, and builds defensive tooling against the full spectrum of APT activity targeting Nigerian institutions — from nation-state-level exploitation to PhaaS (Phishing-as-a-Service) campaigns.

In early 2026 alone:

- **ByteToBreach** breached Sterling Bank via RCE, exfiltrated Remita's entire KYC database (588GB including HSM keys for 46 banks), ransomwared Ikeja Electric, and dumped 25 million CAC corporate records
- **NullSec Nigeria** leaked EFCC law enforcement operative data including names, phone numbers, and password hashes
- Global PhaaS kits (Tycoon2FA, Xiū gǒu, Salty 2FA, EvilTokens) are actively evolving to target Nigerian banks and fintech

This repo contains:

1. **Deep research reports** documenting the 2026 Nigerian APT campaign — attack vectors, MITRE ATT&CK TTPs, IOCs, lateral movement techniques, and recommended mitigations
2. **Phishing Kit Hunter** — an AI-powered tool that hunts phishing kits impersonating Nigerian brands in real-time (currently in active development)
3. **Threat actor profiles** — ByteToBreach, NullSec Nigeria, iProfessor
4. **IOC feeds** — extracted from confirmed phishing kits and breach data

---

## Threat Intelligence

### 2026 Campaign Timeline

| Date | Target | Actor | Method | Impact |
|------|--------|-------|--------|--------|
| Mar 2026 | Sterling Bank | ByteToBreach | CVE-2025-55182 RCE | BVN/NIN data exfiltrated |
| Mar 2026 | Remita | ByteToBreach | AWS S3 misconfiguration | 588GB KYC + HSM keys for 46 banks |
| Apr 2026 | Ikeja Electric | ByteToBreach | PHP webshell upload | Ransomware on 50 hosts |
| Apr 2026 | CAC | ByteToBreach | Unknown exploit | 25M corporate records |
| Apr 2026 | EFCC | NullSec Nigeria | Data scraping | Operative names + password hashes |

### Key Findings

- ByteToBreach explicitly uses **no phishing** — pure technical exploitation (RCE, misconfigs, credential harvesting)
- ByteToBreach demonstrated ability to **spoof Central Bank of Nigeria emails** using compromised signing keys
- Sliver C2 with MTLS encryption on port 443, 60-second beacon interval
- Remita breach exposed **HSM keys for 46 Nigerian banks** — potentially compromising the entire interbank payment system
- Global PhaaS operators ARE targeting Nigerian brands with AiTM kits that bypass MFA

---

## Research Reports

- **[Deep Research Report](./deep-research-report.md)** — Full technical analysis of the 2026 Nigerian APT campaign: attack vectors, MITRE TTPs, IOCs, C2 infrastructure, lateral movement, data exfiltration, and recommended mitigations

- **Phishing Research Report** *(coming soon)* — The PhaaS ecosystem targeting Nigerian brands: kit families, exfiltration methods, Telegram bot infrastructure

---

## Phishing Kit Hunter (In Development)

One component of this project is a real-time phishing kit detection engine. You give it a legitimate Nigerian brand domain — it finds every fake domain attackers built to impersonate it.

```
INPUT: gtbank.com (legitimate brand)
         ↓
DISCOVERY
  ├── 1,900+ typosquat variants generated
  ├── Certificate Transparency logs (crt.sh + certspotter)
  ├── Favicon hash (MMH3) hunted across Shodan + FOFA
  ├── URLScan.io + PhishTank + OpenPhish feed checks
  ├── 100-thread DNS resolution
  └── Reverse IP hunting on confirmed phishing servers
         ↓
6-STAGE CLASSIFICATION
  ├── Stage 1: Domain heuristics — TLD, entropy, brand signals
  ├── Stage 2: Favicon hash comparison — cloned favicon = impersonation
  ├── Stage 3: Behavioral — login forms, Nigerian brand words, trampoline JS
  ├── Stage 4: AiTM detection — WebSocket relay, cookie theft, OTP interception
  ├── Stage 5: Kit confirmation — ZIP download, YARA scanning, PHP deobfuscation
  └── Stage 6: Exfiltration mapping — Telegram bot tokens, Discord webhooks, crypto wallets
         ↓
OUTPUT
  ├── Confidence score (0–100%) + severity
  ├── Full IOC extraction
  ├── STIX2 bundle + MISP event export
  └── React dashboard with live scan log
```

---

## Sectors Monitored

**Banking:** Access Bank, GTBank, Zenith Bank, First Bank, UBA, Fidelity, Stanbic IBTC, Wema, Union, Ecobank

**Fintech:** Flutterwave, Paystack, OPay, PalmPay, Moniepoint, Kuda, Interswitch, Monnify

**Government:** CBN, NIMC, JAMB, INEC, NNPC, CAC, FIRS

**Telco:** MTN Nigeria, Airtel Nigeria, Glo, 9mobile

**Energy:** NNPC, Ikeja Electric, Eko Disco, TCN

**Law Enforcement:** EFCC, DSS, NIA, NPF

---

## Known Threat Actors

| Actor | TTPs | Targets | Status |
|-------|------|---------|--------|
| **ByteToBreach** | Sliver C2, Metasploit, RCE exploitation, AWS credential theft, ransomware | Banks, fintech, government, utilities | Confirmed — active |
| **NullSec Nigeria** | Forum dumps, hacktivism, data scraping | Law enforcement, government | Claimed |
| **iProfessor** | Dark web kit sales | Finance | Unverified |

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
- Threat actor tracking and IOC correlation
- Nigerian-specific YARA rule development
- Training data for the visual phishing classifier
- Integration with MISP community feeds
- Testing against real phishing kits
- Dark web monitoring for Nigerian-targeted kit sales
- Incident response playbooks for Nigerian organizations

---

## Legal

This project is for **defensive security research only**. Use it to protect organizations you are authorized to defend. Do not use it to attack systems you do not own.

---

## License

MIT

---

*Built with urgency. Nigeria's infrastructure cannot wait.*
