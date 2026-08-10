# 🛡️ Homelab & Security Operations Center (SOC) Journal

Репозиторий предназначен для документирования практического опыта в администрировании Linux/Windows систем, развертывании сетевых сервисов в Docker и проведении анализа безопасности (SOC Analytics, Log Analysis, Threat Detection).

---

## 🚀 Learning & Lab Roadmap

| # | Topic / Technology | Focus Area | Status | Document |
| :---: | :--- | :--- | :---: | :---: |
| **01** | **Ubuntu Server + SSH** | Hardening, Auth Logs, Brute-Force Detection | ![Completed](https://img.shields.io/badge/Status-Completed-success?style=flat-square) | [`Read Report`](docs/01-ssh-log-analysis.md) |
| **02** | **Docker & Containers** | Containerization, Isolation, Container Log Inspection | ![Completed](https://img.shields.io/badge/Status-Completed-success?style=flat-square) | [`Read Report`](docs/03-docker-basics.md) |
| **03** | **Docker Compose** | Multi-container Orchestration, Network Isolation | ![Completed](https://img.shields.io/badge/Status-Completed-success?style=flat-square) | [`Read Report`](docs/04-docker-compose.md) |
| **04** | **Network Packet Analysis** | `tcpdump`, PCAP Inspection, HTTP Traffic Capture | ![Completed](https://img.shields.io/badge/Status-Completed-success?style=flat-square) | [`Read Report`](docs/05-tcpdump-analysis.md) |
| **05** | **Nginx Log Analysis** | Access Logs, Reconnaissance & SQLi Detection, One-Liners | ![Completed](https://img.shields.io/badge/Status-Completed-success?style=flat-square) | [`Read Report`](docs/06-nginx-log-analysis.md) |
| **06** | **Automated Scanner Detection** | Nmap & Nikto Fingerprinting, Bad User-Agents | ![Completed](https://img.shields.io/badge/Status-Completed-success?style=flat-square) | [`Read Report`](docs/07-scanner-detection.md) |
| **07** | **Centralized Logging** | Rsyslog / Vector, Log Aggregation & Parsing | ![Completed](https://img.shields.io/badge/Status-Completed-success?style=flat-square) | [`Read Report`](docs/08-centralized-logging Rsyslog.md) |
| **08** | **Windows Server 2022 & AD** | Domain Controller, Kerberos, Event Logs (WinEvent) | ![Completed](https://img.shields.io/badge/Status-Completed-success?style=flat-square) | [`Read Report`](docs/09-windows-ad-kerberos-logs.md) |
| **09** | **Monitoring Stack** | SIEM / Elastic Stack / Grafana / Syslog | ![Planned](https://img.shields.io/badge/Status-Planned-lightgrey?style=flat-square) | *Pending* |
| **10** | **Cybersecurity Detection Lab** | Attack Simulation & Incident Response | ![Planned](https://img.shields.io/badge/Status-Planned-lightgrey?style=flat-square) | *Pending* |

---

## 🛠️ Tech Stack & Tools
* **OS:** Ubuntu Server 24.04 LTS, Windows Server 2022
* **Web & Network:** Nginx, OpenSSH, tcpdump
* **Analysis & CLI:** Bash, `journalctl`, `grep`, `awk`, `cut`, `sort`, `uniq`, `wc`, PowerShell, Event Viewer
* **Containerization:** Docker, Docker Compose
* **Identity & Security:** Active Directory (AD DS), Kerberos, WinEvent Audit
