# 🚀 Day 1 — Installing Docker on Ubuntu Server

## 📌 Objective

Prepare Ubuntu Server for future home lab projects by installing Docker, verifying its operation, and deploying the first web service in a container.

---

# 🛠 Environment

- OS: Ubuntu Server
- Virtualization: VirtualBox
- Network: Bridged Adapter
- Access: SSH

---

# 📥 Installing Docker

Downloaded the official installation script:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
```

Started the installation:

```bash
sudo sh get-docker.sh
```

After installation, verified Docker:

```bash
docker version
```

Docker Engine was installed successfully.

---

# ⚠️ Issues encountered

## Issue 1 — Commands entered incorrectly

Initially, two commands were entered on the same line.

Instead of:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

they were accidentally combined.

As a result, `curl` interpreted `sudo` and `sh` as hostnames and returned:

```
Could not resolve host: sudo
Could not resolve host: sh
```

### Solution

Executed each command separately.

Installation completed successfully.

---

## Issue 2 — Permission denied

After installation I tried:

```bash
docker ps
```

Received:

```
permission denied while trying to connect to the Docker daemon socket
```

### Diagnostics

Checked user groups:

```bash
groups
```

Checked Docker socket permissions:

```bash
ls -l /var/run/docker.sock
```

Verified Docker group:

```bash
getent group docker
```

Checked Docker service:

```bash
sudo systemctl status docker
```

---

## Issue 3 — Typo

While adding the user to the Docker group I accidentally typed:

```bash
sudo usermode -aG docker kali
```

instead of

```bash
sudo usermod -aG docker kali
```

The shell returned:

```
command not found
```

After checking:

```bash
which usermod
```

I found the typo and corrected it.

---

## Fix

Added the user to the Docker group:

```bash
sudo usermod -aG docker kali
```

Rebooted the server.

Verified the groups:

```bash
id
```

The output confirmed membership in the `docker` group.

---

# ✅ Testing Docker

Started the official test container:

```bash
docker run hello-world
```

Docker automatically:

- downloaded the image;
- created the container;
- started it;
- displayed the test message.

Everything worked correctly.

---

# 🌐 Deploying Nginx

Started the official Nginx container:

```bash
docker run -d --name nginx-server -p 80:80 nginx
```

Verified running containers:

```bash
docker ps
```

Opened the browser:

```
http://192.168.1.181
```

Successfully received the default **Welcome to nginx!** page.

---

# 📚 What I learned

- Installing Docker Engine
- Docker Hub
- Docker Images
- Docker Containers
- Running containers
- Publishing ports
- Running containers in background
- Managing Docker service
- Linux user groups
- Docker permissions
- Basic troubleshooting

---

# 📁 Commands used

```bash
docker version
docker images
docker ps
docker ps -a
docker run hello-world
docker run -d --name nginx-server -p 80:80 nginx
groups
id
systemctl status docker
```

---

# 🎯 Result

Today I successfully:

- Installed Docker Engine.
- Fixed permission issues.
- Learned the difference between images and containers.
- Deployed my first Nginx web server in Docker.
- Verified network connectivity from another machine.

  ---

# 🧠 Architecture Overview

Understanding the environment hierarchy is important before working with Docker.

```text
My PC
└── VirtualBox
    └── Ubuntu Server
        └── Docker Engine
            └── Nginx Container
                ├── File System
                ├── Processes
                └── Web Server
```

## Components

**My PC**
- Physical Windows computer.

**VirtualBox**
- Virtualization platform that runs the Ubuntu Server virtual machine.

**Ubuntu Server**
- Host operating system where Docker is installed.

**Docker Engine**
- Container runtime responsible for creating and managing containers.

**Nginx Container**
- An isolated Linux environment running the Nginx web server.
- Has its own filesystem.
- Runs its own processes.
- Shares the host's Linux kernel.

---

### Key Idea

A Docker container is **not a virtual machine**.

It does not include its own operating system kernel.
Instead, it shares the kernel of the host operating system while keeping applications isolated.

This makes containers lightweight, fast, and ideal for modern infrastructure.

This environment will become the foundation of my home lab for Linux administration, networking and cybersecurity.
