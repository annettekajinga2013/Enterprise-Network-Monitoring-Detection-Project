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
