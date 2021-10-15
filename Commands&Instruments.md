Linux
Сети
``
`netstat -nutlp` - какие порты прослушиваются
'nmtui' - настройка сетевых интерфейсов
``
Файловая система
`mc` - тотал командер для линукса в командной строке
Disk space info
```
df -h
cd /
du -sch * --exclude=home
```


```
```

Создание сертификатов (самоподписанных):
`openssl req -new -x509 -keyout scoruba.asbc.lo.key -out scoruba.asbc.lo.crt -days 365 -nodes`

Postgres 12
Установка на сервер приложений:
https://huzefapatel.com/blogs/installing-postgresql-12-on-oracle-linux-7/
https://techviewleo.com/install-postgresql-on-oracle-linux/
1. `sudo psql --u  postgres IdentityServer4Admin` - форма  для подключения к базе данных psql --u {userName} {DBName}
1. `\dt \dn` - таблицы и схемы в базеданных
1. `select * from public."ClientPostLogoutRedirectUris";`
1. ` UPDATE  public."ClientPostLogoutRedirectUris" SET "PostLogoutRedirectUri"='https://inspector.asbc.lo/index.html' where "ClientId"=3;`

Установка пароля при первом пользовании:
1. `sudo su postgres -c psql template` - логинемся в postgres
2. `ALTER USER postgres with PASSWORD 'password';` - Меняем пароль


Открыть доступ к postgresql серверу:
1. `sudo -u postgres psql -c 'SHOW config_file'` - узнать информацию, где лежат конфиг файлы
```bash
could not change directory to "/root": Permission denied
             config_file
-------------------------------------
 /var/lib/pgsql/data/postgresql.conf
(1 row)
```
2. `cd /var/lib/pgsql/data/` - проходим в директорию
3. Кореектируем postgresql.conf
До
```bash
#listen_addresses = '127.0.0.1'		# what IP address(es) to listen on;
```
После
```bash
listen_addresses = '*'		# what IP address(es) to listen on;
```
4. ` systemctl restart postgresql` - Перезагружаем postgresql
5. `netstat -nutlp` - Проверяем, что мы прослушиваем все Ip
```bash
tcp        0      0 0.0.0.0:5432            0.0.0.0:*               LISTEN      96123/postmaster
tcp6       0      0 :::5432                 :::*                    LISTEN      96123/postmaster
```
6. Кореектируем pg_hba.conf, чтобы получилисб такие строки:
```bash
# IPv4 local connections:
host    all             all             0.0.0.0/0               md5
# IPv6 local connections:
host    all             all             ::0/0                   md5
```
7. ` systemctl restart postgresql` - Перезагружаем postgresql

Nginx
1. `systemctl restart nginx` - перезагружаем
2. `systemctl status nginx` - проверяем
3. `nginx -t` - тестируем концигурацию на синтаксические ошибки
Программы для работы с сервером удаленно:
PuTTY
WINSC
MobaXTerm

Remote Development - расширение для VS code

https://habr.com/ru/post/332920/ - Разворачиваем и демонизируем ASP.NET Core приложение под Linux в виде фонового сервиса
https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units - How To Use Systemctl to Manage Systemd Services and Units
