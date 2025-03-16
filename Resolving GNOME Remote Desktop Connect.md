# Resolving GNOME Remote Desktop Connection Issues

This guide provides step-by-step instructions to troubleshoot and resolve issues related to GNOME Remote Desktop (RDP/VNC) on Linux.

## Steps to Resolve Connection Issues

### 1. Restart GNOME Remote Desktop Service
Run the following commands to restart and check the status of the service:
```bash
systemctl --user restart gnome-remote-desktop
systemctl --user status gnome-remote-desktop
```
Ensure that the service is running. If it is not active, check the logs for errors using:
```bash
journalctl --user -u gnome-remote-desktop --no-pager
```

### 2. Enable the Service to Start at Boot
To ensure that GNOME Remote Desktop starts automatically, enable it:
```bash
systemctl --user enable gnome-remote-desktop
```
Then start the service:
```bash
systemctl --user start gnome-remote-desktop
```

### 3. Fix Keyring Issues
GNOME Remote Desktop credentials are stored in the keyring. If you experience authentication issues, check your keyring settings.
- Open **Passwords and Keys** application.
- Ensure that **default keyring** or **login keyring** is unlocked.
- If necessary, delete and re-add the credentials.

If the login keyring is missing, link it to the default keyring:
```bash
ln -s ~/.local/share/keyrings/login.keyring ~/.local/share/keyrings/default.keyring
```
Then, restart the keyring service:
```bash
pkill gnome-keyring-daemon
/usr/bin/gnome-keyring-daemon --start --components=pkcs11,secrets,ssh
```

### 4. Verify Remote Desktop Settings
Check GNOME settings to ensure remote desktop access is properly configured:
- Open **Settings** → **Sharing**.
- Enable **Remote Desktop**.
- Select **RDP** or **VNC**, depending on your setup.
- Ensure authentication is properly configured (password or certificate-based login).

### 5. Confirm Network Availability
If you still cannot connect:
- Ensure the remote computer is powered on and connected to the same network.
- Check the firewall settings to allow incoming RDP or VNC connections:
```bash
sudo ufw allow 3389/tcp  # RDP
sudo ufw allow 5900/tcp  # VNC
sudo ufw reload
```
- If using SSH tunneling, verify your SSH connection:
```bash
ssh -L 3389:localhost:3389 user@remote_ip
```

### 6. Test Connection from Another Device
Try connecting from another computer to rule out client-side issues.
- For RDP: Use `Remmina` or `rdesktop`.
- For VNC: Use `tigervnc` or `vinagre`.

### 7. Review Logs for Errors
If issues persist, check logs for more details:
```bash
journalctl --user -u gnome-remote-desktop -r | less
```
This will display recent logs in reverse order for debugging.

## Conclusion
Following these steps should help resolve common issues with GNOME Remote Desktop. If problems persist, consider reinstalling GNOME Remote Desktop or checking for system updates.
```bash
sudo apt update && sudo apt upgrade -y
```
For advanced troubleshooting, refer to official GNOME documentation or community forums.

# Fixing Remote Desktop Connection Issues on Ubuntu 22.04

## Issue:
When using RDP or VNC on Ubuntu 22.04, the connection may fail due to the system not unlocking the keyring during an automatic login. This results in the VNC server auto-generating a new password each time you reboot.

## Steps to Fix:

### **1. Resetting the Keyring to Remove Password Protection**
1. Open **Passwords and Keys** (also called `seahorse`):  
   - Click on **"Activities"** (top-left corner)  
   - Search for **"Passwords and Keys"**  
   - Open it.
2. Find the **Default Keyring**.
3. Right-click on it and select **"Change Password"**.
4. Enter your **current system password** when prompted.
5. For the **new password**, leave it blank.
6. Accept the security warning that stored passwords will be unencrypted.

### **2. Setting Up Remote Desktop Credentials Again**
1. Open **Passwords and Keys** (`seahorse`).
2. Click **"New Password"** or `+` (Add button).
3. Choose **"Stored Password"**.
4. Enter the details:
   - **Name:** `xrdp`
   - **Username:** *(your Raspberry Pi username)*
   - **Password:** *(your Pi password)*
5. Click **Save**.

### **3. Restarting and Testing the Connection**
1. Ensure your Remote Desktop service is running:
   ```bash
   sudo systemctl restart xrdp
   sudo systemctl status xrdp
   ```
2. Try connecting via **Remmina** or **Windows RDP (`mstsc.exe`)**.

If the issue persists, check the system logs for errors:
```bash
journalctl -u xrdp --no-pager | tail -n 20
```
---
_Last updated: March 15, 2025_

