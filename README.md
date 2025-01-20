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
Проверка конфигурационных файлов 
```
root@nginx:/home/oleg# sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

```



`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`
```
python3 -m http.server 9999 --bind 0.0.0.0
```
/usr/sbin/nginx -t
проверка nginx


curl -H 'HOST: example-http.com' http://localhost




Round roby балансировка на 7 уровне osi
создадим конфиг.файл со следующим содержанием:
nano /etc/nginx/conf.d/example-http.conf
```
include /etc/nginx/include/upstream.inc;

server {
   listen	80;
   

   server_name	example-http.com;
   

   access_log	/var/log/nginx/example-http.com-acess.log;
   error_log	/var/log/nginx/example-http.com-error.log;

   location / {
		proxy_pass	http://example_app;

   }

}
```
создадим файл 
nano /etc/nginx/include/upstream.inc
```
upstream example_app {

	     server 127.0.0.1:8888;
        server 127.0.0.1:9999;
        server 127.0.0.1:7777;

}
```
перезапустим nginx
```
systemctl reload nginx
```
выполним запросы к нашим серверам:
```
root@nginx:/home/oleg# curl -H 'HOST: example-http.com' http://localhost
Server 1 : 8888
root@nginx:/home/oleg# curl -H 'HOST: example-http.com' http://localhost
Server 2 : 9999
root@nginx:/home/oleg# curl -H 'HOST: example-http.com' http://localhost
server 3 : 7777
root@nginx:/home/oleg# curl -H 'HOST: example-http.com' http://localhost
Server 1 : 8888
root@nginx:/home/oleg# curl -H 'HOST: example-http.com' http://localhost
Server 2 : 9999
root@nginx:/home/oleg# curl -H 'HOST: example-http.com' http://localhost
server 3 : 7777
```


поменяем вес:
nano /etc/nginx/include/upstream.inc

```
upstream example_app {

	     server 127.0.0.1:8888 weight=3;
        server 127.0.0.1:9999;
        #server 127.0.0.1:7777;
```

перезапустим сервер:
```
systemctl reload nginx
root@nginx:/home/oleg# curl -H 'HOST: example-http.com' http://localhost
Server 2 : 9999
root@nginx:/home/oleg# curl -H 'HOST: example-http.com' http://localhost
Server 1 : 8888
root@nginx:/home/oleg# curl -H 'HOST: example-http.com' http://localhost
Server 1 : 8888
root@nginx:/home/oleg# curl -H 'HOST: example-http.com' http://localhost
Server 1 : 8888
root@nginx:/home/oleg# curl -H 'HOST: example-http.com' http://localhost
Server 2 : 9999
```
как мы видим три запроса на Server1 и четвертый на Server 2

nginx так же умеет производить балансировку на 4 уровне модели
для этого необходимо произвести настройку главного конфиг.файла nginx

```
nano /etc/nginx/nginx.conf
```
