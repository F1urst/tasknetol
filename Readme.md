
#  "`Домашнее задание к занятию 2 «Кластеризация и балансировка нагрузки»`" - `Кормилицин Дмитрий`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!

### Задание 1

Запустите два simple python сервера на своей виртуальной машине на разных портах
Установите и настройте HAProxy, воспользуйтесь материалами к лекции по ссылке
Настройте балансировку Round-robin на 4 уровне.
На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy.

### Решение:
```
global
    daemon
    user haproxy
    group haproxy

defaults
    mode tcp             
    log global
    option tcplog
    timeout connect 5s
    timeout client 50s
    timeout server 50s

listen stats
    bind :8080         
    mode http
    stats enable
    stats uri /stats
    stats refresh 10s

frontend http-in
    bind *:80
    mode tcp
    default_backend web_servers

backend web_servers
    mode tcp
    balance roundrobin     
    server s1 127.0.0.1:8888 check
    server s2 127.0.0.1:9999 check
```

`скрины`
![cr](https://github.com/F1urst/tasknetol/blob/main/img/cr12.jpg)
![cr](https://github.com/F1urst/tasknetol/blob/main/img/cr2.jpg)
![cr](https://github.com/F1urst/tasknetol/blob/main/img/cr3.jpg)

### Задание 2

Запустите три simple python сервера на своей виртуальной машине на разных портах
Настройте балансировку Weighted Round Robin на 7 уровне, чтобы первый сервер имел вес 2, второй - 3, а третий - 4
HAproxy должен балансировать только тот http-трафик, который адресован домену example.local
На проверку направьте конфигурационный файл haproxy, скриншоты, где видно перенаправление запросов на разные серверы при обращении к HAProxy c использованием домена example.local и без него.

### Решение:

```
global
    daemon

defaults
    mode http                   
    timeout connect 5s
    timeout client 50s
    timeout server 50s

frontend web
    bind *:80
    mode http
    
    use_backend weighted_rr if { hdr(host) -i example.local }

    http-request deny deny_status 403 if !{ hdr(host) -i example.local }

backend weighted_rr
    mode http
    balance roundrobin          

    server srv1 127.0.0.1:8001 weight 2 check
    server srv2 127.0.0.1:8002 weight 3 check
    server srv3 127.0.0.1:8003 weight 4 check
```

`скрины`

![zq](https://github.com/F1urst/tasknetol/blob/main/img/zq1.jpg)
![zq](https://github.com/F1urst/tasknetol/blob/main/img/zq2.jpg)
![zq](https://github.com/F1urst/tasknetol/blob/main/img/zq3.jpg)
![zq](https://github.com/F1urst/tasknetol/blob/main/img/zq4.jpg)

