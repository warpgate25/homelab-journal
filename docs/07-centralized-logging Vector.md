# 03. Centralized Logging: Vector & Log Parsing (SOC Tier 1)

## 📌 Цели лабораторной работы
* Изучение архитектуры централизованного сбора логов (Log Shipper / Collector).
* Практическая установка и настройка пайплайна **Vector** на Ubuntu Server.
* Конвертация неструктурированных системных логов (`/var/log/auth.log`) в формат **JSON** в реальном времени.

---

## 🛠️ 1. Установка и запуск Vector

Установка сборщика логов произведена через официальный APT-репозиторий:
```bash
bash -c "$(curl -sL [https://setup.vector.dev](https://setup.vector.dev))"
sudo apt-get install vector -y

2. Конфигурация пайплайна (Vector Pipeline)
Создан конфигурационный файл vector.yaml, описывающий архитектуру Source ➔ Sink:

YAML
sources:
  auth_logs:
    type: "file"
    include:
      - "/var/log/auth.log"
    read_from: "beginning"

sinks:
  console_out:
    type: "console"
    inputs:
      - "auth_logs"
    encoding:
      codec: "json"
📊 3. Результаты работы (Lab Verification)
При запуске команды sudo vector --config vector.yaml поток логов формата syslog успешно преобразован в структурированный JSON:

JSON
{
  "file": "/var/log/auth.log",
  "host": "Ubuntu-Server",
  "message": "2026-08-03T14:42:50.180951+00:00 Ubuntu-Server sshd[2010]: Accepted password for kali...",
  "source_type": "file",
  "timestamp": "2026-08-03T14:46:19.596537209Z"
}
Выводы для SOC L1:
Данные приведены к единому формату (JSON), что позволяет SIEM-системам индексировать отдельные поля (host, message, timestamp).

Внедрение агентов сбора логов (Vector/Rsyslog) исключает необходимость вручную подключаться к серверам по SSH для анализа инцидентов.
