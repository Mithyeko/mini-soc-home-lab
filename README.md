# 🛡️ Mini SOC Home Lab – Blue Team &amp; Detection Project
## Overview
This project documents the design, deployment, and analysis of a **Mini Security Operations Center (SOC) Home Lab** built to simulate real-world blue team operations.  
The lab focuses on **log collection, threat detection, alert analysis, and network security monitoring**, aligned with SOC analyst and CySA+ objectives.

This repository demonstrates hands-on experience with:
- SIEM deployment and alert analysis
- Network IDS Telemetry (Suricata)
- Endpoint telemetry (Sysmon)
- Network security and firewalling
- Attack detection and investigation
- SOC-style documentation and reporting

---

## Lab Objectives
- Build a realistic SOC-style environment
- Collect and analyze endpoint and network logs
- Detect malicious behavior using SIEM alerts
- Understand attacker techniques and defender visibility
- Practice incident-style documentation

---

## Architecture & Tools

### Core Components
* pfSense – Firewall, NAT, and network segmentation
* Wazuh – SIEM and log correlation
* Suricata – Network IDS (EVE JSON alerts forwarded into Wazuh)
* Windows 10 VM – Endpoint telemetry with Sysmon
* Ubuntu Server – Wazuh manager and Linux monitoring
* Kali Linux – Attack simulation (controlled testing)
* VirtualBox – Virtualization platform

A detailed network diagram is available in the `soc-lab-network-diagram.png`.

---

## Network Design
The lab is segmented to simulate enterprise-style traffic flow:

- External / Attacker Network (Kali)
- Internal Protected Network
- SIEM & Management Network
- Firewall enforcement via pfSense

All traffic passes through pfSense, allowing full visibility and control.

---

## Repository Structure

* `attacks/`   – Attack scenarios + detection notes
* `windows/`   – Sysmon + Windows telemetry setup
* `linux/`     – Linux telemetry and baseline configs
* `pfsense/`   – Firewall configuration and routing notes
* `wazuh/`     – Wazuh setup and rules/decoders notes
* `suricata/`  – Network IDS setup (EVE JSON → Wazuh), ruleset management, validation

---

## Detection & Monitoring
### Endpoint Telemetry
- Sysmon installed on Windows endpoint
- Key event IDs monitored:
  - Process creation
  - Network connections
  - Image loads
  - Logon activity

### SIEM Correlation
- Logs forwarded to Wazuh Manager
- Alerts analyzed and documented
- Detection results mapped to attacker behavior

### Network IDS Telemetry (Suricata)
Suricata runs as the network IDS sensor and writes alerts/events to:
`/var/log/suricata/eve.json`

A Wazuh agent monitors `eve.json` and forwards the JSON events to the Wazuh manager for correlation and visualization.
In Wazuh, Suricata events can be hunted with a filter like:
`rule.groups:suricata`

See: `suricata/README.md`.

---

## Attack Simulations
Controlled attack scenarios were executed to test detection capabilities, including:
- Network scanning
- Authentication failures
- Suspicious process execution

Each scenario includes:
- Attack description
- Expected behavior
- Observed logs
- Detection outcome
- Analyst conclusion

Documentation is available in the `attacks/` directory.

---

## Key Skills Demonstrated
- SOC alert triage and analysis
- Network security fundamentals
- SIEM configuration and usage
- Log interpretation and correlation
- Security documentation and reporting
- Blue team mindset and methodology

---

## Disclaimer
All attacks were performed in a **controlled lab environment** for educational purposes only.  
No production systems or external networks were targeted.

---

## Future Improvements
- Add MITRE ATT&CK mapping
- Expand detection rules
- Add incident response playbooks
- Integrate additional log sources

---

## Author
Cybersecurity student and SOC analyst candidate  
Focused on blue team operations, detection engineering, and incident analysis.
