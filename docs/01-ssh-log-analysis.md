# Lab 01: SSH Log Analysis & Brute-Force Detection

## Executive Summary
В лабораторной работе проведен анализ логов аутентификации OpenSSH (`sshd`) на Ubuntu Server. Смоделированы попытки несанкционированного доступа (Brute-force / User Enumeration), разобрана структура логов и написаны Bash-скрипты для выявления индикаторов компрометации (IOC).

## Environment & Tools
* **Target OS:** Ubuntu Server 22.04 LTS
* **Service:** OpenSSH Daemon (`sshd`)
* **Tools:** `journalctl`, `grep`, `awk`, `sort`, `uniq`

## Log Anatomy

Пример строки неудачного входа:
`Jul 28 06:30:34 Ubuntu-Server sshd[1231]: Failed password for invalid user tali from 192.168.1.201 port 61958 ssh2`

* **Timestamp:** Jul 28 06:30:34
* **Hostname:** Ubuntu-Server
* **Process / PID:** sshd[1231]
* **Event Status:** Failed password
* **User State:** invalid user tali
* **Source IP:** 192.168.1.201
* **Source Port:** 61958

## CLI Analysis (Bash One-Liners)

1. Извлечение всех IP-адресов источников атак:
```bash
sudo journalctl -u ssh --no-pager | grep "Failed password" | awk '{for(i=1;i<=NF;i++) if($i=="from") print $(i+1)}'

1. Подсчет количества попыток входа по IP (Top Attackers):
sudo journalctl -u ssh --no-pager | grep "Failed password" | awk '{for(i=1;i<=NF;i++) if($i=="from") print $(i+1)}' | sort | uniq -c | sort -nr
Результат:
192.168.1.201 — 7 попыток (брутфорс)

192.168.1.84 — 3 попытки

Список целевых учетных записей:
sudo journalctl -u ssh --no-pager | grep "Failed password" | grep -oP 'for (invalid user )?\K\S+' | sort -u
Результат: kali, tali.
Recommendations
Отключить вход по паролю (PasswordAuthentication no в /etc/ssh/sshd_config).

Настроить вход строго по SSH-ключам.

Установить Fail2ban для автоматической блокировки IP при превышении 5 ошибок.

Сменить стандартный порт SSH (22).
