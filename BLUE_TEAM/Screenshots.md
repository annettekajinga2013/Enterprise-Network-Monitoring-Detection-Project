

---
## Sreenshots

<img width="1336" height="805" alt="image" src="https://github.com/user-attachments/assets/73d3bcdd-2c67-4e3d-840c-238fb4c20ebc" />
1.1 shows the persistent that was created(meterpreter test in the hkcu)



---

<img width="1333" height="792" alt="image" src="https://github.com/user-attachments/assets/4fcd3cfc-46ea-4f74-89a9-bc30d6c5fa35" />


<img width="1353" height="809" alt="image" src="https://github.com/user-attachments/assets/c8596ea6-7a09-4029-8de6-c357163a0030" />

<img width="1337" height="831" alt="image" src="https://github.com/user-attachments/assets/bc7a2a2b-e55c-41b7-b4c9-67021d4f4112" />
# Meterpreter Session Analysis – Windows Target

This document analyzes the Meterpreter activity captured in the provided screenshot.  
The session demonstrates **post-exploitation attempts** from a Kali Linux machine against a Windows host.

---

## Overview

- **Attacker OS:** Kali Linux (`cyberagentberry@kali`)
- **Victim OS:** Windows (`C:\Users\apple\Downloads`)
- **Tool Used:** Meterpreter (Metasploit Framework)
- **Objective:** Upload tools, execute commands, modify registry, and establish persistence.

---

## Key Actions Observed

### 1. Initial Commands
- `pwd` – Displayed current directory: `C:\Users\apple\Downloads`
- `whoami` – Not recognized in Meterpreter; requires `getuid`
- `execute -f notepad.exe` – Successfully created process (PID 10084)

### 2. File Upload & Execution
- Uploaded **Netcat** (`nc.exe`) to:



<img width="1350" height="808" alt="image" src="https://github.com/user-attachments/assets/a5ffd08a-3c30-4201-87c5-52e5b7137159" />


<img width="1359" height="806" alt="image" src="https://github.com/user-attachments/assets/d817d89e-e6ac-472c-a31a-cc2af82541b4" />

<img width="1348" height="808" alt="image" src="https://github.com/user-attachments/assets/f53f47c9-afdc-4bc3-881f-410514d009ed" />



