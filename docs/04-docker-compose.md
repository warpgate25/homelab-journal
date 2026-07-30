# Lab 04: Multi-Container Deployment & Network Isolation with Docker Compose

## Executive Summary
В рамках лабораторной работы развёрнута двухуровневая сервисная архитектура (Web Front-End + Database Back-End) с использованием Docker Compose. Реализована сетевая изоляция с ограничением прямого доступа к базе данных из внешней сети для соответствия стандартам безопасности ИБ/SOC.

## Environment & Components
* **Orchestrator:** Docker Compose v2+
* **Web Application:** `nginx:alpine` (Container: `soc-app`)
* **Database:** `postgres:15-alpine` (Container: `soc-db`)
* **Network:** Custom Isolated Bridge Network (`compose-lab_soc-network`)

## Network Architecture & Security Control
1. **Public Endpoint:** Веб-сервер `soc-app` доступен из внешней сети по порту `8081` (`0.0.0.0:8081->80/tcp`).
2. **Backend Protection:** Порт СУБД `5432` открыт **исключительно** внутри виртуальной сети `soc-network`. Прямой доступ к базе данных с внешних интерфейсов заблокирован.
3. **Inter-Service Communication:** Сервисы взаимодействуют друг с другом по внутренним DNS-именам Docker (`web-app`, `db`).

---

## Configuration (`docker-compose.yml`)

```yaml
version: '3.8'

services:
  web-app:
    image: nginx:alpine
    container_name: soc-app
    ports:
      - "8081:80"
    networks:
      - soc-network

  db:
    image: postgres:15-alpine
    container_name: soc-db
    environment:
      POSTGRES_USER: soc_user
      POSTGRES_PASSWORD: SecurePassword123!
      POSTGRES_DB: soc_logs
    networks:
      - soc-network

networks:
  soc-network:
    driver: bridge

Execution Protocol & Verification
Развёртывание стека сервисов:

Bash
docker compose up -d
Проверка статуса процессов:

Bash
sudo docker compose ps
Результат: Оба сервиса (soc-app, soc-db) находятся в статусе Up.

Валидация виртуальных сетей:

Bash
sudo docker network ls
Результат: Зафиксировано создание bridge-сети compose-lab_soc-network.

Тестирование веб-интерфейса:

Bash
curl http://localhost:8081
Результат: Успешно получен ответ HTTP 200 OK (Welcome to nginx!).
