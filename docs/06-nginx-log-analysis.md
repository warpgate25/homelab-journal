# Lab 06: Nginx Log Analysis & Threat Hunting

## Executive Summary
В ходе лабораторной работы проведён анализ журналов веб-сервера Nginx (`soc-app`), развернутого в Docker-контейнере. На основе сгенерированного трафика отработаны навыки фильтрации и выявления сетевой разведки (Reconnaissance) и атак класса SQL Injection с помощью утилит `grep` и `wc`.

## Threat Hunting Methodology

### 1. Detection of Reconnaissance & Sensitive File Probing
Поиск попыток доступа к панелям администратора, конфигурационным файлам (`.env`) и CMS (`wp-config`):

```bash
sudo docker logs soc-app 2>&1 | grep -E "admin|\.env|wp-config"

Результат: Зафиксированы систематические обращения с кодом ответа 404 Not Found с IP-адреса 172.18.0.1.

2. Detection of Web Attacks (SQL Injection)
Фильтрация логов на наличие характерных сигнатур SQL-инъекций (OR 1=1 / OR%201=1):

Bash
sudo docker logs soc-app 2>&1 | grep -E "OR%201=1"
Результат: Выявлены вредоносные HTTP GET-запросы к эндпоинту /product?id=1' OR 1=1.

3. Error Rate & Anomaly Metrics
Подсчёт общего количества ошибок 404 Not Found для оценки масштаба сканирования:

Bash
sudo docker logs soc-app 2>&1 | grep " 404 " | wc -l
Результат: Зафиксировано 12 событий 404 Not Found.

Key Findings
Использование User-Agent curl/8.5.0 при обращении к служебным файлам указывает на автоматизированное сканирование.

Встроенный механизм сбора логов Docker (docker logs) в сочетании с каналом перенаправления ошибок (2>&1) позволяет проводить эффективный поиск угроз без извлечения файлов на хост-систему.
