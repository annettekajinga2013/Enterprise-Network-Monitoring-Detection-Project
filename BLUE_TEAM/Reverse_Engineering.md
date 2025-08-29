# Reverse Engineering Component

## Overview
The reverse engineering phase of the Enterprise Network Monitoring & Detection (ENMD) project was designed to analyze malicious artifacts, traffic flows, and suspicious system events to better understand attacker techniques and create tailored detection rules.

---

## Objectives
- Identify and analyze potential malicious behaviors in both network and endpoint environments.
- Dissect suspicious binaries, scripts, and traffic patterns to extract Indicators of Compromise (IOCs).
- Generate custom detection rules for Suricata, Sigma, and Splunk based on the findings.
- Improve forensic readiness and incident response strategies.

---

## Tools Utilized
- **Wireshark & Zeek:** For packet capture and deep network traffic analysis  
- **Suricata IDS:** For network intrusion detection rule creation  
- **Sysmon (Windows):** To capture detailed endpoint process creation and registry modification events  
- **Splunk:** For centralized log ingestion, correlation, and rule deployment  
- **Ghidra / strings / PE tools (optional):** For binary analysis of suspicious executables or malware samples  

---

## Methodology & Steps
1. **Data Collection**  
   Captured network traffic using Zeek and Wireshark.  
   Aggregated Sysmon logs via Splunk forwarders.

2. **Artifact Analysis**  
   Reverse-engineered suspicious files, scripts, or processes to determine behavior.  
   Extracted indicators: IPs, domains, hashes, command-line arguments.

3. **Pattern Extraction**  
   Mapped activity to MITRE ATT&CK techniques (e.g., T1059 – Command & Scripting Interpreter).

4. **Detection Rule Development**  
   Created Suricata signatures and Sigma rules based on findings.

5. **Validation & Testing**  
   Tested rules against captured traffic.  
   Tuned for minimal false positives.
