# SOC-Automation-Framework
A compact, production-ready SOC Automation Framework designed for blue teams and SOC analysts. This repository contains a reference architecture, deployment steps, Wazuh detection rules, Shuffle (SOAR) playbook examples, helper scripts, and documentation so you can reproduce the environment and demonstrate it during interviews or in a portfolio.

**Overview**

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
