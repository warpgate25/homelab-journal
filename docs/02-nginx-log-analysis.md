# Lab 02: Nginx Web Log Analysis & Attack Detection

## Executive Summary
В лабораторной работе проведен анализ журналов доступа (`access.log`) веб-сервера Nginx. Смоделированы атаки типа сканирования скрытых директорий и Path Traversal (Local File Inclusion). Написаны Bash-скрипты для парсинга веб-логов и выявления индикаторов компрометации (IOCs).

## Environment & Tools
* **Target OS:** Ubuntu Server 22.04 LTS
* **Service:** Nginx Web Server
* **Tools:** `cat`, `grep`, `awk`, `sort`, `uniq`

## Log Anatomy

Пример строки атаки Path Traversal (Nginx Combined Log Format):
`192.168.1.201 - - [28/Jul/2026:09:48:41 +0000] "GET /index.php?file=../../../../etc/passwd HTTP/1.1" 404 162 "-" "curl/8.13.0"`

* **Client IP:** 192.168.1.201
* **Timestamp:** [28/Jul/2026:09:48:41 +0000]
* **HTTP Method:** GET
* **Request URI:** /index.php?file=../../../../etc/passwd
* **HTTP Status Code:** 404
* **User-Agent:** curl/8.13.0

## CLI Analysis (Bash One-Liners)

1. Поиск обращений к несуществующим или скрытым путям (поиск сканирования):
```bash
sudo cat /var/log/nginx/access.log | grep " 404 " | awk '{print $7}'

Результат: /admin, /config.php, /.env

1. Подсчет статистики HTTP-статусов (Агрегация):
sudo cat /var/log/nginx/access.log | awk '{print $9}' | sort | uniq -c | sort -nr

Результат: Выявлено аномальное количество ответов 404 Not Found.

1. Поиск сигнатур веб-атак (Path Traversal, SQLi):
sudo cat /var/log/nginx/access.log | grep -E "etc/passwd|SELECT|UNION|OR%20"

Результат: Зафиксирована попытка чтения локального системного файла /etc/passwd.

Recommendations
1. Внедрить WAF (Web Application Firewall), например ModSecurity, для блокировки вредоносных паттернов (SQLi, LFI, XSS).

2. Настроить Fail2ban для мониторинга логов Nginx и автоматической блокировки IP-адресов, генерирующих большое количество 404 ошибок (защита от сканеров).

3. Ограничить доступ к файлам конфигурации (.env, .git) на уровне конфигов Nginx (deny all;).
