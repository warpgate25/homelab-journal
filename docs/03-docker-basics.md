# Lab 03: Docker Fundamentals & Container Log Monitoring

## Executive Summary
В лабораторной работе изучены основы контейнеризации с использованием Docker. Развернут изолированный веб-сервер Nginx, настроена маршрутизация портов (Port Forwarding), смоделирован сетевой трафик и проверен механизм сбора логов через Docker Daemon (stdout/stderr) для целей ИБ и SOC-мониторинга.

## Environment & Tools
* **Target OS:** Ubuntu Server 22.04 LTS
* **Container Engine:** Docker Engine v24+
* **Container Image:** Nginx (Mainline)
* **Tools:** `docker CLI`, `curl`

## Container Architecture & Workflow



Контейнер изолирует процесс приложения на уровне ядра Linux (Namespaces & Cgroups). Все события приложения перенаправляются в стандартный вывод `stdout/stderr`, откуда Docker собирает их для передачи в централизованные системы мониторинга (SIEM).

* **Container Name:** `soc-web`
* **Host Port:** 8080
* **Container Port:** 80
* **Isolation Level:** Network & Process Isolation

## CLI Execution Steps

1. Запуск изолированного веб-сервера в фоновом режиме:
```bash
sudo docker run -d --name soc-web -p 8080:80 nginx

Проверка статуса запущенных контейнеров:

Bash
sudo docker ps
Генерация тестового сетевого трафика:

Bash
curl http://localhost:8080
Чтение логов контейнера (SOC Analytics):

Bash
sudo docker logs soc-web
Результат: Зафиксирован запрос GET / HTTP/1.1 со статусом 200 от клиента 172.17.0.1 (внутренний шлюз Docker).

Security Recommendations
Принцип наименьших привилегий: Не запускать контейнеры от имени пользователя root внутри контейнера (использовать директиву USER в Dockerfile).

Ограничение ресурсов: Задавать лимиты по CPU и RAM (--memory, --cpus), чтобы предотвратить DoS-атаки на хост.

Мониторинг логов: Настроить отправку логов Docker через драйвер syslog или fluentd в единый SIEM-сервер.
