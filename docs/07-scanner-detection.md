# Lab 07: Automated Scanner Detection & User-Agent Analysis

## Executive Summary
В ходе лабораторной работы проведен анализ логов веб-сервера Nginx (`soc-app`) с целью выявления автоматизированных сканеров безопасности (Nikto, Nmap, Sqlmap) и кастомных скриптов (Python). На основе перехваченных заголовков HTTP отрабатаны навыки составления сигнатурных фильтров и агрегации параметров `User-Agent`.

## Detection & Threat Hunting

### 1. Multi-Scanner Signature Search
Выявление автоматических утилит с помощью регулярных выражений без учета регистра:

```bash
sudo docker logs soc-app 2>&1 | grep -Ei "nikto|nmap|sqlmap|python|go-http-client"

Результат: Зафиксированы обращения со следующими скомпрометированными заголовками User-Agent:

Mozilla/5.0 (Nikto/2.1.6)

Nmap Scripting Engine

sqlmap/1.5.11#stable

python-requests/2.28.1

2. User-Agent Profiling & Aggregation
Сбор статистики и группировка уникальных клиентских приложений:

Bash
sudo docker logs soc-app 2>&1 | awk -F'"' '{print $6}' | sort | uniq -c | sort -nr
Результат: Получена наглядная метрика, позволяющая быстро выявлять аномальные и редкие User-Agent среди основного массива легитимного трафика.

Key Takeaways
Сигнатурный анализ: Большинство коробочных сканеров безопасности по умолчанию раскрывают свою личность в заголовке User-Agent.

Быстрая триада: Однострочники на базе awk + sort + uniq позволяют проводить оперативный первичный анализ (Triage) при подозрении на DDoS или сканирование без поднятия тяжелых SIEM-систем.
