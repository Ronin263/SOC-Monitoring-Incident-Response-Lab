# 🔐 SSH Brute Force Attack Detection using Wazuh & Suricata


## 📌 Project Overview

This project demonstrates a multi-layered cybersecurity monitoring and incident response environment built in a controlled home lab.

The lab combines Linux server security monitoring with Windows endpoint network monitoring, using centralized event analysis through Wazuh.

The project consists of three main areas:

### 1. Linux SSH Brute Force Detection
- Attack simulation using Hydra on Kali Linux.
- SSH authentication monitoring on Debian 12.
- Network traffic inspection using Suricata.
- Host-based event analysis and correlation in Wazuh.

### 2. Automated Incident Response
- Fail2Ban detection of repeated SSH authentication failures.
- Automatic temporary IP blocking through nftables.
- Validation of the ban, automatic unban, and SSH connectivity recovery.

### 3. Windows Endpoint & Network Monitoring
- Windows 11 endpoint telemetry collection using Sysmon.
- Centralized event forwarding through Wazuh Agent.
- Custom Wazuh detection rules for outbound SSH and HTTPS connections.
- Validation of network connection alerts using Sysmon Event ID 3.

The main technologies used include:

- 🧠 SIEM: Wazuh
- 🌐 NIDS: Suricata
- 🖥️ Linux host monitoring: Debian 12 / systemd journal
- 🪟 Windows endpoint monitoring: Windows 11 / Sysmon
- 📡 Endpoint telemetry: Wazuh Agent
- ⚔️ Attack simulation: Hydra (Kali Linux)
- 🛡️ Automated response: Fail2Ban
- 🔥 Firewall enforcement: nftables

The goal is to demonstrate practical SOC workflows covering security monitoring, threat detection, log analysis, event correlation, and automated incident response across Linux and Windows environments.


---
## 🧪 Lab Architecture
| Component | Configuration |
|---|---|
| Attacker | Kali Linux — `192.168.0.197` |
| Target server | Debian 12 — `192.168.0.101` |
| Windows endpoint | Windows 11 VM — `192.168.0.247` |
| SIEM | Wazuh Manager — `192.168.0.85` |
| Linux NIDS | Suricata 7.0.10 |
| Linux Wazuh Agent | 4.13.1 |
| Windows Wazuh Agent | 4.14.9 |
| Windows endpoint monitoring | Sysmon 15.22 |
| Sysmon configuration | SwiftOnSecurity |
| Linux automated response | Fail2Ban 1.0.2-2 |
| Linux firewall | nftables |
| Linux log source | systemd journal |
| Windows log source | Microsoft-Windows-Sysmon/Operational |
| Monitored Linux service | SSH — TCP port 22 |
| Windows network monitoring | Outbound SSH (TCP/22) and HTTPS (TCP/443) |
| Custom Wazuh rules | 100100 — SSH; 100101 — HTTPS |

---

## ⚔️ Attack Simulation

The attack was performed using **Hydra**:

```bash
hydra -l root -P rockyou.txt ssh://192.168.0.101
```

### 📊 Attack Details:

* ⏱️ Duration: ~10 minutes (19:30 – 19:40)
* 🔁 Attempts: ~2000 login attempts
* 🎯 Target: SSH (port 22)
* 👤 Account targeted: root

### Attack Simulation Scope

This test focused on generating repeated SSH authentication failures
using Hydra and observing the resulting events across the host,
network, and SIEM monitoring layers.

The automated Fail2Ban response was validated separately
under a dedicated test scenario described below.
---

## 🔍 Detection Results

### 🖥️ Host-Level Detection (Debian)

* Logs collected via `journalctl`
* Multiple entries:

  ```
  Failed password for root from 192.168.0.197
  ```
* Confirms brute force activity at system level

---

### 🌐 Network-Level Detection (Suricata)

Suricata monitored network traffic during the SSH brute-force simulation.

A custom SSH-related detection signature generated an alert
for potentially suspicious SSH activity.

The alert was treated as a network-level detection indicator
and correlated with host-based authentication logs.

Alert evidence is available in the screenshots below.

---

### 🧠 SIEM Detection (Wazuh)

* Detected multiple failed login attempts
* Triggered rules for:

  * Authentication failure
  * Password guessing

### 🔥 MITRE ATT&CK Mapping:

* **T1110 – Brute Force**
* Category: Credential Access

---

## 🔗 Correlation Analysis

Data from All systems confirmed corelation ofthe same attack:

| Indicator | Value         |
| --------- | ------------- |
| Source IP | 192.168.0.197 |
| Target IP | 192.168.0.101 |
| Port      | 22 (SSH)      |
| Time      | 19:30 – 19:40 |

### ✅ Conclusion:

The attack was successfully detected across:

* Host logs
* Network IDS
* SIEM platform

This demonstrates a **defense-in-depth security model**.

---

## 🛡️ Mitigation Recommendations

* Disable SSH root login
* Use SSH key authentication
* Install and configure fail2ban
* Limit login attempts
* Change default SSH port
* Configure firewall rules using nftables.

---
## 🛡️ Automated Incident Response — Fail2Ban & nftables

### Objective

Extend the detection workflow with automated response capabilities to temporarily block a source IP address after repeated failed SSH authentication attempts.

### Configuration

* **Detection tool:** Fail2Ban
* **Log source:** systemd journal
* **Firewall:** nftables
* **Failed-attempt threshold:** 5
* **Detection window:** 600 seconds
* **Ban duration:** 600 seconds

### Test Results

This was a separate validation scenario from the Hydra attack simulation.

The test focused on verifying the automated response to repeated
SSH authentication failures using the configured Fail2Ban SSH jail.

1. Five failed SSH authentication attempts were generated.
2. Fail2Ban detected the repeated failures.
3. The source IP address was blocked through nftables.
4. The block expired automatically after approximately 10 minutes.
5. SSH connectivity was successfully restored.

### Log Evidence

```text
2026-10-18 18:46:42 — Ban 192.168.0.197
2026-10-18 18:56:42 — Unban 192.168.0.197
```

### Response Lifecycle

**Detection → Automated Blocking → Temporary Containment → Automatic Unban → Connectivity Validation**

This extension demonstrates a basic automated response workflow alongside the existing host-level, network-level, and SIEM detection components.

### Validation Status

* [x] Repeated failed SSH attempts detected
* [x] Source IP blocked through nftables
* [x] Automatic unblocking confirmed in logs
* [x] SSH connectivity restored after the ban expired

---

## 🖥️ Windows Endpoint & Network Monitoring — Sysmon + Wazuh

### Objective

Extend the existing monitoring environment with Windows endpoint telemetry and network connection detection using Sysmon and Wazuh.

This module focuses on monitoring outbound network connections from a Windows 11 endpoint and generating dedicated Wazuh alerts for SSH and HTTPS traffic.

### Environment

- Endpoint: Windows 11 VM
- Windows IP: `192.168.0.247`
- Telemetry source: Microsoft-Windows-Sysmon/Operational
- Sysmon version: 15.22
- Sysmon configuration: SwiftOnSecurity
- Wazuh Agent: 4.14.9
- Wazuh Manager: `192.168.0.85`
- Monitored event: Sysmon Event ID 3 — Network Connection

### Data Collection Workflow

1. Sysmon monitors network connection events on Windows 11.
2. Windows events are written to the Sysmon Operational event channel.
3. Wazuh Agent collects events through the Windows EventChannel integration.
4. Events are forwarded to the central Wazuh Manager.
5. Custom Wazuh rules identify selected outbound network connections.
6. Matching events generate security alerts in Wazuh.

### Custom Detection Rules

Two custom Wazuh rules were implemented and validated:

| Rule ID | Detection | Conditions |
|---|---|---|
| `100100` | Outbound SSH | TCP, initiated connection, destination port 22, `ssh.exe` |
| `100101` | Outbound HTTPS | TCP, initiated connection, destination port 443 |

Both rules use Sysmon Event ID 3 as their event source and generate Wazuh alerts at level 5.

### Validation Results

**SSH Detection**

- Source endpoint: Windows 11 VM
- Process: `ssh.exe`
- Destination: Wazuh Manager host (`192.168.0.85`)
- Destination port: TCP/22
- Wazuh rule: `100100`
- Result: Alert successfully generated.

**HTTPS Detection**

- Source endpoint: Windows 11 VM
- Process: `OneDrive.Sync.Service.exe`
- Destination IP: `74.151.238.130`
- Destination port: TCP/443
- Wazuh rule: `100101`
- Result: Alert successfully generated.

### Evidence and Log Analysis

The generated alerts were verified in:

- `/var/ossec/logs/archives/archives.json`
- `/var/ossec/logs/alerts/alerts.json`

The validation confirmed that Windows Sysmon events were collected by the Wazuh Agent, processed by the Manager, and matched against the custom detection rules.

### Scope and Limitations

This module demonstrates endpoint network connection monitoring. It is a separate validation scenario from the Linux SSH brute-force simulation and does not represent an authentication attack.

The current implementation focuses on selected outbound SSH and HTTPS connections. It does not claim comprehensive monitoring of all Windows network traffic.

---


## 📚 Documentation

Detailed documentation of the project and validation scenarios:

* [Incident Report](docs/incident-report.md) — incident overview, detection, response, evidence, and recovery.
* [Test Results](docs/test-results.md) — technical test results, log correlation, and validation of the automated response workflow.
  
## 🚀 Technical Skills Demonstrated

* Linux security monitoring and log analysis
* SSH brute-force attack detection
* Network traffic analysis with Suricata
* SIEM event analysis and correlation with Wazuh
* Custom Wazuh detection rule development
* Windows endpoint monitoring with Sysmon
* Windows EventChannel log collection
* Network connection detection (Sysmon Event ID 3)
* SSH and HTTPS traffic monitoring
* Automated incident response with Fail2Ban
* Firewall rule enforcement with nftables
* Security event validation and troubleshooting
* Incident documentation and technical reporting

---

## 📎 Screenshots
* Hydra attack
![Hydra](../main/screenshots/Kali.jpg)

* Debian logs
![Debian Logs](../main/screenshots/victim-logs.png)

* Suricata alerts
![Suricata alerts](../main/screenshots/network-detection/Suricata1.jpg)

* Wazuh dashboard
![Wazuh](../main/screenshots/siem-analysis/Wazuh.jpg)
![Wazuh](../main/screenshots/siem-analysis/Wazuh1.jpg)
![Wazuh](../main/screenshots/siem-analysis/Wazuh2.jpg)
![Wazuh](../main/screenshots/siem-analysis/Wazuh3.jpg)

* Fail2Ban / nftables
![Fail2Ban detection and nftables](../main/screenshots/automated-response/fail2ban-detection_nftables.png)

* Automatic unban
![Automatic unban](../main/screenshots/automated-response/automatic-unban.png)

* SSH connectivity recovery
![SSH connectivity recovery](../main/screenshots/automated-response/ssh-connections-recovery.png)


---

## 💬 Summary

This project demonstrates a multi-layered SSH brute-force detection
and automated response workflow in a controlled cybersecurity home lab.

The environment combines Wazuh for host-based security event analysis,
Suricata for network monitoring, Fail2Ban for automated response,
and nftables for firewall enforcement.

The project includes a Hydra-based SSH attack simulation and a separate
validation of automatic IP blocking, unblocking, and SSH connectivity recovery.

It demonstrates practical SOC skills in security monitoring,
log analysis, event correlation, incident response, and technical documentation.

---

## 👤 Author

[Arkadiusz Rytych]
[Linkedin](http://linkedin.com/in/arkadiusz-rytych-60644bb7) 
| Cybersecurity Enthusiast | SOC Analyst  | L2 Infrastructure & Security Operation | ISC2 CC
