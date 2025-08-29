# Enterprise Network Monitoring & Detection (ENMD)

**Reverse Engineering Approach using Suricata, Sysmon, Splunk, and Tailscale**

---

## 1. Project Overview

The ENMD project implements an enterprise-scale monitoring and detection environment to:

- Capture **network-based file downloads**.
- Correlate them with **endpoint file executions**.
- Perform **centralized log analysis**.
- Use **Tailscale** for secure interconnectivity between nodes.

### Key Components

- **Suricata IDS (Ubuntu)** – Network-based detection and logging  
- **Sysmon (Windows)** – Endpoint process and file telemetry  
- **Splunk (Kali)** – Log ingestion, analysis, and correlation  
- **Tailscale** – Secure mesh networking for remote connectivity  

---

## 2. Network Architecture

The architecture integrates **Suricata**, **Sysmon**, and **Splunk** connected via **Tailscale** for encrypted communications.

### Data Flow

1. Suricata logs downloads into `eve.json`.
2. Sysmon logs process/file executions with SHA256 hashes.
3. Logs forwarded to Splunk via **Universal Forwarder (UF)**.
4. Correlation performed using **hash-based** and **flow/time-based** queries.

> **Diagram Placeholder**: Add network architecture diagram here.

---

## 3. Environment Setup

### Suricata (Ubuntu)

```bash
sudo apt update && sudo apt install -y suricata
# Edit /etc/suricata/suricata.yaml to enable:
# eve.json, fileinfo, filestore with SHA256
sudo systemctl enable --now suricata
Logs:

/var/log/suricata/eve.json

/var/log/suricata/filestore/

Sysmon (Windows)
powershell
Copy code
sysmon64.exe -accepteula -i sysmon-config.xml
Key features:

SHA256 hashing

FileCreateStreamHash enabled

Logs:

nginx
Copy code
Applications and Services Logs →
Microsoft → Windows → Sysmon → Operational
Splunk (Kali Server)
Enable receiving:

bash
Copy code
./splunk enable listen 9997 -auth admin:changeme
Add forwarder:

bash
Copy code
./splunk add forward-server <INDEXER_IP>:9997 -auth <user>:<pass>
./splunk add monitor /var/log/auth.log
./splunk list forward-server
Tailscale Mesh
bash
Copy code
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --authkey=<TS_AUTHKEY> --hostname=<HOST_TAG>
tailscale status
4. Correlation & Detection
Hash-Based Correlation (Suricata ↔ Sysmon)
spl
Copy code
index=suricata event_type=fileinfo
| eval file_sha256=fileinfo.sha256
| join type=left file_sha256 [ search index=windows EventCode=1
  | rex field=Hashes "SHA256=(?<sysmon_sha256>[A-Fa-f0-9]+)" ]
| where file_sha256=sysmon_sha256
| table _time src_ip dest_ip http.hostname http.url fileinfo.filename Image CommandLine file_sha256
Flow/IP-Time Correlation
spl
Copy code
index=suricata (event_type=http OR event_type=alert)
| eval five_min=_time - (_time % 300)
| stats values(http.hostname) as host, values(http.url) as urls, values(src_ip) as srcs, values(dest_ip) as dsts by five_min
| join five_min [ search index=windows (EventCode=3 OR EventCode=1) earliest=-10m latest=now()
  | stats values(Image) as images, values(DestinationIp) as win_dst by five_min ]
| table five_min host urls srcs dsts images win_dst
5. Troubleshooting & Challenges
Common Issues
Permission errors in Splunk UF directories

Connection refused on port 9997 (receiver disabled)

Large eve.json files slowing ingestion

HTTPS traffic not visible without TLS interception

Resolutions
Enabled Splunk receiving with:

bash
Copy code
./splunk enable listen 9997
Used sudo for UF commands

Implemented NTP for time sync

6. Reverse Engineering Notes
Promiscuous mode was initially enabled to capture all packets:

bash
Copy code
ip link show ens33
sudo ip link set ens33 promisc off
Alternatives:

Run Suricata directly on Kali

Route Windows ↔ Kali via Ubuntu (as gateway)

Use a virtual network tap/mirror

7. Results
Successful correlation of download → execution events

Secure connectivity achieved with Tailscale

Centralized Splunk monitoring functional for Suricata & Sysmon

8. Lessons Learned
Splunk indexers must be configured before UF deployment

Time synchronization (NTP) is critical for correlation

HTTPS monitoring requires TLS interception or endpoint analysis

9. Future Improvements
Implement TLS/SSL decryption for HTTPS

Automate deployments using Ansible/PowerShell DSC

Consider ELK/Wazuh for open-source scalability

10. Screenshots
Reserved placeholders for:

Suricata configuration & alerts

Sysmon event logs

Splunk correlation dashboard

Tailscale status
