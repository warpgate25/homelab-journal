# Тема 03: Windows Server 2022, Active Directory, Kerberos & WinEvent Audit

## 📌 Цель работы
Развертывание контроллера домена Active Directory, управление доменными объектами, настройка аудита безопасности Windows (Event Viewer) и анализ работы протокола Kerberos для задач SOC Tier 1.

---

## 🛠️ Ход работы и конфигурация

### 1. Развертывание Active Directory Domain Services (AD DS)
В среде Windows Server 2022 был развернут доменный лес `homelab.local`.

```powershell
# Установка роли AD DS и инструментов управления
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# Создание нового леса Active Directory
Install-ADDSForest -DomainName "homelab.local" -InstallDns -Force

2. Управление доменными объектами (Пользователи и Группы)
Для имитации рабочей среды и проведения атак создана учетная запись пользователя и доменная группа:

PowerShell
# Создание доменного пользователя jdoe
New-ADUser -Name "John Doe" -GivenName "John" -Surname "Doe" -SamAccountName "jdoe" -UserPrincipalName "jdoe@homelab.local" -AccountPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force) -Enabled $true

# Создание группы и добавление пользователя
New-ADGroup -Name "IT-Support" -GroupScope Global
Add-ADGroupMember -Identity "IT-Support" -Members "jdoe"
🛡️ Аудит событий безопасности (WinEvent Logs)
Настройка политики аудита
Включен аудит событий входа в систему для фиксации легитимных и вредоносных попыток аутентификации:

PowerShell
auditpol /set /subcategory:"Logon" /success:enable /failure:enable
Генерация и анализ событий (Event ID)
Был сгенерирован тестовый неудачный вход для отлова события подбора пароля:

PowerShell
net use \\127.0.0.1 /user:jdoe WrongPass123!
Ключевые Event ID для SOC-аналитика:

Event ID 4624: Успешный вход в систему (Successful Logon).

Event ID 4625: Ошибка аутентификации (Failed Logon — неправильный пароль / брутфорс).

Фильтрация событий через PowerShell:

PowerShell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624,4625} -MaxEvents 20
🔑 Протокол Kerberos & Service Principal Name (SPN)
Для изучения векторов атак (в частности, Kerberoasting) к учетной записи сервиса был привязан SPN и проанализирована структура билетов Kerberos.

PowerShell
# Регистрация SPN для учетной записи jdoe
setspn -A MSSQLSvc/db.homelab.local:1433 jdoe

# Проверка привязанных SPN
setspn -L jdoe

# Дамп билетов Kerberos из памяти текущей сессии
klist
Результат проверки klist:
Client: Administrator@HOMELAB.LOCAL

Server: krbtgt/HOMELAB.LOCAL

Encryption Type: AES-256-CTS-HMAC-SHA1-96

📊 Выводы и SOC-рекомендации
Мониторинг 4625: Всплеск событий Event ID 4625 от одного источника указывает на атаку типа Brute-force или Password Spraying.

Защита SPN: Аккаунты с привязанными SPN (Kerberoasting) должны использовать сложные пароли (25+ символов) или управляемые учетные записи служб (gMSA) для предотвращения оффлайн-расшифровки TGS-билетов.
