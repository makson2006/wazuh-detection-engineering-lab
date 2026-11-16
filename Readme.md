# Wazuh SIEM & Sysmon: Custom Detection Engineering & Incident Triage Lab

## 🎯 Project Overview
This hands-on lab demonstrates core **Detection Engineering** and **Security Operations Center (SOC)** capabilities. The objective was to deploy a centralized Wazuh SIEM manager, configure Microsoft Sysmon on a Windows endpoint for deep endpoint telemetry, write custom correlation rules mapped to the **MITRE ATT&CK®** framework, simulate post-exploitation activities, and perform a structured Tier 1/Tier 2 Incident Triage.
All the steps and documentation you can find on my Medium profile: [Endpoint Detection Engineering & Incident Triage with Wazuh SIEM](https://medium.com/@salyhamaks/endpoint-detection-engineering-incident-triage-with-wazuh-siem-d9af7291a061?postPublishedType=initial)
---

## 🏗️ Lab Architecture & Environment

| Component | Hostname / IP | OS / Version | Role |
| :--- | :--- | :--- | :--- |
| **SIEM Manager** | `maks-VMware-Virtual-Platform`<br>`(192.168.0.140)` | Ubuntu 26.04.1 LTS /<br>Wazuh v4.12.0 (AIO) | Log ingestion, rule correlation, alerting, and dashboarding |
| **Monitored Endpoint** | `DESKTOP-KPS6G6S`<br>`(Agent 001, 192.168.0.49)` | Windows 10 /<br>Wazuh Agent v4.12.0 | Target workstation for telemetry collection and attack simulation |

---

## 🚀 Execution Roadmap

1. **Phase 1 — Endpoint Setup:** Installed Microsoft Sysmon using the SwiftOnSecurity baseline configuration, configured Wazuh agent to ingest the operational event channel, and verified real-time TCP connectivity.
2. **Phase 2 — Detection Engineering:** Authored custom XML rules in Wazuh (`local_rules.xml`) to elevate low-noise administrative commands and stealthy persistence mechanisms to high-severity alerts.
3. **Phase 3 — Incident Triage:** Performed full-cycle alert analysis, raw log verification, timeline reconstruction, and false-positive evaluation for a reconnaissance detection.

---

## 🛠️ Custom Detection Rules Implemented

All custom rules were integrated into `/var/ossec/etc/rules/local_rules.xml` to catch common adversary tactics:

### 1. Command-Line Reconnaissance (`100101`)
* **Sysmon Event ID:** `1` (Process Creation)
* **Severity Level:** `12` (High)
* **MITRE ATT&CK:** [T1033](https://attack.mitre.org/techniques/T1033/) (System Owner/User Discovery) & [T1087](https://attack.mitre.org/techniques/T1087/) (Account Discovery)
* **Description:** Detects post-exploitation enumeration utilities (`whoami`, `net localgroup`, `systeminfo`) run immediately after initial access.

### 2. Scheduled Task Persistence (`100110`)
* **Sysmon Event ID:** `1` (Process Creation)
* **Severity Level:** `10` (High)
* **MITRE ATT&CK:** [T1053.005](https://attack.mitre.org/techniques/T1053/005/) (Scheduled Task/Job: Scheduled Task)
* **Description:** Monitors abuse of the built-in Windows task scheduler (`schtasks.exe /create`) to ensure malicious payloads automatically re-execute.

### 3. Windows Service Creation (`100111`)
* **Sysmon Event ID:** `1` (Process Creation)
* **Severity Level:** `10` (High)
* **MITRE ATT&CK:** [T1543.003](https://attack.mitre.org/techniques/T1543/003/) (Create or Modify System Process: Windows Service)
* **Description:** Targets service control manager invocations (`sc.exe create`) used by malware to register payloads running under `SYSTEM` privileges.

---

## 🔍 Phase 3 — Incident Triage Summary (Wazuh Alert `100101`)

* **Event Timestamp:** 2026-11-15 18:30:47 UTC
* **Endpoint:** `DESKTOP-KPS6G6S` (User: `DESKTOP-KPS6G6S\salyc`)
* **Triggered Command:** `C:\Windows\System32\whoami.exe /all` spawned from an interactive PowerShell session (`PID 3140` $\rightarrow$ `PID 20656`).
* **Verdict:** **Benign True Positive** (authorized lab simulation).
* **Key Findings:** Binary verified as legitimate Microsoft executable (`whoami.exe`) with matching metadata and no signs of masquerading. Parent PowerShell process was clean and un-obfuscated.
* **Analyst Recommendations (Remediation & Improvements):**
  1. Split broad reconnaissance rules into per-command branches with granular MITRE mappings (e.g., separating user discovery from group discovery).
  2. Reserve highest severity levels (Level 12) for correlation triggers (multiple distinct recon commands within a tight time window) rather than single command executions.
  3. Integrate environmental exclusions for known administrative scripts to reduce alert fatigue.

---

## 📈 Key Takeaways & Skills Demonstrated
* **Endpoint Telemetry Hardening:** Configuring advanced Sysmon rulesets to capture full command-line arguments and process trees.
* **SIEM Rule Authoring:** Writing robust XML detection logic for Wazuh using regex (`pcre2`) and field filtering.
* **Threat Detection & Triage:** Validating security controls through controlled purple-team simulations and conducting thorough log analysis.

---
*Author: Maksym Salyha*  
[LinkedIn](https://www.linkedin.com/in/maksym-salyha-4957892b7/) | [GitHub](https://github.com/makson2006)
