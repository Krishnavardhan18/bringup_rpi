# README: Dell Laptop Setup Guide

## **Overview**
This document outlines the steps taken to set up the old Dell laptop with new hardware, Linux installation, disk partitioning, remote access configuration, and Wake-on-LAN setup.

---

## **1. Hardware Upgrades**
### **Replaced Components:**
- **New SSD (2TB) installed** (replacing old HDD)
- **Upgraded RAM** to enhance performance

---

## **2. Ubuntu Linux Installation**
### **Steps to Install Ubuntu:**
1. **Create a Bootable USB:**
   - Use [Rufus](https://rufus.ie/) (Windows) or `dd` command (Linux/macOS) to write the Ubuntu ISO.
   - Example:
     ```bash
     sudo dd if=ubuntu-22.04.iso of=/dev/sdX bs=4M status=progress
     ```
2. **Boot into Ubuntu Installer:**
   - Access BIOS (`F2` or `F12` on boot) and set USB as first boot device.
   - Select *Install Ubuntu* and follow on-screen instructions.

---

## **3. Disk Partitioning**
### **Desired Partition Structure:**
| Mount Point       | Size Allocation |
|-------------------|----------------|
| `/get/work/<user_name>` | User workspace |
| `/get/tools`    | 1.2-1.5 TB (for installations) |
| `/get/share`    | Shared resources |
| `/get/releases` | Release builds |

### **Partitioning Steps:**
1. **Use GParted or fdisk:**
   ```bash
   sudo fdisk /dev/sdX
   ```
2. **Create and format partitions:**
   ```bash
   sudo mkfs.ext4 /dev/sdX1  # Adjust partition accordingly
   ```
3. **Mount and add to `/etc/fstab`** for auto-mounting on boot.

---

## **4. Remote Desktop Setup**
### **Enable RDP on Ubuntu:**
1. **Install GNOME Remote Desktop:**
   ```bash
   sudo apt install gnome-remote-desktop
   ```
2. **Restart the service:**
   ```bash
   systemctl --user restart gnome-remote-desktop
   ```
3. **Ensure auto-start:**
   ```bash
   systemctl --user enable gnome-remote-desktop
   ```
4. **Verify connection from another machine.**

---

## **5. Preventing Sleep Mode**
### **Disable Auto-Sleep:**
1. **Modify systemd sleep settings:**
   ```bash
   sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
   ```
2. **Set Ubuntu to not sleep on lid close:**
   - Edit `/etc/systemd/logind.conf`
   - Set `HandleLidSwitch=ignore`
   - Restart service: `sudo systemctl restart systemd-logind`

---

## **6. Wake-on-LAN (WOL) Configuration**
### **Enable Wake-on-LAN:**
1. **Check network interface name:**
   ```bash
   ip link show
   ```
2. **Enable WOL in BIOS.**
3. **Enable WOL in Linux:**
   ```bash
   sudo ethtool -s enp7s0 wol g
   ```
4. **Make it persistent:**
   - Create a new script in `/etc/networkd-dispatcher/routable.d/`.

### **Send a Magic Packet from Another Device:**
```bash
wakeonlan <MAC_ADDRESS>
```

---

## **Next Steps**
- Further refine storage management.
- Optimize power settings.
- Install essential development tools.

---

### **Conclusion**
This guide details the entire setup process, ensuring the laptop is optimized for development and remote access. 🚀

