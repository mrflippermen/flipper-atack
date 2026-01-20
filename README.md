# 🎯 AUSTRALIS Red Team Arsenal
## Professional Offensive Security & Digital Forensics Training Suite

[![MITRE ATT&CK](https://img.shields.io/badge/MITRE-ATT%26CK-red)](https://attack.mitre.org/)
[![Platform](https://img.shields.io/badge/Platform-Windows-blue)](https://www.microsoft.com/windows)
[![Flipper Zero](https://img.shields.io/badge/Hardware-Flipper_Zero-orange)](https://flipperzero.one/)

---

## 📋 Overview

**AUSTRALIS** is an enterprise-grade Red Team toolkit designed for offensive security operations and digital forensics training. This suite provides realistic adversary simulation modules that generate authentic forensic artifacts, enabling Blue Team analysts to practice detection, incident response, and timeline reconstruction in controlled environments.

### 🎯 Mission

Train cybersecurity professionals through:
- **Red Team Operations**: Execute realistic attack chains using Flipper Zero BadUSB
- **Blue Team Practice**: Analyze forensic artifacts (Event Logs, Registry, USN Journal, Memory)
- **Incident Response**: Practice evidence collection, timeline analysis, and attribution

---

## 🗂️ Module Catalog

### 🔑 Credential Access

| Module | MITRE Technique | Description |
|:-------|:----------------|:------------|
| **Credential_Phishing** | [T1056.002](https://attack.mitre.org/techniques/T1056/002/) | GUI-based credential harvesting via fake prompts |
| **Credential_OS_Dump** | [T1003.002](https://attack.mitre.org/techniques/T1003/002/) | SAM/SYSTEM registry hive extraction for offline cracking |

### 🔍 Discovery

| Module | MITRE Technique | Description |
|:-------|:----------------|:------------|
| **Discovery_WiFi_Exfiltration** | [T1016](https://attack.mitre.org/techniques/T1016/) | Extract and exfiltrate saved Wi-Fi profiles/passwords |
| **Discovery_WiFi_Demo** | [T1016](https://attack.mitre.org/techniques/T1016/) | Demonstration variant with UI feedback |
| **Discovery_System_Recon** | [T1082](https://attack.mitre.org/techniques/T1082/) | Comprehensive system enumeration (OS, hardware, network, software) |

### 📦 Collection

| Module | MITRE Technique | Description |
|:-------|:----------------|:------------|
| **Collection_BrowserForensics** | [T1005](https://attack.mitre.org/techniques/T1005/) | Extract browser history, cookies, passwords (Chrome, Edge, Firefox, Opera) |
| **Collection_Audio_Surveillance** | [T1123](https://attack.mitre.org/techniques/T1123/) | Ambient audio recording via microphone |
| **Collection_Screenshot_Demo** | [T1113](https://attack.mitre.org/techniques/T1113/) | Screen capture with visual indicators |
| **Collection_Screenshot_Stealth** | [T1113](https://attack.mitre.org/techniques/T1113/) | Silent screenshot capture |

### ⏱️ Persistence

| Module | MITRE Technique | Description |
|:-------|:----------------|:------------|
| **Persistence_Keylogger** | [T1056.001](https://attack.mitre.org/techniques/T1056/001/) | Keyboard input logger with Dropbox exfiltration & timestomping |

### 🛡️ Defense Evasion

| Module | MITRE Technique | Description |
|:-------|:----------------|:------------|
| **Defense_Evasion_Cleanup** | [T1070](https://attack.mitre.org/techniques/T1070/) | Anti-forensic cleanup (logs, history, temp files, recycle bin) |

---

## 🚀 Quick Start

### Prerequisites
- **Hardware**: Flipper Zero with BadUSB capability
- **Target**: Windows 10/11 system (testing environment)
- **Exfiltration**: Dropbox API token and/or Discord webhook (optional)

### Deployment Workflow

1️⃣ **Configure Exfiltration** (Optional)
```powershell
# Set environment variables or edit scripts directly
$env:DROPBOX_TOKEN = "your_token_here"
$env:DISCORD_WEBHOOK = "your_webhook_url"
```

2️⃣ **Load Payload to Flipper Zero**
```
Copy DuckyScript_*.txt from desired module to Flipper Zero:
SD Card → badusb → [payload_name].txt
```

3️⃣ **Execute Attack**
- Connect Flipper Zero to target Windows machine
- Navigate to Bad USB → Select payload → Execute
- Flipper will inject keystrokes to launch PowerShell and execute module

4️⃣ **Forensic Analysis** (Blue Team)
- See [FORENSICS_GUIDE.md](FORENSICS_GUIDE.md) for artifact locations
- Practice detection using KAPE, EvtxECmd, Registry Explorer, Volatility

---

## 📚 Documentation

- **[DEPLOYMENT.md](DEPLOYMENT.md)** - Detailed deployment instructions & configuration
- **[FORENSICS_GUIDE.md](FORENSICS_GUIDE.md)** - Blue Team artifact analysis guide
- **[MODULES.md](MODULES.md)** - Complete module reference with usage examples
- **[SECURITY.md](SECURITY.md)** - Security policy & responsible disclosure

---

## 🧪 Forensic Artifacts Generated

Each module creates specific forensic evidence for training:

| Artifact Type | Detection Method | Modules |
|:--------------|:-----------------|:--------|
| **Event ID 4104** (PowerShell Script Block) | Windows Event Logs | All modules |
| **Prefetch Files** | `C:\Windows\Prefetch\POWERSHELL.EXE-*.pf` | All modules |
| **USN Journal Flood** | File system timeline anomalies | Discovery_System_Recon |
| **Alternate Data Streams** | `Get-Item -Stream *` | Credential_Phishing |
| **Timestomping** | USN Journal vs $MFT timestamp mismatch | Persistence_Keylogger |
| **ShellBags** | `UsrClass.dat` registry analysis | Collection_BrowserForensics |

---

## ⚖️ Legal & Ethical Use

> [!CAUTION]
> **AUTHORIZED TRAINING USE ONLY**
> 
> This toolkit is designed for:
> - ✅ Controlled laboratory environments
> - ✅ Authorized penetration testing with written permission
> - ✅ Digital forensics training and education
> - ✅ Red Team vs Blue Team exercises
> 
> **NEVER** use on systems without explicit authorization.
> Unauthorized use may violate computer fraud laws (CFAA, Computer Misuse Act, etc.)

**Disclaimer**: The authors are not responsible for misuse. Users assume full legal responsibility for their actions.

---

## 🤝 Contributing

Contributions welcome! Please:
1. Follow PowerShell Verb-Noun naming conventions
2. Use MITRE ATT&CK framework for categorization
3. Document forensic artifacts created
4. Include proper README.md per module

---

## 📞 Support & Contact

- **Issues**: Submit via GitHub Issues
- **Security**: See [SECURITY.md](SECURITY.md) for vulnerability reporting
- **Documentation**: Check [MODULES.md](MODULES.md) for detailed usage

---

**Built for the AUSTRALIS Forensic Team** 🦘🔒
