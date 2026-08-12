```markdown
# Модуль 10: Cybersecurity Detection Lab & Incident Response

## 📌 Описание модуля
В рамках данного модуля проведены практические симуляции компьютерных атак разного типа (Brute-Force, Web SQLi, Active Directory Logon Failure) и отработана методология детекции аномалий (Threat Hunting / Incident Response) через централизованные логи в **Grafana / Loki** и **Windows Event Viewer**.

---

## 🎯 Симуляция атак и Детекция (Threat Hunting)

### 1. SSH Brute-Force Attack (Linux OS Security)
* **Вектор атаки:** Симуляция несанкционированного подбора паролей к SSH-службе Ubuntu Server с внешнего хоста:
  ```powershell
  ssh hacker_test@192.168.1.207

Детекция в Grafana (LogQL):

Фрагмент кода
{job="varlogs"} |= "Failed password"
Результат аналитики SOC:
Перехвачено событие аудита безопасности PAM:
Failed password for invalid user hacker_test from 192.168.1.206 port 57265 ssh2
Идентифицирован IP-адрес атакующего (192.168.1.206), целевой логин и время атаки.

2. Web Reconnaissance & SQL Injection (Nginx Web Security)
Вектор атаки: Отправка HTTP GET-запроса с внедрением SQL-пейлоада (SQLi) в параметры запроса:

PowerShell
curl "[http://192.168.1.207/?id=1](http://192.168.1.207/?id=1)' OR '1'='1"
Детекция в Grafana (LogQL):

Фрагмент кода
{job="varlogs"} |= "id=1"
Результат аналитики SOC:
Зафиксировано обращение к веб-ресурсу, содержащее аномальные символы и конструкцию условия ' OR '1'='1. Выявлена активная фаза разведки/эксплуатации веб-уязвимостей.

3. Windows Security & Active Directory Authentication Failure
Вектор атаки: Имитация несанкционированного входа / Password Spraying под доменной учётной записью jdoe:

PowerShell
net use \\127.0.0.1 /user:homelab.local\jdoe BadPassword999!
Детекция в Windows Event Viewer (eventvwr.msc):

Канал: Windows Logs -> Security

Event ID: 4625 (An account failed to log on)

Результат аналитики SOC:
В журнале безопасности зафиксирована запись 4625 (Аудит отказа / Logon). Зафиксирован факт неудачной аутентификации пользователя jdoe на хосте DESKTOP-0MPC1RV с кодом ошибки 0xC000006A (Неверный пароль).

📊 Итоговые выводы
Развернутый стек Grafana + Loki + Promtail успешно справляется с задачами SIEM-системы начального и среднего уровня, обеспечивая оперативную идентификацию атак на Linux-инфраструктуру и веб-приложения.

Подтверждена эффективная детекция угроз на трех уровнях: ОС (Linux Auth), Приложения (Nginx/Web) и Корпоративная среда (Windows Audit/Active Directory).

Собранный практический материал формирует готовую базу для настройки автоматических правил оповещения (Grafana Alerting Rules).
