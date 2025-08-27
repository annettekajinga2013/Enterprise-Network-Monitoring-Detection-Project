
# Suricata + Forwarder Setup (Windows, Linux, macOS)

Turnkey guide to install **Suricata IDS/IPS** and forward its logs (eve.json) to a SIEM using **Filebeat** or the **Splunk Universal Forwarder**.

> **Note on iOS:** Suricata does not run on iOS (iPhone/iPad). Use macOS (Intel/Apple Silicon) instead.

---

## Contents

* [What You’ll Get](#what-youll-get)
* [Supported Platforms](#supported-platforms)
* [Prerequisites](#prerequisites)
* [Quick Start](#quick-start)
* [Install Suricata](#install-suricata)

  * [Windows](#windows)
  * [Linux — Debian/Ubuntu](#linux--debianubuntu)
  * [Linux — RHEL/CentOS/Rocky](#linux--rhelcentosrocky)
  * [macOS](#macos)
* [Run Modes (IDS/IPS)](#run-modes-idsips)
* [Rules Management](#rules-management)
* [Log Forwarding Options](#log-forwarding-options)

  * [Forward to Elastic via Filebeat](#forward-to-elastic-via-filebeat)
  * [Forward to Splunk via Universal Forwarder](#forward-to-splunk-via-universal-forwarder)
  * [Forward via Syslog](#forward-via-syslog)
* [Service Management](#service-management)
* [Troubleshooting](#troubleshooting)
* [Uninstall / Clean Up](#uninstall--clean-up)

---

## What You’ll Get

* A working **Suricata** sensor capturing network traffic
* **`eve.json`** event stream (alerts, flows, DNS, HTTP, TLS, etc.)
* Log shipping to your **SIEM** (Elastic or Splunk examples included)
* Copy‑paste configs and commands

---

## Supported Platforms

* **Windows 10/11** (Npcap recommended)
* **Linux** (Debian/Ubuntu, RHEL/CentOS/Rocky)
* **macOS** (Intel & Apple Silicon)

---

## Prerequisites

* Admin/root privileges
* Network interface name (e.g., `Ethernet`, `eth0`, `en0`)
* **Packet capture** driver:

  * **Windows:** [Npcap](https://nmap.org/npcap/)
  * **Linux:** AF﻿-PACKET (kernel built‑in)
  * **macOS:** Native `pcap`

> **Tip:** On Windows, prefer **Npcap** (in WinPcap compatibility mode if needed).

---

## Quick Start

### TL;DR

1. Install Suricata for your OS
2. Point `suricata.yaml` to your NIC
3. Start Suricata (IDS mode)
4. Forward `eve.json` to SIEM (Filebeat or Splunk UF)

**Default eve.json locations**

* Windows: `C:\\Program Files\\Suricata\\log\\eve.json`
* Linux: `/var/log/suricata/eve.json`
* macOS: `/usr/local/var/log/suricata/eve.json` (Homebrew)

---

## Install Suricata

### Windows

1. **Install Npcap** (recommended): [https://nmap.org/npcap/](https://nmap.org/npcap/)
2. **Download Suricata MSI**: [https://suricata.io/download/](https://suricata.io/download/)
3. **Run Installer as Administrator** and complete setup.

**Config file**: `C:\\Program Files\\Suricata\\suricata.yaml`

**Find your adapter** (PowerShell):

```powershell
Get-NetAdapter | Where-Object { $_.Status -eq 'Up' } | Select-Object Name, InterfaceDescription
```

**Set interface** in `suricata.yaml` (Windows uses `pcap` by default):

```yaml
pcap:
  - interface: "Ethernet"   # replace with your adapter Name
```

**Run (terminal as Admin):**

```powershell
cd "C:\\Program Files\\Suricata"
./suricata.exe -c .\suricata.yaml -i "Ethernet"
```

> To install as a Windows Service, use NSSM or a scheduled task, or rely on the MSI service if available in your build.

---

### Linux — Debian/Ubuntu

```bash
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:oisf/suricata-stable -y
sudo apt update
sudo apt install -y suricata
```

**Config file**: `/etc/suricata/suricata.yaml`

**Select interface (AF-PACKET recommended)**

```yaml
af-packet:
  - interface: eth0   # your NIC
    cluster-id: 99
    cluster-type: cluster_flow
    defrag: yes
```

**Enable & start**

```bash
sudo systemctl enable suricata
sudo systemctl start suricata
sudo systemctl status suricata --no-pager
```

---

### Linux — RHEL/CentOS/Rocky

```bash
sudo dnf install -y epel-release
sudo dnf install -y suricata
```

**Config**: `/etc/suricata/suricata.yaml` (AF-PACKET as above)

**Service**

```bash
sudo systemctl enable --now suricata
```

---

### macOS

```bash
# Install Homebrew if not present: https://brew.sh
brew update
brew install suricata
```

**Config**: `/usr/local/etc/suricata/suricata.yaml` (Intel) or `/opt/homebrew/etc/suricata/suricata.yaml` (Apple Silicon)

**Find interface**

```bash
networksetup -listallhardwareports
```

**Run**

```bash
sudo suricata -c /usr/local/etc/suricata/suricata.yaml -i en0
# or on Apple Silicon
sudo suricata -c /opt/homebrew/etc/suricata/suricata.yaml -i en0
```

---

## Run Modes (IDS/IPS)

* **IDS (passive):** monitor traffic, generate alerts
* **IPS (inline):** block traffic. On Linux, enable `af-packet` with `inline: yes` and set iptables/nftables if needed.

**Example (Linux, inline IPS)**

```yaml
af-packet:
  - interface: eth0
    cluster-id: 99
    cluster-type: cluster_qm
    defrag: yes
    use-mmap: yes
    tpacket-v3: yes
    # inline mode
    copy-mode: ips
    copy-iface: eth0
```

> Inline mode requires appropriate network placement (bridge/tap) or NFQUEUE; consult Suricata docs for production IPS design.

---

## Rules Management

* Rules dir (Linux): `/etc/suricata/rules/`
* Update rules with **suricata-update** (installed with Suricata on many distros):

```bash
sudo suricata-update
sudo systemctl restart suricata
```

Add custom rules to `local.rules` and include it in `suricata.yaml`.

---

## Log Forwarding Options

Suricata writes structured JSON to **eve.json**. Choose one shipper below.

### Forward to Elastic via Filebeat

**Windows**

1. Download Filebeat for Windows: [https://www.elastic.co/downloads/beats/filebeat](https://www.elastic.co/downloads/beats/filebeat)
2. Edit `filebeat.yml` to add an input for `eve.json` and your outputs (Elasticsearch or Logstash).

```yaml
filebeat.inputs:
  - type: filestream
    id: suricata-eve
    enabled: true
    paths:
      - 'C:/Program Files/Suricata/log/eve.json'
    parsers:
      - ndjson:
          overwrite_keys: true
          add_error_key: true
          message_key: log

processors:
  - decode_json_fields:
      fields: ['message']
      process_array: true
      max_depth: 10
      target: ''
      overwrite_keys: true

output.elasticsearch:
  hosts: ["https://ELASTIC_HOST:9200"]
  username: elastic
  password: YOUR_PASSWORD
  ssl.verification_mode: certificate
```

**Linux/macOS** (paths change only)

```yaml
filebeat.inputs:
  - type: filestream
    id: suricata-eve
    enabled: true
    paths:
      - /var/log/suricata/eve.json   # Linux
      # - /usr/local/var/log/suricata/eve.json  # macOS (Intel)
      # - /opt/homebrew/var/log/suricata/eve.json  # macOS (Apple Silicon)

output.elasticsearch:
  hosts: ["https://ELASTIC_HOST:9200"]
  username: elastic
  password: YOUR_PASSWORD
```

**Enable and start Filebeat**

```bash
# Windows (PowerShell as Admin)
& "C:\\Program Files\\Filebeat\\install-service-filebeat.ps1"
Start-Service filebeat

# Linux
sudo systemctl enable --now filebeat

# macOS (Homebrew)
brew services start filebeat
```

---

### Forward to Splunk via Universal Forwarder

#### Windows (UF + monitor eve.json)

1. Download **Splunk Universal Forwarder** MSI: [https://www.splunk.com/en\_us/download/universal-forwarder.html](https://www.splunk.com/en_us/download/universal-forwarder.html)
2. Install (PowerShell as Admin):

```powershell
$msi = "C:\\Users\\Public\\splunkforwarder.msi"   # path to your MSI
'time to install UF'
msiexec /i $msi AGREETOLICENSE=Yes DEPLOYMENT_SERVER=ds.yourdomain.local:8089 /qn
```

3. Configure outputs (to your indexer/heavy forwarder): `C:\\Program Files\\SplunkUniversalForwarder\\etc\\system\\local\\outputs.conf`

```conf
[tcpout]
defaultGroup = default-autolb-group

tcpout:default-autolb-group]
server = indexer1.yourorg.local:9997,indexer2.yourorg.local:9997
sslCertPath = $SPLUNK_HOME\etc\auth\server.pem
sslPassword = CHANGEME
sslVerifyServerCert = true
```

4. Monitor Suricata `eve.json`: `C:\\Program Files\\SplunkUniversalForwarder\\etc\\system\\local\\inputs.conf`

```conf
[monitor://C:\\Program Files\\Suricata\\log\\eve.json]
index = suricata
sourcetype = suricata:json
disabled = false
```

5. Start/restart UF:

```powershell
& "C:\\Program Files\\SplunkUniversalForwarder\\bin\\splunk.exe" restart
```

#### Linux (UF + monitor eve.json)

```bash
# Example for .deb (adjust for .rpm)
sudo dpkg -i splunkforwarder-*.deb
sudo /opt/splunkforwarder/bin/splunk start --accept-license
sudo /opt/splunkforwarder/bin/splunk enable boot-start

# Add indexer(s)
sudo /opt/splunkforwarder/bin/splunk add forward-server indexer1.yourorg.local:9997 -auth admin:changeme

# Set outputs (alternative to CLI): /opt/splunkforwarder/etc/system/local/outputs.conf

# Monitor eve.json
sudo tee /opt/splunkforwarder/etc/system/local/inputs.conf <<'CONF'
[monitor:///var/log/suricata/eve.json]
index = suricata
sourcetype = suricata:json
disabled = false
CONF

# Restart UF
sudo /opt/splunkforwarder/bin/splunk restart
```

#### Sourcetype suggestion (Splunk)

Use a JSON sourcetype (e.g., `suricata:json`) and enable **line‑breaking** for JSON events if needed. Consider installing the Splunk Add‑on for Suricata (if available) or create field extractions based on `EVAL`/`KV_MODE=json`.

---

### Forward via Syslog

In `suricata.yaml`, enable the syslog output (less rich than JSON):

```yaml
outputs:
  - syslog:
      enabled: yes
      facility: local0
      level: Info
      server: 192.0.2.10
      port: 514
      format: "legacy"
```

> Prefer `eve.json` for structured analytics; use syslog for legacy collectors.

---

## Service Management

**Windows**

```powershell
# If installed as a service
Get-Service *suricata*
Restart-Service suricata
```

**Linux**

```bash
sudo systemctl status suricata
sudo journalctl -u suricata -e
```

**macOS (Homebrew)**

```bash
brew services list
brew services restart suricata
```

---

## Troubleshooting

* **No events in eve.json:**

  * Confirm the interface name is correct and **UP**
  * Run Suricata with higher verbosity: `-vvv`
  * Check permissions and path to `suricata.yaml`
* **High packet loss:**

  * Use AF‑PACKET with `tpacket-v3` on Linux
  * Increase capture buffers; disable NIC offloads if needed
* **UF/Filebeat not shipping:**

  * Verify service status; check UF/Filebeat logs
  * Confirm network egress to indexer/Elasticsearch
* **Splunk sourcetype issues:**

  * Set `sourcetype=suricata:json` and ensure `INDEXED_EXTRACTIONS=json` or `KV_MODE=json` at search‑time

---

## Uninstall / Clean Up

**Windows**: Remove via *Apps & features*; delete `C:\\Program Files\\Suricata` logs if desired.

**Linux**:

```bash
sudo systemctl stop suricata
sudo apt purge suricata -y   # or: sudo dnf remove suricata -y
sudo rm -rf /var/log/suricata
```

**macOS**:

```bash
brew uninstall suricata
rm -rf /usr/local/var/log/suricata /opt/homebrew/var/log/suricata
```

---

## Appendix: Minimal `eve.json` output (ensure enabled)

```yaml
outputs:
  - eve-log:
      enabled: yes
      filetype: regular
      filename: eve.json
      community-id: true
      types: [alert, flow, dns, http, tls, ssh, ftp, fileinfo]
```

---

**Next Steps**

* Add dashboards in Elastic/Splunk for Suricata alerts & flows
* Automate deployment with Ansible or SCCM/Intune
* Consider span/tap/virtual mirroring for traffic visibility

