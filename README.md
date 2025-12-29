# SOC-Automation-Framework
A compact, production-ready SOC Automation Framework designed for blue teams and SOC analysts. This repository contains a reference architecture, deployment steps, Wazuh detection rules, Shuffle (SOAR) playbook examples, helper scripts, and documentation so you can reproduce the environment and demonstrate it during interviews or in a portfolio.

**Overview**
<img width="1046" height="667" alt="ChatGPT Image Dec 29, 2025, 10_52_11 AM" src="https://github.com/user-attachments/assets/7caeb9cb-a3c2-4709-a1ff-966c0b36e416" />


Goal: Automate alert triage, enrichment, and response to reduce manual work and improve Mean Time To Respond (MTTR).

Core components:

Wazuh — log collection, File Integrity Monitoring (FIM), rule engine and alerting.

Shuffle — no-code SOAR platform to create workflows that enrich alerts and trigger responses.

Threat Intelligence — VirusTotal, AbuseIPDB (examples), custom feeds.

Firewall / Endpoint — simulated via local scripts or API calls (example: iptables, Palo Alto/OPNsense API).


SOC-Automation-Framework/
├─ docs/
│ ├─ architecture.md
│ └─ playbook_examples.md
├─ wazuh/
│ ├─ rules/local_rules.xml
│ ├─ decoders/local_decoder.xml
│ └─ agent-setup/
│ ├─ install-agent-windows.ps1
│ └─ install-agent-linux.sh
├─ shuffle/
│ ├─ playbooks/
│ │ ├─ brute_force_detection.json
│ │ └─ malicious_hash_workflow.json
│ └─ connectors/
│ └─ virustotal_connector.py
├─ scripts/
│ ├─ enrich_ip.py
│ ├─ send_to_shuffle.py
│ └─ simulate_attack.sh
├─ examples/
│ └─ sample_alert.json
├─ README.md
└─ LICENSE


##  Quick Start

### 1. Wazuh Installation on Kali
```bash
# Install Wazuh Manager
"curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
sudo bash wazuh-install.sh --wazuh-indexer-node wazuh.indexer"
<img width="1523" height="900" alt="ChatGPT Image Dec 29, 2025, 10_36_11 AM" src="https://github.com/user-attachments/assets/768b830f-f66f-4493-96fd-8fa516b122ea" />


###2. Windows Agent Setup
Download Windows agent from Wazuh dashboard

Install on Windows 7 VM with command:
msiexec.exe /i wazuh-agent-4.7.3-1.msi /q WAZUH_MANAGER="192.168.----" WAZUH_REGISTRATION_SERVER="192.168.1---"

3. Filebeat Configuration
# filebeat.yml
filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/ossec/logs/alerts/alerts.json
  json.keys_under_root: true
  json.add_error_key: true

output.http:
  hosts: ["https://-----------/api/v1/hooks/wazuh-alerts"]
  headers:
    Content-Type: "application/json"
<img width="1536" height="1024" alt="ChatGPT Image Dec 29, 2025, 10_39_32 AM" src="https://github.com/user-attachments/assets/a64284e6-4b74-481d-a775-1552ab868c6f" />


###4. Shuffle Workflow Setup
Import the workflow from config/shuffle/workflows/soc-automation.json

<!-- local_rules.xml -->
<rule id="5710" level="12">
  <if_sid>5710</if_sid>
  <description>Brute force login detected</description>
  <options>no_full_log</options>
  <group>authentication_failed,</group>
</rule>
<img width="1219" height="845" alt="ChatGPT Image Dec 29, 2025, 10_47_59 AM" src="https://github.com/user-attachments/assets/9358e622-5ae2-4769-bafb-3bb9451dd5cf" />

Wazuh Rule for Brute Force Detection
<!-- local_rules.xml -->
<rule id="5710" level="12">
  <if_sid>5710</if_sid>
  <description>Brute force login detected</description>
  <options>no_full_log</options>
  <group>authentication_failed,</group>
</rule>

Shuffle Automation Workflow
The workflow includes:

Parse Alert Fields: Extract IP, username, port, rule ID

VirusTotal Check: Get reputation score

AbuseIPDB Analysis: Check confidence score

Decision Logic: If malicious_score > 5 OR abuseConfidenceScore > 80

Block IP: Add to firewall blacklist

Create Case: Generate investigation ticket

Slack Alert: Notify SOC team

VirusTotal: IP/Domain/File reputation

AbuseIPDB: IP confidence scoring

GreyNoise: Internet background noise

Wazuh - Open source SIEM

Shuffle - SOAR platform

Filebeat - Log forwarding


## Additional Documentation Files

**docs/architecture.md:**
```markdown
# System Architecture

## Components
1. **Wazuh Manager (Kali Linux)**: SIEM central server
2. **Wazuh Agent (Windows 7)**: Endpoint monitoring
3. **Filebeat (Kali)**: Log forwarder
4. **Shuffle SOAR**: Automation platform
5. **External APIs**: Threat intelligence services

## Data Flow
1. Agent → Manager: Log collection
2. Manager → Filebeat: Alert generation
3. Filebeat → Shuffle: HTTP POST
4. Shuffle → APIs: Threat enrichment
5. Shuffle → Actions: Automated response
