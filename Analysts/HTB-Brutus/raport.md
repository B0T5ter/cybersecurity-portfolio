# 🛡️ Security Incident Analysis Report

**Prepared by:** Filip Targosz  
**Date:** April 13, 2026  
**Severity:** High  
**Status:** Completed  

---

## 1. Executive Summary
Incident description:
- The system was targeted by a brute-force attack attempting to guess the administrative password.
- The attacker successfully gained access to the system after identifying the correct credentials.
- After gaining access, the attacker created a new user group with administrative privileges and installed a backdoor for persistent access.

---

## 2. Timeline of Events
| Timestamp (UTC)       | Event Description |
|----------------------|------------------|
| 2026-03-06 06:31:31  | Start of the SSH brute-force attack. |
| 2026-03-06 06:31:31  | Successful password identification by the automated script. |
| 2026-03-06 06:32:45  | **Manual login to the server.** Establishment of a terminal session (pts/1). |
| 2026-03-06 06:34:18  | Creation of a new local account named "cyberjunkie". |
| 2026-03-26 06:35:15  | Adding the "cyberjunkie" user to the "sudo" group for privilege escalation. |
| 2026-03-26 06:39:38  | Downloading and execution of the persistence script (linper.sh). |

---

## 3. Detection & Analysis
**Detection Source:**
System Logs Analysis.

**Analysis Description:**
- Detailed review of `auth.log` and binary `wtmp` artifacts to correlate login events and terminal session initialization.

**Indicators of Compromise (IoC):**
- **IP:** 65.2.161.68
- **Domain:** raw.githubusercontent.com
- **Hash:** N/A (Requires file acquisition)
- **File paths:** /home/cyberjunkie/linper.sh

---

## 4. Attack Description
**Attack Type:**
- Brute Force (SSH)

**Attack Vector:**
1. **Initial Access:** Gained unauthorized access to the root account via password guessing.
2. **Execution:** Used `curl` to download the `linper.sh` script from a remote repository.
3. **Persistence:** Created a local account "cyberjunkie" to maintain access independent of the root account.
4. **Privilege Escalation:** Granted the "cyberjunkie" account administrative (sudo) privileges.
5. **Lateral Movement:** N/A (No evidence found in analyzed logs).
6. **Data Exfiltration:** Attempted to read `/etc/shadow` to acquire password hashes for offline cracking.

---

## 5. Impact Assessment
- **Affected Systems:** Production Server.
- **Data Loss:** N/A (Unauthorized access to password hashes suspected).
- **Service Downtime:** N/A.
- **Incident Scope:** Localized to a single host.

---

## 6. Mitigation & Response
**Actions Taken:**
- Host isolation.
- IP Address blacklisting.
- Malware (linper.sh) removal.

**Follow-up Steps:**
- Mandatory password reset for all local accounts.
- System patching and security hardening.

---

## 7. Recommendations
- Implement a strong password policy (high complexity requirements).
- Disable root login via SSH and enforce SSH Key-Based Authentication.
- Deploy a brute-force protection tool (e.g., Fail2Ban).

---

## 8. Conclusion
The incident is classified as a successful system compromise with full administrative access. The attacker's actions, specifically creating a secondary account and downloading persistence tools, indicate an intent for long-term unauthorized presence. Timely analysis of `wtmp` artifacts allowed for precise identification of the breach window and effective remediation.