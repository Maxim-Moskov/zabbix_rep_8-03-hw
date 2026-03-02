# Домашнее задания "Система мониторинга Zabbix" — Моськов Максим

## Задание 1: Установите Zabbix Server с веб-интерфейсом.

### Описание решения:

# Обновление системы и установка базы данных
sudo apt update && sudo apt upgrade -y
sudo apt install -y postgresql postgresql-contrib

# Добавление официального репозитория Zabbix 7.0 LTS
wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_7.0-2+debian12_all.deb
sudo dpkg -i zabbix-release_7.0-2+debian12_all.deb
sudo apt update

# Установка сервера, веб-интерфейса (Apache) и агента
sudo apt install -y zabbix-server-pgsql zabbix-frontend-php php8.2-pgsql zabbix-apache-conf zabbix-sql-scripts zabbix-agent

# Создание пользователя БД (система запросит задать пароль)
sudo -u postgres createuser --pwprompt zabbix

# Создание базы данных zabbix
sudo -u postgres createdb -O zabbix zabbix

# Переход во временную папку (чтобы избежать ошибок прав доступа) и импорт начальной структуры БД
cd /tmp
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix

# Редактирование файла конфигурации для указания пароля от БД
sudo nano /etc/zabbix/zabbix_server.conf
# В файле нужно раскомментировать строку DBPassword= и указать свой пароль

# Перезапуск и добавление служб в автозагрузку
sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2

![Скриншот авторизации Zabbix](img/zabbix-login.png)