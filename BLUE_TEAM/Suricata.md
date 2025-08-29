# Suricata and Splunk Integration – Enterprise Network Monitoring Project

This section documents the implementation and configuration of **Suricata** and **Splunk** for network detection and monitoring.

---

## 1. Suricata – Network Intrusion Detection System (NIDS)

### Purpose of Suricata in the Project

- **Network Traffic Inspection**: Monitored network traffic in real-time, capturing packets and analyzing them against defined rules to detect malicious or suspicious activity.  
- **Threat Detection**: Detected brute-force attempts, suspicious HTTP/HTTPS traffic, malicious DNS queries, and lateral movement.  
- **Log Generation for Correlation**: Generated JSON/EVE logs, forwarded to Splunk for centralized analysis alongside Sysmon endpoint logs.  
- **Protocol Analysis**: Parsed HTTP, DNS, TLS, SMB, and more to identify anomalies in legitimate traffic.  

### How Suricata Was Used

- **Deployment Mode**:  
  - Primarily ran in IDS mode (passive monitoring).  
  - IPS mode (with iptables/NFQUEUE) tested in a controlled environment.  

- **Rule Sets Used**:  
  - Emerging Threats (ET Open).  
  - Custom rules based on reverse-engineered malware and lab attack scenarios.  

- **Integration Points**:  
  - Logs forwarded via Filebeat/Logstash or directly to Splunk HTTP Event Collector (HEC).  
  - Worked alongside Sysmon for a combined network + endpoint threat view.  

### Limitations & Challenges

High CPU usage on busy networks.

False positives during early detection phase.

Frequent rule updates required.

Parsing adjustments needed in Splunk (props.conf, transforms.conf).

## Troubleshooting Performed

Increased packet buffers (-w flag, af-packet tuning).

Fixed NTP sync issues between Suricata and Splunk.

Corrected log parsing errors in Splunk.

Disabled overly broad rules.


