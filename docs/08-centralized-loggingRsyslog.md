# Lab 08: Centralized Log Aggregation via Rsyslog & Docker Integration

## Executive Summary
В ходе лабораторной работы произведена настройка централизованной системы сбора логов на базе службы **Rsyslog** (протокол UDP, порт 514). Проведена интеграция Docker Daemon с системным сборщиком логов для автоматического перенаправления событий из контейнеров в единый журнал безопасности `/var/log/syslog`.

## Configuration & Testing

### 1. Rsyslog Network Listener
Включение приёма внешних Syslog-сообщений по UDP/514 в `/etc/rsyslog.conf`:

```bash
sudo sed -i 's/#module(load="imudp")/module(load="imudp")/' /etc/rsyslog.conf
sudo sed -i 's/#input(type="imudp" port="514")/input(type="imudp" port="514")/' /etc/rsyslog.conf
sudo systemctl restart rsyslog

Проверка активности порта:

Bash
ss -tuln | grep 514
# Output: udp UNCONN 0 0 0.0.0.0:514
2. Manual Syslog Event Injection
Тестирование отправки алертов через утилиту logger:

Bash
logger -n 127.0.0.1 -P 514 -t SOC_ALERT "CRITICAL: Suspicious activity detected on port 8081"
3. Docker Daemon Syslog Logging Driver
Конфигурация /etc/docker/daemon.json для перенаправления вывода stdout/stderr всех контейнеров в Rsyslog с тегом docker/{{.Name}}:

JSON
{
  "log-driver": "syslog",
  "log-opts": {
    "syslog-address": "udp://127.0.0.1:514",
    "tag": "docker/{{.Name}}"
  }
}
4. Verification & Output
Проверка поступления логов доступа Nginx (soc-web) в системный журнал:

Bash
sudo tail -n 10 /var/log/syslog | grep "docker/soc-web"
Результат:
localhost docker/soc-web[3146]: 172.17.0.1 - - [31/Jul/2026:10:51:01 +0000] "GET / HTTP/1.1" 200 896 "-" "curl/8.5.0"

Key Takeaways
Централизация: Перенаправление логов в Syslog исключает необходимость заходить в каждый контейнер вручную.

Безопасность логов: В реальных SOC-средах централизованный сбор предотвращает уничтожение следов (Log Tampering) злоумышленником при компрометации отдельного контейнера.
