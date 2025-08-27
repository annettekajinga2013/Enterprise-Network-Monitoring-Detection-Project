# Splunk Installation on Windows

Prerequisites:

- Windows 10/11 or Windows Server 2016+

- At least 5GB free space and 4GB RAM

- Administrator privileges

Steps

1. Download Splunk Enterprise

* Go to: https://www.splunk.com/download

*Select Splunk Enterprise → Windows 64-bit MSI.

2. Run Installer

* Double-click the .msi file.

* Accept license agreement.

* Choose Custom Installation (optional) to select installation directory.

3. Set Admin Credentials

* During setup, you will be prompted to create:

 - Username (default: admin)
 - Password (Strong, remember this for later)

4. Start Splunk

* Splunk will install as a service.

* Access via:

 - Browser: http://localhost:8000

 - Username: admin

 - Password: the one you set.

5. Open Firewall Ports

Ensure port 8000 (web interface) and any forwarding ports (9997 for Universal Forwarder) are open.

# Splunk Installation on macOS

Prerequisites: 

- macOS Catalina or later

- 4GB RAM, 5GB free space

- Terminal access

Steps: 

1. Download Splunk Enterprise

bash

curl -O https://download.splunk.com/products/splunk/releases/latest/mac/splunk-latest-darwin-64.tgz

2. Extract & Move

bash

tar -xvzf splunk-latest-darwin-64.tgz
sudo mv splunk /Applications

3. Start Splunk

bash

cd /Applications/splunk/bin
sudo ./splunk start --accept-license

4. Set admin username and password when prompted.

2. Access Splunk

Browser: http://localhost:8000

# Splunk Installation on Linux

Prerequisites: 
- Ubuntu 20.04+, CentOS 7+, or Debian

- 4GB RAM, 5GB free

- Root or sudo access

Steps:
1. Download Splunk

bash

wget -O splunk-latest-linux-x86_64.tgz 'https://download.splunk.com/products/splunk/releases/latest/linux/splunk-latest-linux-x86_64.tgz'

2. Extract & Move

bash

tar -xvzf splunk-latest-linux-x86_64.tgz
sudo mv splunk /opt/

3. Start Splunk

bash

cd /opt/splunk/bin
sudo ./splunk start --accept-license

* Create admin credentials when prompted.

4. Enable Splunk to Start on Boot

bash

sudo ./splunk enable boot-start

5. Access Splunk

- Browser: http://<server-ip>:8000

# Install Splunk Universal Forwarder on Endpoints

The Universal Forwarder (UF) collects logs from endpoints and sends them to the Splunk indexer.

# Windows

1. Download UF: https://www.splunk.com/download/universal-forwarder

2. Install .msi and set:

 - Indexer IP: <Splunk_Server_IP>

- Port: 9997

3. Verify:

powershell

splunk status

# macOS/Linux

1. Download UF:

bash

wget -O splunkforwarder.tgz 'https://download.splunk.com/products/universalforwarder/releases/latest/linux/splunkforwarder-latest-linux-x86_64.tgz'

2. Extract & move:

bash

tar -xvzf splunkforwarder.tgz
sudo mv splunkforwarder /opt/

3. Start and configure:

bash

cd /opt/splunkforwarder/bin
sudo ./splunk start --accept-license
sudo ./splunk add forward-server <Indexer_IP>:9997 -auth admin:<password>
sudo ./splunk add monitor /var/log
sudo ./splunk enable boot-start


# Basic Configuration
After installation:

1. Set Data Inputs → Go to Settings → Data Inputs and add:

  -TCP/UDP
  -Files & directories
  -Syslog

2. Install Splunk Apps

   For network monitoring: Splunk App for Windows Infrastructure, Splunk Security Essentials, Zeek App for Splunk, or Suricata App for Splunk.

3. Verify Logs

   Search in Splunk Search & Reporting:

   ini

   index=* | stats count by sourcetype

