# 🚀 Day 1 — Ubuntu Server Installation & SSH Configuration

## 🎯 Objective

Prepare a virtual Ubuntu Server environment for future home lab projects by installing the operating system, configuring network access, and enabling SSH for remote administration.

---

## 🛠 Environment

- **OS:** Ubuntu Server 24.04 LTS
- **Virtualization:** Oracle VirtualBox
- **Host OS:** Windows 11
- **Network Mode:** Bridged Adapter
- **Remote Access:** OpenSSH Server

---

## 📥 Installing Ubuntu Server

Created a new virtual machine in Oracle VirtualBox and installed Ubuntu Server.

During installation:

- Created user account
- Configured hostname
- Enabled OpenSSH Server
- Configured network using DHCP
- Completed initial system installation

---

## 🌐 Network Configuration

Verified network connectivity.

Checked IP address:

```bash
ip a
```

Verified Internet access:

```bash
ping google.com
```

Confirmed that the virtual machine received an IP address from the local network.

---

## 🔑 SSH Configuration

Verified that the SSH service was running.

Checked service status:

```bash
sudo systemctl status ssh
```

Connected remotely from Windows using:

```bash
ssh kali@192.168.1.181
```

Successfully established a remote SSH session.

---

## ⚠️ Problems Encountered

### SSH connection failed

Initially SSH connection could not be established.

Possible causes investigated:

- SSH service status
- Firewall configuration
- Network adapter mode
- Incorrect IP address

---

## 🔧 Troubleshooting

Performed diagnostics using:

```bash
ip a
```

```bash
systemctl status ssh
```

```bash
ping
```

Verified that:

- SSH service was active
- Correct IP address was assigned
- Network bridge was functioning properly

After correcting the configuration, SSH access worked successfully.

---

## ✅ Result

Successfully prepared the first server for the home lab.

Completed:

- Ubuntu Server installation
- Network configuration
- SSH remote access
- Initial Linux administration

The virtual machine is now ready for future infrastructure projects.

---

## 📚 What I Learned

- Installing Ubuntu Server
- Basic Linux administration
- Network configuration
- SSH remote management
- Using systemctl
- Verifying network connectivity

---

## 📋 Commands Used

```bash
ip a

ping google.com

sudo systemctl status ssh

ssh kali@192.168.1.181
```

---

## 🎯 Next Step

Install Docker Engine and deploy the first containerized web service using Nginx.
