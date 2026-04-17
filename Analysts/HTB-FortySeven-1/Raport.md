# Threat Intelligence Report – Mysterious Elephant (APT-K-47)

**Project name:** FortySeven-1
**Date:** 17.04.2026

---

## 1. Executive Summary

This report analyzes the threat actor known as **Mysterious Elephant (APT-K-47)**, a South Asian APT group conducting targeted cyber-espionage operations. The group primarily targets government and diplomatic entities using Hajj-themed phishing campaigns to deliver malware designed to steal sensitive communications, including WhatsApp data.

Their operations demonstrate advanced persistence, use of custom backdoors, and a modular malware ecosystem that has evolved significantly since at least 2022.

---

## 2. Threat Actor Overview

- **Primary name:** Mysterious Elephant  
- **Alias:** APT-K-47  
- **Region:** South Asia  
- **Activity observed since:** 2022  
- **Motivation:** Cyber espionage and intelligence gathering  

---

## 3. Target Profile

- Government officials  
- Diplomatic personnel  
- High-value communication targets  
- Messaging applications (notably WhatsApp)

---

## 4. Initial Access Techniques

- Spear-phishing campaigns using Hajj-themed lures  
- Malicious archives delivering payloads  
- Exploitation of known vulnerabilities:  
  - CVE-2023-38831  

---

## 5. Malware & Toolset

### 5.1 Backdoors
- ORPCBackdoor  
- Asyncshell-v2 (later evolving to HTTPS-based C2 communication)

### 5.2 Other tools
- MemLoader HidenDesk (sandbox evasion + hidden desktop execution)  
- Stom Exfiltrator (recursive file collection from user directories)  
- ChromeStealer Exfiltrator (WhatsAppOB.exe sample)

### 5.3 Downloader (early stage)
- Vtyrei (linked to Origami Elephant)

---

## 6. Persistence Mechanisms

- Registry Run Keys / Startup Folder  
  - MITRE ATT&CK: **T1547.001**  
- Autostart shortcut placement  
- Execution via PowerShell scripts  

---

## 7. Evasion Techniques

- Process count check (minimum **40 active processes**) before execution  
- Hidden desktop creation:
  - **MalwareTech_Hidden**  
- Sandboxing detection logic  

---

## 8. Command & Control (C2)

- Transition from TCP-based communication to HTTPS  
- Use of custom C2 infrastructure  
- Exfiltration via C2 channels  

---

## 9. Data Exfiltration

- Targeted WhatsApp data theft  
- Recursive directory scraping (Desktop, Downloads, etc.)  
- Upload of stolen data to attacker-controlled servers  

- MITRE ATT&CK: **T1041 (Exfiltration Over C2 Channel)**  

---

## 10. Key Findings

- Strong operational evolution since 2022  
- Shift from basic downloader chains to modular malware ecosystem  
- Increased focus on stealth and persistence  
- Clear emphasis on diplomatic and governmental intelligence gathering  

---

## 11. Indicators of Compromise (IOC)

- MD5: `9e50adb6107067ff0bab73307f5499b6`  
- File artifacts: WhatsAppOB.exe  
- Malicious archive payloads  
- Asyncshell variants  
- Hidden desktop: MalwareTech_Hidden  

---

## 12. Conclusion

Mysterious Elephant (APT-K-47) represents a mature and evolving cyber-espionage actor with a strong focus on stealth, persistence, and data exfiltration. Their toolset demonstrates continuous development, particularly in the Asyncshell backdoor family and supporting exfiltration utilities.

Their operations align with state-aligned espionage objectives targeting sensitive communications in South Asia and diplomatic environments.