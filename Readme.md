# Wazuh SIEM & Sysmon: Custom Detection Engineering Lab

## 🎯 Project Overview
This hands-on lab demonstrates **Detection Engineering** and **Security Operations Center (SOC)** core capabilities. The objective was to deploy a centralized Wazuh SIEM manager, configure Sysmon on a Windows endpoint for deep endpoint telemetry, write custom correlation rules for advanced adversary behaviors, and map every detection to the **MITRE ATT&CK®** framework.

---

## 🏗️ Lab Architecture & Environment
* **SIEM Manager:** Wazuh v4.12.0 deployed on Ubuntu Linux.
* **Endpoint Monitored:** Windows 11 (`DESKTOP-KPS6G6S`) running **Sysmon** (System Monitor) for advanced process, registry, and service telemetry.
* **Log Ingestion Pipeline:** Sysmon Event IDs $\rightarrow$ Wazuh Agent $\rightarrow$ Wazuh Manager Decoders & Rules $\rightarrow$ OpenSearch Dashboard.

---

## 🛠️ Custom Detection Rules Implemented
All custom correlation rules were integrated into `/var/ossec/etc/rules/local_rules.xml` to elevate low-noise administrative commands and malicious persistence mechanisms to high-severity alerts (Level 10–12) for immediate SOC triage.

### 1. Command-Line Reconnaissance (`100101`)
* **Sysmon Event ID:** `1 */` (Process Creation)
* **MITRE ATT&CK:** [T1033](https://attack.mitre.org/techniques/T1033/) (System Owner/User Discovery) & [T1087](https://attack.mitre.org/techniques/T1087/) (Account Discovery)
* **Description:** Detects enumeration utilities (`whoami`, `net localgroup`, `systeminfo`) executed immediately post-initial access.

### 2. Registry Persistence Mechanism (`100102`)
* **Sysmon Event ID:** `13` (Registry Event - Value Set)
* **MITRE ATT&CK:** [T1547.001](https://attack.mitre.org/techniques/T1547/001/) (Registry Run Keys / Startup Folder)
* **Description:** Flags unauthorized modifications to Windows startup Run keys (`CurrentVersion\Run`).

### 3. Scheduled Task Persistence (`100110`)
* **Sysmon Event ID:** `1 */` (Process Creation)
* **MITRE ATT&CK:** [T1053.005](https://attack.mitre.org/techniques/T1053/005/) (Scheduled Task)
* **Description:** Detects persistence established via the task scheduler utility (`schtasks.exe /create`).

### 4. Windows Service Creation (`100111`)
* **Sysmon Event ID:** `1 */` (Process Creation)
* **MITRE ATT&CK:** [T1543.003](https://attack.mitre.org/techniques/T1543/003/) (Create or Modify System Process: Windows Service)
* **Description:** Monitors abuse of the service control manager (`sc.exe create`) to register malicious payloads as system services.

---

## 📊 Attack Simulation & Validation (Screenshots)

### Detection of Scheduled Tasks and Windows Services (`100110` / `100111`)
![Wazuh Dashboard Hits](screenshots/wazuh-dashboard.png)

---

## 🚀 Key Takeaways & Skills Demonstrated
* **Telemetry Collection:** Configuring Sysmon config XML to capture high-fidelity process and registry events.
* **Detection Engineering:** Writing robust XML rules for Wazuh using regex (`pcre2`) and specific field filtering.
* **Threat Hunting & Incident Response:** Validating alerts through active attack simulation and inspecting Wazuh Discover dashboards.

