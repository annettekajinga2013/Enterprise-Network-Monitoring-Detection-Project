# Sigma Rules – Reverse Engineering Detection

This repository contains Sigma rules and Splunk queries to detect suspicious activities related to reverse engineering and debugging on Linux and Windows systems.

## Rules Included

1. **Debugger Attachment Detection (Linux)**  
   Detects processes attaching debuggers (gdb, lldb, strace).

2. **Unusual Port Activity (Linux/Windows)**  
   Detects network connections on uncommon ports often used by reverse engineering tools or backdoors.

## Usage

1. Clone the repository:
   ```bash
   git clone https://github.com/your-org/sigma-reverse-engineering.git
Navigate to the rules folder:

bash
Copy
Edit
cd sigma-reverse-engineering/rules/reverse-engineering
Convert Sigma rules to your SIEM format using sigmac:

bash
Copy
Edit
sigmac -t splunk debugger-attachment-detection.yml
sigmac -t splunk unusual-port-activity.yml
Use the .spl queries directly in Splunk to monitor for suspicious activity.

# Tags
MITRE ATT&CK: T1518, T1041, T1571, T1003

Platforms: Linux, Windows

yaml

---

### **2. Sigma Rules**

**a) debugger-attachment-detection.yml**
```yaml
title: Debugger Attachment Detection
id: 4a1c34e2-f67a-11ee-8c99-0242ac120002
description: Detects processes attaching debuggers (gdb, lldb, strace) to running processes.
status: experimental
author: Security Team
date: 2025/08/25
logsource:
  product: linux
  category: process_creation
detection:
  selection:
    Image|endswith:
      - "/gdb"
      - "/lldb"
      - "/strace"
  condition: selection
level: high
tags:
  - attack.t1518
  - attack.discovery


---
Splunk Query: Debugger Attachment Detection
spl
index=linux sourcetype=process_creation
(Image="/gdb" OR Image="/lldb" OR Image="/strace")
| table _time, host, user, Image, CommandLine, pid
| sort -_time

---

# Explanation

#index=linux sourcetype=process_creation

Searches your Linux process logs.

#(Image="/gdb" OR Image="/lldb" OR Image="/strace")

Matches processes where the Image field ends with debugger tools.

#table _time, host, user, Image, CommandLine, pid

Displays relevant details: time, host, user, process image, command line, process ID.

#sort -_time

Shows the latest suspicious activity at the top.
