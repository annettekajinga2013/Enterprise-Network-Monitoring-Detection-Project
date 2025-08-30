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

Suricata Setup (Linux Sensor Recommended)

Install Suricata:

sudo apt install suricata


Edit /etc/suricata/suricata.yaml to enable eve.json, fileinfo, and filestore.
Example configuration snippet:

outputs:
  - eve-log:
      enabled: yes
      filetype: regular
      filename: eve.json
      types:
        - alert
        - http
        - dns
        - fileinfo
        - files:
            file-store:
              enabled: yes
              dir: filestore
              force-hash: [sha256]


Start Suricata:

sudo systemctl enable --now suricata


Verify eve.json and filestore output.

Splunk Setup

Install Splunk Enterprise or Splunk Universal Forwarder.

Configure inputs.conf:

For Sysmon logs (Windows VM):

[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = windows
sourcetype = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational


For Suricata logs (Linux sensor):

[monitor:///var/log/suricata/eve.json]
disabled = false
index = suricata
sourcetype = suricata:eve


Configure outputs.conf to forward logs to your Splunk indexer.

Install Splunk Add-ons for Sysmon and Suricata to parse fields correctly.

Correlation in Splunk
1. Hash-based Correlation

Match Suricata fileinfo sha256 with Sysmon process/file hash.

index=suricata event_type=fileinfo
| eval file_sha256=fileinfo.sha256
| join type=left file_sha256 [ search index=windows EventCode=1
| rex field=Hashes "SHA256=(?<sysmon_sha256>[A-Fa-f0-9]+)" ]
| where file_sha256=sysmon_sha256

2. Flow/IP-based Correlation

Match Suricata download src/dest with Sysmon network events in a defined time window.

Challenges & Limitations

Suricata cannot inspect file contents over HTTPS unless TLS interception is used.

Ensure time synchronization (NTP) across all hosts.

Splunk field names may vary depending on installed add-ons — adjust SPL queries accordingly.

eve.json can become large; enable only the necessary log types.


