# Detection & Correlation Guide: Suricata + Sysmon + Splunk

This guide provides a step-by-step process to **detect file downloads** using **Suricata IDS**, **detect file executions** using **Sysmon** on Windows, and **send both logs to Splunk for correlation**.  
The objective is to **correlate network-based download activity with endpoint file execution events**.

---

## Architecture Overview

1. **Suricata IDS**: Detects file downloads on the wire and produces `eve.json` logs.
2. **Sysmon (Windows)**: Detects process creation, file creation, and hashes of downloaded files.
3. **Splunk**: Central log collector where Suricata and Sysmon logs are ingested and correlated.

---

## Sysmon Setup (Windows VM)

1. Download **Sysmon** from [Microsoft Sysinternals](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon).
2. Use a configuration file with:
   - `SHA256` hashing enabled
   - `FileCreateStreamHash` enabled
3. Install Sysmon with:

   ```bash
   sysmon64.exe -accepteula -i sysmon-config.xml


4. Verify logs in Event Viewer:
Applications and Services Logs → Microsoft → Windows → Sysmon → Operational


## Suricata Setup (Linux Sensor Recommended)

1. Install Suricata:

<img width="596" height="78" alt="image" src="https://github.com/user-attachments/assets/e9e06223-48b8-4911-9dcf-d355faa424f5" />


2. Edit /etc/suricata/suricata.yaml to enable eve.json, fileinfo, and filestore.
Example configuration snippet:

<img width="513" height="395" alt="image" src="https://github.com/user-attachments/assets/bcde5867-09cf-4bd9-942a-80cb9e81e9d8" />


3. Start Suricata:

<img width="539" height="74" alt="image" src="https://github.com/user-attachments/assets/1ee80549-1b3b-46fd-ba51-2d8140247075" />


4. Verify eve.json and filestore output.


## Splunk Setup

1. Install Splunk Enterprise or Splunk Universal Forwarder.

2. Configure inputs.conf:

## * For Sysmon logs (Windows VM):

<img width="569" height="152" alt="image" src="https://github.com/user-attachments/assets/60a4e3b6-341a-4510-89bc-ed5c5d34559c" />


## *For Suricata logs (Linux sensor):

<img width="551" height="145" alt="image" src="https://github.com/user-attachments/assets/56031696-fb6e-4615-a88d-4d91b42dd4db" />


3. Configure outputs.conf to forward logs to your Splunk indexer.

4. Install Splunk Add-ons for Sysmon and Suricata to parse fields correctly.

## Correlation in Splunk
1. Hash-based Correlation

Match Suricata fileinfo sha256 with Sysmon process/file hash.

<img width="579" height="187" alt="image" src="https://github.com/user-attachments/assets/3c9fe12f-96f4-4b9b-8aa3-b9a0e5949888" />


2. Flow/IP-based Correlation

Match Suricata download src/dest with Sysmon network events in a defined time window.


## Challenges & Limitations

* Suricata cannot inspect file contents over HTTPS unless TLS interception is used.

* Ensure time synchronization (NTP) across all hosts.

* Splunk field names may vary depending on installed add-ons — adjust SPL queries accordingly.

* eve.json can become large; enable only the necessary log types.


