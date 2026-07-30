# Lab 05: Network Traffic Analysis with tcpdump

## Executive Summary
В рамках лабораторной работы произведён перехват и анализ сетевого трафика локального веб-сервиса (Nginx на порту 8081) с помощью консольного анализатора `tcpdump`. Зафиксирован полный цикл TCP-соединения (3-way handshake, передача данных HTTP, разрыв соединения).

## Environment & Tooling
* **Target Interface:** Loopback / Any (`-i any`)
* **Target Port:** `8081`
* **Traffic Generator:** `curl http://localhost:8081`
* **Packet Capture Tool:** `tcpdump`

## Execution Protocol

1. Запуск перехвата пакетов:
```bash
sudo tcpdump -i any port 8081 -n -v

Генерация тестового HTTP-запроса из параллельной сессии:

Bash
curl http://localhost:8081
Captured Traffic Breakdown (SOC Analysis)
В результате захвата получено 12 сетевых пакетов. Анализ выявил следующие ключевые этапы:

TCP Three-Way Handshake:

::1.41730 > ::1.8081 [S] — Запрос на открытие сессии (SYN).

::1.8081 > ::1.41730 [S.] — Подтверждение сервера (SYN-ACK).

::1.41730 > ::1.8081 [.] — Финализация установки соединения (ACK).

HTTP Data Exchange:

::1.41730 > ::1.8081 [P.] (length 77) — Отправка клиентом HTTP GET-запроса.

::1.8081 > ::1.41730 [P.] (length 238 / 896) — Передача веб-сервером Nginx тела ответа (HTML-страницы).

Session Termination:

Flags [F.] — Корректное закрытие TCP-сессии (FIN-ACK) с обеих сторон.

Security Takeaway
Утилита tcpdump позволяет в реальном времени диагностировать сетевую активность, анализировать флаги TCP и выявлять несанкционированные подключения без необходимости запуска GUI-анализаторов (таких как Wireshark).
