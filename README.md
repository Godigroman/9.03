# Домашнее задание к занятию "`ELK`" - `Клименко Олег`


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
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 1. Elasticsearch

Установите и запустите Elasticsearch, после чего поменяйте параметр cluster_name на случайный.

`Приведите скриншот команды 'curl -X GET 'localhost:9200/_cluster/health?pretty', сделанной на сервере с установленным Elasticsearch. Где будет виден нестандартный cluster_name.`

![](https://cdn.discordapp.com/attachments/1258765295351365672/1260190449805688902/image.png?ex=66906590&is=668f1410&hm=3a664bb585091d3f3866b190cac4d3498369c5c4bc232c65521b32caefff48a4&)

---

### Задание 2. Kibana

Установите и запустите Kibana.

`Приведите скриншот интерфейса Kibana на странице http://<ip вашего сервера>:5601/app/dev_tools#/console, где будет выполнен запрос GET /_cluster/health?pretty.`

![](https://cdn.discordapp.com/attachments/1258765295351365672/1260190906854801469/image.png?ex=669065fd&is=668f147d&hm=4209428822261fa5a1baab89dfa0f1cb900dd533213429dccfb2f9e94410e32b&)

---

### Задание 3. Logstash

Установите и запустите Logstash и Nginx. С помощью Logstash отправьте access-лог Nginx в Elasticsearch.

```
input {
  file {
    path => "/var/log/nginx/access.log"
    start_position => "beginning"
  }
}

filter {
    grok {
        match => { "message" => "%{IPORHOST:remote_ip} - %{DATA:user} \[%{HTTPDATE:access_time}\] \"%{WORD:http_method} %{DATA:url} HTTP/%{NUMBER:http_version}\" %{NUMBER:response_code} %{NUMBER:body_sent_bytes} \"%{DATA:referrer}\" \"%{DATA:agent}\"" }
    }
    mutate {
        remove_field => [ "host" ]
    }
}

output {
    elasticsearch {
        hosts => "http://localhost:9200"
        data_stream => "true"
    }
}
```

`Приведите скриншот интерфейса Kibana, на котором видны логи Nginx.`

![](https://cdn.discordapp.com/attachments/1258765295351365672/1260193386170683402/image.png?ex=6690684c&is=668f16cc&hm=a8e2f73489f4b3bb4fdf218facf9b2dbc3c67f6b4cff7fafcc66ac2f7a8c8594&)

---

### Задание 4. Filebeat

Установите и запустите Filebeat. Переключите поставку логов Nginx с Logstash на Filebeat.

```
input {
  beats {
    port => 5044
  }
}

filebeat.inputs:
  - type: log
    enabled: true
    paths:
      - /var/log/nginx/access.log

output.logstash:
hosts: ["127.0.0.1:5044"]
```

`Приведите скриншот интерфейса Kibana, на котором видны логи Nginx, которые были отправлены через Filebeat.`

![](https://cdn.discordapp.com/attachments/1258765295351365672/1260195509566574602/image.png?ex=66906a46&is=668f18c6&hm=e897b4c99df15e76703d9f076892ad33417e0f3a86a075c0e9dfc3d08f62ff6c&)

---
---
