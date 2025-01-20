# Домашнее задание к занятию "Кластеризация и балансировка нагрузки" - Еноктаев Олег



### Задание 1

`Приведите ответ в свободной форме........`

- Запустите два simple python сервера на своей виртуальной машине на разных портах
- Установите и настройте HAProxy, воспользуйтесь материалами к лекции по ссылке
- Настройте балансировку Round-robin на 4 уровне.
- На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy.

1. Запустим два simple python сервера:
```
oleg@nginx:~/http/server1$ cat index.html
Server 1 : 8888
oleg@nginx:~/http/server1$ python3 -m http.server 8888 --bind 0.0.0.0
Serving HTTP on 0.0.0.0 port 8888 (http://0.0.0.0:8888/) ...
```
```
oleg@nginx:~/http/server1$ cat index.html
Server 2 : 9999
oleg@nginx:~/http/server1$ python3 -m http.server 9999 --bind 0.0.0.0
Serving HTTP on 0.0.0.0 port 9999 (http://0.0.0.0:9999/) ...
```
---

### Задание 2

- Запустите три simple python сервера на своей виртуальной машине на разных портах
- Настройте балансировку Weighted Round Robin на 7 уровне, чтобы первый сервер имел вес 2, второй - 3, а третий - 4
- HAproxy должен балансировать только тот http-трафик, который адресован домену example.local
- На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy c использованием домена example.local и без него.

2. Запустим три python сервера:

```

oleg@nginx:~/http/server3$ python3 -m http.server 7777 --bind 0.0.0.0
Serving HTTP on 0.0.0.0 port 7777 (http://0.0.0.0:7777/) ...
oleg@nginx:~/http/server1$ cat index.html
Server 1 : 8888
oleg@nginx:~/http/server1$ python3 -m http.server 8888 --bind 0.0.0.0
Serving HTTP on 0.0.0.0 port 8888 (http://0.0.0.0:8888/) ...
oleg@nginx:~/http/server1$ cat index.html
Server 2 : 9999
oleg@nginx:~/http/server1$ python3 -m http.server 9999 --bind 0.0.0.0
Serving HTTP on 0.0.0.0 port 9999 (http://0.0.0.0:9999/) ...
```
Проверим работоспобоность:
```
oleg@nginx:~$ curl http://localhost:9999
Server 2 : 9999
oleg@nginx:~$ curl http://localhost:7777
server 3 : 7777
oleg@nginx:~$ curl http://localhost:8888
Server 1 : 8888
```
посмотрим конфиг.файл nginx:
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
error_log /var/log/nginx/error.log;
include /etc/nginx/modules-enabled/*.conf;

events {
        worker_connections 768;
        # multi_accept on;
}

http {

        ##
        # Basic Settings
        ##

        sendfile on;
        tcp_nopush on;
        types_hash_max_size 2048;
        # server_tokens off;

        # server_names_hash_bucket_size 64;
        # server_name_in_redirect off;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        ##
        # SSL Settings
        ##

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;

        ##
        # Logging Settings
        ##

        access_log /var/log/nginx/access.log;

        ##
        # Gzip Settings
        ##

        gzip on;

        # gzip_vary on;
        # gzip_proxied any;
        # gzip_comp_level 6;
        # gzip_buffers 16 8k;
        # gzip_http_version 1.1;
        # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

        ##
        # Virtual Host Configs
        ##

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
}


#mail {
#       # See sample authentication script at:
#       # http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#
#       # auth_http localhost/auth.php;
#       # pop3_capabilities "TOP" "USER";
#       # imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#       server {
#               listen     localhost:110;
#               protocol   pop3;
#               proxy      on;
#       }
#
#       server {
#               listen     localhost:143;
#               protocol   imap;
#               proxy      on;
#       }
#}
```

```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`
```
python3 -m http.server 9999 --bind 0.0.0.0
```
/usr/sbin/nginx -t
проверка nginx


curl -H 'HOST: example-http.com' http://localhost