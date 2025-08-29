### **1. Sigma Rule 

<img width="810" height="614" alt="image" src="https://github.com/user-attachments/assets/c5fd59f9-18ea-485f-b46a-531e75d2d528" />


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


# Debugger Attachment Detection – Splunk Query

## Query

```spl
index=linux sourcetype=process_creation
(Image="/gdb" OR Image="/lldb" OR Image="/strace")
| table _time, host, user, Image, CommandLine, pid
| sort -_time

---




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
