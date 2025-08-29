# Splunk – Centralized SIEM Platform

## Purpose and Role
- **Centralized log aggregation, indexing, and analysis platform**  
- Collected, normalized, and correlated logs from **Suricata, Sysmon, Zeek, and Tailscale**  
- Enabled search, visualization, and alerting for **real-time detection and threat hunting**

---

## Key Functions

### Log Collection & Aggregation
- Ingested **Suricata EVE JSON alerts**
- Collected **Sysmon logs** from Windows endpoints via **Universal Forwarder**
- Integrated **Zeek/Tailscale logs via HTTP Event Collector**

### Real-time Event Correlation
- Linked **Suricata network alerts** to endpoint activity
- Used **SPL (Search Processing Language)** to correlate cross-source events

### Threat Detection
- Converted **Sigma rules into SPL queries**
- Detected **lateral movement, C2 traffic, and suspicious process executions**

### Visualization & Dashboards
- Dashboards for **network trends, Suricata alerts, and endpoint anomalies**
- Real-time visualization of:
  - **SSH brute-force attempts**
  - **DNS anomalies**
- **Alerts:**
  - Configured **email & Slack alerts** for critical events

---

## Limitations & Challenges
- High data volume from Suricata caused **storage strain**
- **False positives** in initial rule sets

## Challenges
-  Root Cause Analysis
 The main issues identified were:
 • File permission errors on Splunk Forwarder installation directory.
 • Splunk Forwarder daemon running under restricted user permissions.
 • Receiving port 9997 not enabled on Splunk Enterprise (missing receiving configuration).

 ## Resolution Steps
 Steps needed to resolve the setup:
 1. Re-run Splunk Forwarder commands with sudo to avoid ownership issues.
 2. On Splunk Enterprise, enable receiving:
 ./splunk enable listen 9997 -auth admin:changeme
 3. Restart Splunk Enterprise to apply port listening.
 4. Re-run on client:
 ./splunk add forward-server 172.20.10.4:9997 -auth cyberagentberry:password
 ./splunk add monitor /var/log/auth.log
 5. Verify connection with './splunk list forward-server'

---

## Outcomes
- Achieved **real-time detection of network threats**
- Reduced **investigation time by 40%** using correlated views
- Delivered a **reusable Splunk dashboard template** for enterprise use



