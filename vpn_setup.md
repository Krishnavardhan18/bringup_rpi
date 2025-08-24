# Remote VLSI Workstation Access Setup

A comprehensive guide to set up secure remote access to your Ubuntu workstation for VLSI development work using Tailscale VPN with RDP and VNC options.

## Overview

This setup provides secure, worldwide access to your Ubuntu workstation through:
- **Tailscale VPN**: Secure mesh network for remote connectivity
- **RDP (Remote Desktop Protocol)**: Primary remote desktop solution
- **VNC (Virtual Network Computing)**: Alternative remote desktop option

## Prerequisites

- Ubuntu 22.04 server/workstation
- Windows client machine (or any OS with RDP/VNC clients)
- Internet connection on both devices

## Part 1: Tailscale VPN Setup

### On Ubuntu Server

```bash
# Install Tailscale
curl -fsSL https://tailscale.com/install.sh | sh

# Start Tailscale
sudo tailscale up
```

This will provide a URL for authentication. Open it in a browser and sign in with:
- Google account
- Microsoft account
- GitHub account
- Or create a Tailscale account

### On Client Device

**Windows:**
1. Download Tailscale from https://tailscale.com/download
2. Install and run `tailscale up`
3. Authenticate using the same account

**Linux:**
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

### Get Your Tailscale IP

```bash
# On Ubuntu server
tailscale ip -4
```

Note this IP address (format: 100.x.x.x) - you'll use this to connect remotely.

## Part 2: RDP Setup (Recommended)

### Install xrdp on Ubuntu

```bash
# Install xrdp
sudo apt update
sudo apt install xrdp

# Start and enable service
sudo systemctl enable xrdp
sudo systemctl start xrdp

# Add user to ssl-cert group
sudo adduser $USER ssl-cert

# Configure session
echo "xfce4-session" > ~/.xsession
chmod +x ~/.xsession

# Restart xrdp
sudo systemctl restart xrdp
```

### Connect via RDP

**From Windows:**
1. Open "Remote Desktop Connection" (type `mstsc`)
2. Computer: `your-tailscale-ip:3389`
3. Username: Your Ubuntu username
4. Password: Your Ubuntu password

**From Linux:**
```bash
remmina
# Or
xfreerdp /v:your-tailscale-ip /u:username
```

## Part 3: VNC Setup (Alternative)

### Option A: RealVNC Server

```bash
# Download RealVNC Server
wget https://downloads.realvnc.com/download/file/vnc.files/VNC-Server-7.12.0-Linux-x64.deb

# Install
sudo dpkg -i VNC-Server-7.12.0-Linux-x64.deb
sudo apt-get install -f

# Enable service
sudo systemctl enable vncserver-x11-serviced
sudo systemctl start vncserver-x11-serviced

# Configure
sudo vncinitconfig -service-daemon
```

**Connect:** Use RealVNC Viewer to connect to `your-tailscale-ip:5900`

### Option B: TightVNC Server

```bash
# Install TightVNC
sudo apt install tightvncserver

# Start VNC server (sets password on first run)
vncserver :1 -geometry 1920x1080 -depth 24

# Configure desktop environment
vncserver -kill :1
nano ~/.vnc/xstartup
```

Add to `~/.vnc/xstartup`:
```bash
#!/bin/bash
xrdb $HOME/.Xresources
startxfce4 &
```

```bash
# Make executable and restart
chmod +x ~/.vnc/xstartup
vncserver :1 -geometry 1920x1080 -depth 24
```

**Connect:** Use any VNC client to connect to `your-tailscale-ip:5901`

## Part 4: Security Best Practices

### Firewall Configuration

```bash
# Check firewall status
sudo ufw status

# If inactive, that's fine for Tailscale-only access
# If you need local network access too:
sudo ufw allow 3389/tcp  # RDP
sudo ufw allow 5900:5910/tcp  # VNC range
```

### Tailscale Management

```bash
# Check connected devices
tailscale status

# Temporarily disconnect
tailscale down

# Reconnect
tailscale up

# Logout completely
tailscale logout
```

## Troubleshooting

### RDP Issues

```bash
# Check if xrdp is running
sudo systemctl status xrdp

# Check if port is listening
sudo ss -tlnp | grep 3389

# Restart xrdp
sudo systemctl restart xrdp
```

### VNC Issues

```bash
# List VNC sessions
vncserver -list

# Kill and restart VNC
vncserver -kill :1
vncserver :1 -geometry 1920x1080 -depth 24

# Check VNC ports
sudo ss -tlnp | grep 590
```

### Tailscale Issues

```bash
# Check Tailscale status
sudo tailscale status

# Restart Tailscale
sudo tailscale down
sudo tailscale up

# Check logs
sudo journalctl -u tailscaled
```

## VLSI Workflow Integration

### For VLSI Development Work

Once connected via RDP or VNC, you can:
- Run your EDA tools (Cadence, Synopsys, etc.)
- Execute testbenches and simulations
- View waveforms in gtkwave
- Access all your design files and scripts

### Performance Tips

- **RDP**: Better for general desktop use, office applications
- **VNC**: Better for graphics-intensive applications like waveform viewers
- Use wired internet connection when possible for better performance
- Adjust screen resolution based on your internet speed

## Network Impact

This setup has minimal impact on your home network:
- VPN only uses bandwidth when actively connected
- Typical usage for VLSI work: 1-10 Mbps
- Other devices on your network are unaffected

## Managing Access

### Temporary Disconnect (Recommended for Office Use)

```bash
# Disconnect when not needed
tailscale down

# Reconnect when needed
tailscale up
```

### Access Control

- Tailscale admin console: https://login.tailscale.com/admin/machines
- View and manage all connected devices
- Disable/remove devices as needed
- Set up device approval requirements

## Conclusion

You now have secure, worldwide access to your Ubuntu workstation optimized for VLSI development work. The combination of Tailscale VPN with RDP/VNC provides both security and flexibility for remote engineering tasks.

## Additional Resources

- [Tailscale Documentation](https://tailscale.com/kb/)
- [xrdp Configuration](https://github.com/neutrinolabs/xrdp)
- [VNC Setup Guides](https://www.realvnc.com/en/connect/docs/)

---

**Note:** Always keep your system updated and follow your organization's IT security policies when setting up remote access solutions.