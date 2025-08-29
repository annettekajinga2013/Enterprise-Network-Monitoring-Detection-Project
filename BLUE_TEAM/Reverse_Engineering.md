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

---
## Sreenshots

<img width="1336" height="805" alt="image" src="https://github.com/user-attachments/assets/73d3bcdd-2c67-4e3d-840c-238fb4c20ebc" />


<img width="1333" height="792" alt="image" src="https://github.com/user-attachments/assets/4fcd3cfc-46ea-4f74-89a9-bc30d6c5fa35" />


<img width="1353" height="809" alt="image" src="https://github.com/user-attachments/assets/c8596ea6-7a09-4029-8de6-c357163a0030" />

<img width="1337" height="831" alt="image" src="https://github.com/user-attachments/assets/bc7a2a2b-e55c-41b7-b4c9-67021d4f4112" />


<img width="1350" height="808" alt="image" src="https://github.com/user-attachments/assets/a5ffd08a-3c30-4201-87c5-52e5b7137159" />


<img width="1359" height="806" alt="image" src="https://github.com/user-attachments/assets/d817d89e-e6ac-472c-a31a-cc2af82541b4" />

<img width="1348" height="808" alt="image" src="https://github.com/user-attachments/assets/f53f47c9-afdc-4bc3-881f-410514d009ed" />




## Key Outcomes
- Enhanced detection coverage with custom signatures  
- Improved threat hunting aligned with ATT&CK framework  
- Accelerated incident response with high-fidelity IOCs  
- Documented knowledge base for future investigations
