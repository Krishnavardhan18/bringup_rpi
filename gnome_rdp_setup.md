# GNOME Remote Desktop (RDP/VNC) Setup After IP Address Change

This guide walks you through fixing GNOME Remote Desktop access after an IP address change, especially useful when using tools like RealVNC or Windows RDP.

---

## 🔧 Issue Summary

When the IP address of your server changes (typically in dynamic IP environments), Remote Desktop (RDP/VNC) may stop working due to keyring or credential misconfigurations. This guide helps reset and fix it.

---

## ✅ Step-by-Step Guide

### 1. **Fixing GNOME Keyring Issues**

If you deleted the default keyring or GNOME credential store, follow these steps:

#### a. **Recreate Keyring**

Open *Passwords and Keys* (a.k.a. Seahorse):

```bash
seahorse &
```

* If "Login" keyring is missing, create a new keyring.
* Set its password same as your user password.

#### b. **Set Password to Empty (Optional but helpful)**

To avoid constant password prompts:

* Right-click `Login` or `Default` keyring
* Select `Change Password`
* Enter current password
* Leave new password empty
* Accept the warning

---

### 2. **Ensure Remote Desktop is Enabled**

```bash
gsettings set org.gnome.desktop.remote-desktop.rdp enable true
gsettings set org.gnome.desktop.remote-desktop.vnc enable true
```

You can also check status with:

```bash
gsettings list-recursively org.gnome.desktop.remote-desktop
```

---

### 3. **Check GNOME Remote Desktop Daemon**

```bash
systemctl --user status gnome-remote-desktop.service
```

If not running, start and enable it:

```bash
systemctl --user start gnome-remote-desktop.service
systemctl --user enable gnome-remote-desktop.service
```

If you see permission errors:

```bash
loginctl enable-linger $USER
```

---

### 4. **Open RDP Ports**

Allow necessary ports:

```bash
sudo ufw allow 3389/tcp
```

Check listening:

```bash
ss -tulpn | grep 3389
```

---

### 5. **Connect from Client**

Use RealVNC or Windows RDP with:

```
<Your IP>:3389
```

Ensure the system is not asleep (use `power settings` to disable sleep).

---

## 💡 Tips

* Use static IP or configure DHCP reservation to avoid repeating this
* Use VNC if RDP fails: Install `tigervnc-standalone-server`
* Use `x11vnc` or `realvnc-server` for advanced use cases

---

## ✅ Status

* [x] GNOME Keyring reconfigured
* [x] GNOME Remote Desktop re-enabled
* [x] Port opened and service verified
* [x] Remote login tested

---

Next up: SSH password-less login setup with VS Code support.
