# Incident Response Report: Compromise of backup-secondary
**Projekt Name:** Telly
**Date:** 17.04.2026
**Severity:** Critical

---

## 1. Executive Summary
On January 27, 2026, a critical security breach was detected on the `backup-secondary` host. An unauthorized actor gained access via the unencrypted Telnet protocol. The attacker performed local reconnaissance, escalated privileges, and utilized the `linper.sh` automation script to establish widespread persistence. The incident culminated in the exfiltration of a sensitive customer credit card database.

---

## 2. Incident Timeline (UTC)

| Time | Event | Technical Details |
|:---|:---|:---|
| **10:39:28** | **Initial Access** | Successful login to `backup-secondary` account via Telnet. |
| **10:42:56** | **Account Manipulation** | Attacker created a new user to maintain backdoor access. |
| **10:43:01** | **Credential Access** | Unauthorized access/reading of `/etc/shadow` to dump password hashes. |
| **10:43:14** | **Reconnaissance** | Discovery of sensitive directories: `/opt`, `/media`, `/dev`, and `/tmp`. |
| **10:44:29** | **Staging Tools** | Ingress of the [linper.sh](https://raw.githubusercontent.com/montysecurity/linper/refs/heads/main/linper.sh) post-exploitation script. |
| **10:44:32** | **Privilege Escalation** | Permissions modified (`chmod +x`) to allow script execution. |
| **10:44:40** | **Automated Execution** | Execution of `linper.sh` for system enumeration and defense listing. |
| **10:44:46** | **Persistence** | Mass installation of backdoors (Cron, Systemd, RC.local) and timestomping. |
| **10:49:54** | **Exfiltration** | Python HTTP server launched on port 6932; credit card DB exfiltrated. |
| **10:52:04** | **Anti-Forensics** | Attacker attempted to securely delete the database using `shred`/`rm`. |
| **10:54:00** | **Session Termination** | Attacker logged out of the compromised session. |

---

## 3. Technical Analysis

### Threat Actor Profile
* **Source IP:** `192.168.72.131` (Internal Pivot) / `91.99.25.54` (External C2/Stealth Mode).
* **Techniques:** T1071 (Application Layer Protocol), T1059 (Command and Scripting Interpreter), T1567 (Exfiltration Over Web Service).

### Evidence of Exfiltration
The attacker utilized a temporary Python-based web server to host the sensitive file. Network logs confirm a successful `GET` request for the following asset:
* **Filename:** `credit-cards-25-blackfriday.db`
* **Size:** ~12 KB
* **Victim Data Identified:** Credit card information for customers, including **Quinn Harris** (Card No: `5312269047781209`).

### Anti-Forensics Measures
The attacker consulted a "secure erase" guide within the terminal session and attempted to use **Timestomping** on modified configuration files to alter metadata (mtime/atime), making it harder for administrators to identify which files were tampered with during the breach.

---

## 4. Breached Data Validation (Compliance)
As part of the notification requirements, the following record was verified from the recovered database:
* **Customer Name:** Quinn Harris
* **Email:** `quinn.harris@hotmail.com`
* **Compromised Asset:** 4K Monitor Purchase
* **Credit Card Number:** `5312269047781209`

---

## 5. Mitigation & Recovery
1. **Remediation:** Disable Telnet services immediately and enforce SSH with Key-Based Authentication.
2. **Cleanup:** Audit all `crontab`, `systemd` units, and `rc.local` files for unauthorized entries added by `linper.sh`.
3. **Password Reset:** Force a global password reset for all accounts found in the `/etc/shadow` dump.
4. **Legal:** Notify affected customers regarding the breach of their financial information.

---