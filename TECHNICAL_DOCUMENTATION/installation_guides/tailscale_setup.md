# Tailscale Setup with Configuration for 3 Machines

## Prerequisites
- Admin/root access on all three machines
- Internet connection
- Tailscale account (Google, Microsoft, GitHub, or email sign-in)

## Machines
- **Machine A**: Windows 10/11
- **Machine B**: Linux (Ubuntu/Debian)
- **Machine C**: macOS

---

## 1. Install Tailscale

### Windows
```powershell
# Download and install Tailscale
Invoke-WebRequest -Uri "https://pkgs.tailscale.com/stable/tailscale-setup-latest.exe" -OutFile "tailscale-setup.exe"
Start-Process -FilePath ".\tailscale-setup.exe" -Wait
```

Launch Tailscale from Start Menu and sign in.

### Linux (Ubuntu/Debian)
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```
Follow the authentication URL to sign in.

### macOS
```bash
brew install --cask tailscale
sudo tailscale up
```

Open the authentication link and sign in.

---

## 2. Configure Tailscale Network

### Designate a Subnet Router (Optional)
Choose one machine (e.g., Linux) to route your LAN:
```bash
sudo tailscale up --advertise-routes=192.168.1.0/24
```
Enable it in the Tailscale Admin Console.

### Enable MagicDNS (Recommended)
- Go to [Tailscale Admin Console](https://login.tailscale.com/admin/dns)
- Enable MagicDNS and assign hostnames (e.g., `win-machine`, `linux-node`, `mac-client`)

---

## 3. Test Connectivity
On any machine, run:
```bash
ping linux-node.tailnet-name.ts.net
ping win-machine.tailnet-name.ts.net
ping mac-client.tailnet-name.ts.net
```

For SSH:
```bash
ssh user@linux-node.tailnet-name.ts.net
```

---

## 4. Optional: Set One Machine as Exit Node
Designate one device (e.g., macOS):
```bash
sudo tailscale up --advertise-exit-node
```

Enable it from the Admin Console for others to use as an internet gateway.

---

## 5. Autostart Tailscale

### Windows
Tailscale automatically starts with Windows.

### Linux
```bash
sudo systemctl enable tailscaled
sudo systemctl start tailscaled
```

### macOS
Enabled by default when installed via Homebrew Cask.

---

## 6. Verify Status
```bash
tailscale status
tailscale ping linux-node
tailscale ping mac-client
tailscale ping win-machine
```

---

## Troubleshooting
- **Firewall issues?** Allow Tailscale through local firewalls.
- **Cannot route traffic?** Check subnet routing or exit node settings.
- **Authentication issues?** Re-run `sudo tailscale up`.

---

## Example GitHub Repo Structure
```
tailscale-3-machines-setup/
│   README.md
│   LICENSE
│   .gitignore
└── configs/
    ├── linux-subnet-router.md
    ├── windows-setup.md
    └── macos-setup.md
```

---

## License
MIT License.

