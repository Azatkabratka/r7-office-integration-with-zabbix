🔧 1. Установка Zabbix Agent на сервере
1.1 Подключение к хосту
bash
Копировать
Редактировать
ssh youruser@your-hostname-or-ip
sudo -i

1.2 Установка Zabbix Agent 2
bash
Копировать
Редактировать
wget https://repo.zabbix.com/zabbix/7.0/debian/pool/main/z/zabbix-release/zabbix-release_7.0-2+debian10_all.deb
dpkg -i zabbix-release_7.0-2+debian10_all.deb
apt update -y
apt install -y zabbix-agent2 -t buster

1.3 Настройка агента
Открой файл /etc/zabbix/zabbix_agent2.conf и отредактируй следующие параметры:

ini
Копировать
Редактировать
Server=your-zabbix-server-hostname-or-ip
ServerActive=your-zabbix-server-hostname-or-ip
ListenPort=10050
⚠️ Замените your-zabbix-server-hostname-or-ip на актуальное имя хоста или IP вашего Zabbix-сервера.

1.4 Запуск агента
bash
Копировать
Редактировать
systemctl enable zabbix-agent2 --now
systemctl status zabbix-agent2

📥 2. Импорт шаблонов R7
2.1 Скачать шаблоны
Загрузите YAML-шаблоны R7 с официального сайта:

📎 Скачать шаблоны R7 Office - https://nct.r7-office.ru/doc.html?uid=6f09a860-ab44-4094-82e4-5661da39e6ca_1177983

Обычно файл называется CS_2024_templates.yml.

2.2 Импорт в Zabbix
Откройте веб-интерфейс Zabbix:
http://your-zabbix-server-hostname:8080

Перейдите:
Configuration → Templates → Import

Нажмите Выбрать файл, выберите загруженный .yml.

Отметьте «Расширенные опции» → Импорт.

🧩 3. Настройка шаблона CS 2024
В интерфейсе Zabbix:
Configuration → Templates → CS 2024

Перейдите на вкладку Macros → Inherited and host macros.

Установите следующие макросы:

Macro	Value
{$CALENDAR}	r7calendar
{$CDADMIN}	r7admin
{$CDDISK}	r7disk
{$CDMAIL}	r7mail
{$CONTACTS}	r7contacts
{$DOMAIN}	yourdomain.local
{$DRAW}	draw
{$DS}	ds
{$PROJECTS}	r7projects
{$R7_PASSWORD}	youradminpass
{$R7_USERNAME}	youradminuser
{$SHEMA}	https
⚠️ Замените значения youradminuser, youradminpass, yourdomain.local на свои.


➕ 4. Добавление узла сети
В интерфейсе Zabbix:
Monitoring → Hosts → Create host

Заполните поля:

Поле	Значение
Имя узла	Community Server R7
Группы узлов	Applications, Linux servers
Интерфейс агента	DNS: your-agent-hostname
Подключение	По DNS
Добавьте шаблон:

Группа шаблонов: r7-office

Имя шаблона: CS 2024

Нажмите Добавить

✅ Готово!
Через некоторое время агент должен начать передавать данные, а иконка узла в списке станет зелёной. На дашборде Zabbix может отобразиться предупреждение о графике (если не установлен модуль «Графика» в R7 — это нормально).

🛡 Подсказки
Убедитесь, что порт 10050 открыт на сервере с агентом.

Если используете SELinux/AppArmor — проверьте политики.

Можно использовать zabbix_get для ручной проверки связи с агентом.

