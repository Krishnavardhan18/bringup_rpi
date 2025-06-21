# SSH Passwordless Login & VS Code Integration

This guide explains how to set up passwordless SSH login from your Windows machine to your Linux server. This also enables **VS Code Remote - SSH** login without prompting for a password every time.

---

## ✅ Prerequisites

* Windows host with **OpenSSH** or Git Bash
* Linux server (e.g. Ubuntu) with SSH enabled
* VS Code with **Remote - SSH** extension

---

## 1️⃣ Generate SSH Key on Windows

1. Open PowerShell or Git Bash

```bash
ssh-keygen -t ed25519 -C "youremail@example.com"
```

2. When prompted:

   * Save to default path (`C:\Users\<username>\.ssh\id_ed25519`)
   * Press Enter twice (no passphrase for automation)

---

## 2️⃣ Copy Public Key to Server

### Option A: Manual

```bash
type $env:USERPROFILE\.ssh\id_ed25519.pub
```

Copy the contents and then on your Linux server:

```bash
echo '<paste here>' >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### Option B: Using `ssh-copy-id` (on Linux/macOS or WSL)

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@server_ip
```

> Note: On Windows, you may not have `ssh-copy-id` — use manual method instead.

---

## 3️⃣ Test Passwordless Login

```bash
ssh user@server_ip
```

It should **not ask for a password**.

---

## 4️⃣ Setup VS Code

1. Install **Remote - SSH** extension
2. Open Command Palette (Ctrl + Shift + P):

```text
Remote-SSH: Connect to Host...
```

3. Enter:

```text
user@server_ip
```

VS Code will connect to your server without asking for a password if the key is correctly configured.

---

## 🛠 Tips

* Ensure `sshd` is running: `sudo systemctl status ssh`
* If issues occur, delete `known_hosts` entry:

```bash
ssh-keygen -R server_ip
```

* Key permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

## ✅ Done!

You’ve now set up:

* Passwordless SSH login
* VS Code seamless access to your server

This saves time and improves automation workflows.
