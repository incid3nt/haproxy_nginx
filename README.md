# Домашнее задание к занятию "Кластеризация и балансировка нагрузки" - Еноктаев Олег



### Задание 1



- Запустите два simple python сервера на своей виртуальной машине на разных портах
- Установите и настройте HAProxy, воспользуйтесь материалами к лекции по ссылке
- Настройте балансировку Round-robin на 4 уровне.
- На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy.

### Задание 2

- Запустите три simple python сервера на своей виртуальной машине на разных портах
- Настройте балансировку Weighted Round Robin на 7 уровне, чтобы первый сервер имел вес 2, второй - 3, а третий - 4
- HAproxy должен балансировать только тот http-трафик, который адресован домену example.local
- На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy c использованием домена example.local и без него.

### Задание 1
- Запустите два simple python сервера на своей виртуальной машине на разных портах:

Создадим папку http и еще внутри 2 папки для каждого сервера:

```
mkdir http && mkdir http/server1 http/server2
```
```
echo "Server 1 : 8888" > http/server1/index.html
echo "Server 2 : 9999" > http/server2/index.html
```
```
cat http/server1/index.html
Server 1 : 8888
cat http/server2/index.html
Server 2 : 9999
```

Запустим pthon сервер
```
python3 -m http.server 8888 --bind 0.0.0.0
python3 -m http.server 9999 --bind 0.0.0.0
```
проверяем:
```

oleg@DESKTOP-6TMQOI1:~/http/server1$ curl http://localhost:8888
Server 1 : 8888
oleg@DESKTOP-6TMQOI1:~/http/server1$ curl http://localhost:9999
Server2 : 9999
```
установим haproxy
```
sudo apt install haproxy
```
установили:
```
oleg@DESKTOP-6TMQOI1:~/http/server1$ systemctl status haproxy
● haproxy.service - HAProxy Load Balancer
     Loaded: loaded (/usr/lib/systemd/system/haproxy.service; enabled; preset: enabled)
     Active: active (running) since Tue 2025-01-21 22:34:16 MSK; 27min ago
       Docs: man:haproxy(1)
             file:/usr/share/doc/haproxy/configuration.txt.gz
   Main PID: 6658 (haproxy)
     Status: "Ready."
      Tasks: 9 (limit: 9447)
     Memory: 85.9M ()
     CGroup: /system.slice/haproxy.service
             ├─6658 /usr/sbin/haproxy -Ws -f /etc/haproxy/haproxy.cfg -p /run/haproxy.pid -S /run/haproxy-master.sock
             └─6660 /usr/sbin/haproxy -Ws -f /etc/haproxy/haproxy.cfg -p /run/haproxy.pid -S /run/haproxy-master.sock

Jan 21 22:34:16 DESKTOP-6TMQOI1 systemd[1]: Starting haproxy.service - HAProxy Load Balancer...
Jan 21 22:34:16 DESKTOP-6TMQOI1 haproxy[6658]: [NOTICE]   (6658) : New worker (6660) forked
Jan 21 22:34:16 DESKTOP-6TMQOI1 haproxy[6658]: [NOTICE]   (6658) : Loading success.
Jan 21 22:34:16 DESKTOP-6TMQOI1 systemd[1]: Started haproxy.service - HAProxy Load Balancer.
```