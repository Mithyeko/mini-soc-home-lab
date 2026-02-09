# Suricata (Network IDS) → Wazuh (EVE JSON Ingestion)
This documents my Suricata IDS deployment and how I forward Suricata `eve.json` events into Wazuh using the Wazuh agent.

## Data flow
Suricata (sensor) → `/var/log/suricata/eve.json` → Wazuh agent `<localfile log_format=json>` → Wazuh manager → Wazuh Dashboard (Threat Hunting)

## Prerequisites
- Suricata installed on the sensor machine (Ubuntu in my lab)
- Wazuh manager + dashboard already running
- Network connectivity between the sensor and Wazuh manager

---

## Confirm Suricata is running
```bash
sudo systemctl status suricata
```

---

## Install Wazuh agent on the Suricata machine
In Wazuh Dashboard:
Agents → Deploy new agent
Enter Wazuh manager IP (Server address)
Give the agent a name
Copy the generated install command and run it on the Suricata machine

---

## Configure Wazuh agent to ingest Suricata eve.json
```bash
sudo nano /var/ossec/etc/ossec.conf
```
Add:
```XML
<localfile>
  <log_format>json</log_format>
  <location>/var/log/suricata/eve.json</location>
</localfile>
```
Then:
```bash
sudo systemctl restart wazuh-agent
```

---

## Download + extract Emerging Threats Open rules (ET Open)
Emerging Threats Open (ET Open) is a free, community/curated ruleset (signatures) used by IDS/IPS engines like Suricata (and Snort). These “rules” tell Suricata what patterns to look for in network traffic—things like known malicious IP/domain indicators, exploit attempts, malware command-and-control behavior, suspicious protocol usage, and recon/scanning patterns.

I'm using it because I want better detection coverage than “stock” Suricata. I want to generate useful alert events in eve.json so Wazuh can ingest and show them. ET Open helps quickly produce alerts for common lab actions like Nmap scans, Suspicious HTTP patterns, DNS oddities, Known exploit signatures. This makes Wazuh's Threat Hunting view actually populate with meaningful events.

To download:
```bash
cd /tmp/ && curl -LO https://rules.emergingthreats.net/open/suricata-6.0.8/emerging.rules.tar.gz
sudo tar -xvzf emerging.rules.tar.gz
sudo mkdir -p /etc/suricata/rules
sudo mv rules/*.rules /etc/suricata/rules/
sudo chmod 640 /etc/suricata/rules/*.rules
```

---

## Update Suricata config (/etc/suricata/suricata.yaml)
Update Suricata config (/etc/suricata/suricata.yaml)
```yaml
HOME_NET: "<UBUNTU_IP>"
EXTERNAL_NET: "any"
default-rule-path: /etc/suricata/rules
rule-files:
- "*.rules"
# Global stats configuration
stats:
enabled: yes
# Linux high speed capture support
af-packet:
- interface: enp0s3
```
restart Suricata and wazuh-agent
```bash
systemctl restart suricata
systemctl restart wazuh-agent
```


   
