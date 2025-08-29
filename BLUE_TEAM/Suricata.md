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
4. Forward `eve.json` 
