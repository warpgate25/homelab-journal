# Модуль 09: Monitoring Stack (SIEM / Elastic Stack / Grafana / Syslog)

## 📌 Описание модуля
В данном модуле развернута централизованная система сбора, хранения и визуализации системных логов и событий безопасности (Log Aggregation & Monitoring Stack) на базе **Grafana**, **Loki** и **Promtail**. 

Стек выступает в качестве легкой альтернативы классическому Elastic Stack (ELK) / SIEM, обеспечивая высокую скорость фильтрации логов через язык запросов **LogQL** при минимальном потреблении ресурсов серверной инфраструктуры.

---

## 🏗️ Архитектура стека

Стек развернут в изолированной контейнерной среде Docker на **Ubuntu Server (192.168.1.207)**:

1. **Promtail (Log Collector / Agent):**
   * Фоновый агент сбора логов.
   * Осуществляет мониторинг локальных файлов журналов Linux (`/var/log/**/*.log`), включая системные события `auth.log`, `syslog` и логи веб-сервисов.
   * Пересылает потоки данных в базу Loki по HTTP API.

2. **Loki (Log Aggregation DB):**
   * Высокопроизводительная СУБД для хранения и индексации логов от Grafana Labs.
   * Индексирует метки (labels), а не полный текст, сохраняя минимальный след по памяти и дисковому пространству.

3. **Grafana (SIEM Dashboard & Search UI):**
   * Единый веб-интерфейс аналитика SOC (`http://192.168.1.207:3000`).
   * Интегрирован с Loki в качестве Data Source.
   * Позволяет выполнять оперативный Threat Hunting через панель **Explore** с помощью языка **LogQL**.

---

## 🛠️ Файлы конфигурации

### 1. `promtail-config.yml`
```yaml
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
  - job_name: system
    static_configs:
      - targets:
          - localhost
        labels:
          job: varlogs
          __path__: /var/log/**/*.log

docker-compose.yml
YAML
version: '3.8'

services:
  loki:
    image: grafana/loki:2.9.0
    container_name: soc-loki
    ports:
      - "3100:3100"
    command: -config.file=/etc/loki/local-config.yaml
    restart: unless-stopped

  promtail:
    image: grafana/promtail:2.9.0
    container_name: soc-promtail
    volumes:
      - /var/log:/var/log:ro
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - ./promtail-config.yml:/etc/promtail/config.yml:ro
    command: -config.file=/etc/promtail/config.yml
    restart: unless-stopped

  grafana:
    image: grafana/grafana:latest
    container_name: soc-grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    restart: unless-stopped
🚀 Запуск и проверка статуса
Запуск контейнеров из директории ~/homelab-monitoring:

Bash
docker compose up -d
Проверка статуса сервисов:

Bash
docker ps
Результат:

soc-grafana — Up (Port 3000)

soc-loki — Up (Port 3100)

soc-promtail — Up

🔍 Проверка сбора логов (LogQL)
В интерфейсе Grafana Explore подключен Data Source http://loki:3100. Выполнен запрос для вывода общего потока логов:

Фрагмент кода
{job="varlogs"}
Стек подтвердил корректную работу и поступление записей из системы аудита в реальном времени.
