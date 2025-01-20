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
https://raw.githubusercontent.com/incid3nt/haproxy_nginx/main/nginx.conf
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`
```
python3 -m http.server 9999 --bind 0.0.0.0
```
/usr/sbin/nginx -t
проверка nginx


curl -H 'HOST: example-http.com' http://localhost