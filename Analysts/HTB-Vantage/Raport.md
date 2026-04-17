# 🛡️ Security Incident Analysis Report

- **Prepared by:** Filip Targosz
- **Date:** 2026-04-14
- **Severity:** Critical
- **Status:** In Progress

---

## 1. Executive Summary

**Incident Overview:**
The organization experienced a critical security breach within the OpenStack cloud infrastructure. The attacker initiated the intrusion by utilizing directory fuzzing tools (`ffuf`) to discover an exposed web endpoint. Due to a severe web server misconfiguration, the threat actor successfully downloaded the `admin-openrc.sh` file, which contained plaintext administrative credentials. Leveraging these compromised credentials, the attacker authenticated to the OpenStack API (Keystone), enumerated the environment, and successfully exfiltrated sensitive employee and user data residing in the Swift object storage. To maintain persistent access, the attacker created a rogue administrator account before the activity was detected.

---

## 2. Timeline of Events

| Timestamp (UTC) | Event Description |
| :--- | :--- |
| 2025-07-01 09:38:26 | Attacker initiated active directory scanning/fuzzing. |
| 2025-07-01 09:39:07 | Hidden subdomain/directory successfully discovered. |
| 2025-07-01 09:40:07 | Successful authentication to the web dashboard. |
| 2025-07-01 09:40:29 | Download of the `admin-openrc.sh` file containing OpenStack API credentials. |
| 2025-07-01 09:41:44 | First unauthorized interaction with the OpenStack API (Keystone / Identity service). |
| 2025-07-01 09:43:27 | Discovery of the Swift (Object Storage) endpoint URL. |
| 2025-07-01 09:43:47 | Exfiltration of the sensitive `user-data` file. |
| 2025-07-01 09:44:05 | Exfiltration of the sensitive `employee-data` file. |
| 2025-07-01 09:48:02 | Creation of a rogue administrator user account for persistence. |

---

## 3. Detection & Analysis

- **Detection Source:**
  - Network traffic analysis and server logs.
- **Analysis Description:**
  - Forensic analysis was conducted on network packet captures: `controller.2025-07-01.pcap` and `web-server.2025-07-01.pcap`. The pcaps revealed the entire attack chain, from initial fuzzing over HTTP to unauthorized OpenStack API queries.
- **Indicators of Compromise (IoC):**
  - **Attacker IP:** `117.200.21.26`
  - **Rogue User Account:** `jellibean`
  - **Exfiltrated Files:** `user-details.csv`, `employee-details.csv`

---

## 4. Attack Description (with MITRE ATT&CK Mapping)

- **Attack Type:** Application Misconfiguration Exploitation & Credential Theft.
- **Attack Kill Chain:**

  1. **Reconnaissance & Initial Access:**
     The attacker actively scanned the infrastructure to find exposed services and hidden directories.
     * **MITRE ATT&CK:** `T1595.001` (Active Scanning: Scanning IP Blocks) / `T1190` (Exploit Public-Facing Application).
  2. **Credential Access:**
     Exploiting a misconfiguration, the attacker downloaded an unsecured environment file containing administrative passwords.
     * **MITRE ATT&CK:** `T1552.001` (Unsecured Credentials: Credentials In Files).
  3. **Execution & Privilege Escalation:**
     The attacker utilized the stolen administrative credentials to authenticate directly to the OpenStack API, effectively gaining highest-level privileges in the cloud environment.
     * **MITRE ATT&CK:** `T1078.004` (Valid Accounts: Cloud Accounts).
  4. **Discovery & Lateral Movement:**
     The attacker enumerated the OpenStack catalog to locate the Swift object storage endpoint.
     * **MITRE ATT&CK:** `T1526` (Cloud Service Discovery).
  5. **Data Exfiltration:**
     The attacker accessed the Swift storage containers and downloaded CSV files containing sensitive PII.
     * **MITRE ATT&CK:** `T1530` (Data from Cloud Storage Object).
  6. **Persistence:**
     To ensure continuous access even if the original compromised password was changed, the attacker created a new user account (`jellibean`) with admin privileges via the Keystone API.
     * **MITRE ATT&CK:** `T1136.003` (Create Account: Cloud Account).

---

## 5. Impact Assessment

**Severity: Critical.**
The primary impact is a severe data breach involving the compromise of Personally Identifiable Information (PII) belonging to both employees and users. Furthermore, the complete compromise of the OpenStack administrative layer exposed the entire cloud infrastructure to potential sabotage or further exploitation.

---

## 6. Mitigation & Response

- **Actions Taken:**
  - Isolated the compromised web server host from the network.
  - Blocked the attacker's IP address (`117.200.21.26`) at the perimeter firewall.
  - Revoked all active API tokens associated with the compromised project.
- **Additional Remediation Steps:**
  - Executed a mandatory global password reset for all OpenStack administrators.
  - Identified and permanently deleted the rogue persistence account (`jellibean`).

---

## 7. Recommendations

- **Secure Configuration Management:** Restrict access to sensitive `.sh` and environment files on web servers. Implement proper directory permissions.
- **Enhance Password Policy & MFA:** Enforce Multi-Factor Authentication (MFA) for all administrative access to the OpenStack dashboard and API.
- **Proactive Monitoring:** Implement robust API logging and alerting rules for anomalous administrative actions (e.g., creation of new admin accounts, bulk data downloads from Swift).
- **Vulnerability Scanning:** Regularly perform automated fuzzing and vulnerability scans on external-facing assets to catch misconfigurations before attackers do.

---

## 8. Conclusion

This incident highlights that a single perimeter misconfiguration, combined with unsecured credential storage, can lead to the total compromise of a cloud environment. The attacker demonstrated proficiency in moving from a simple web application vulnerability directly to the core cloud API, successfully exfiltrating data and establishing persistence. Implementing the recommended defense-in-depth strategies is critical to preventing similar breaches in the future.