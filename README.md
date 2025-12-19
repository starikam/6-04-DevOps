# Домашнее задание к занятию "Docker. Часть 2" - Викторов Михаил


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

### Задание 1

### Напишите ответ в свободной форме, не больше одного абзаца текста.
### Установите Docker Compose и опишите, для чего он нужен и как может улучшить лично вашу жизнь.

Docker Compose нужен для упрощённой оркестровки многоконтейнерных приложений: он позволяет описать в одном YAML‑файле все сервисы, 
сети и тома, а затем запустить их единой командой. Для меня это значит экономию времени на 
развёртывание и настройку окружения (не надо вручную поднимать базу данных, бэкенд, фронтенд и т. п.), 
воспроизводимость среды на разных машинах (один и тот же docker-compose.yml работает у меня и у коллег), 
а также лёгкость остановки/перезапуска всего стека  — всё изолировано в контейнерах и управляется парой команд.


---

### Задание 2

### Выполните действия и приложите текст конфига на этом этапе.

Создайте файл docker-compose.yml и внесите туда первичные настройки:

* version;
* services;
* volumes;
* networks.

При выполнении задания используйте подсеть 10.5.0.0/16. Ваша подсеть должна называться: <ваши фамилия и инициалы>-my-netology-hw. Все приложения из последующих заданий должны находиться в этой конфигурации.

### Решение:

```version: '3.8'

services:

volumes:

networks:
  viktorovmv-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```
---

### Задание 3

Выполните действия:

1) Создайте конфигурацию docker-compose для Prometheus с именем контейнера <ваши фамилия и инициалы>-netology-prometheus.
2) Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/prometheus ).
3) Обеспечьте внешний доступ к порту 9090 c докер-сервера.

### Решение:

```version: '3.9'

services:
  prometheus:
    image: prom/prometheus:latest
    container_name: viktorovmv-netology-prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./6-04/prometheus/prometheus.yml:/etc/prometheus/prometheus.yml
      - prometheus_data:/prometheus
    networks:
      - viktorovmv-my-netology-hw
    restart: always

volumes:
  prometheus_data:

networks:
  viktorovmv-my-netology-hw:
    driver: bridge
    ipam:
      config:
        - subnet: 10.5.0.0/16
```
Заодно обновил версия конфига на 3.9

---

### Задание 4

Выполните действия:

1) Создайте конфигурацию docker-compose для Pushgateway с именем контейнера <ваши фамилия и инициалы>-netology-pushgateway.
2) Обеспечьте внешний доступ к порту 9091 c докер-сервера.

### Решение:

Добавим в docker-compose.yml

```
  pushgateway:
    container_name: viktorovmv-netology-pushgateway
    image: prom/pushgateway:latest
    ports:
      - "9091:9091"
    networks:
      - viktorovmv-my-netology-hw
    restart: unless-stopped
    depends_on:
      - prometheus
```

---

### Задание 5

Выполните действия:

1) Создайте конфигурацию docker-compose для Grafana с именем контейнера <ваши фамилия и инициалы>-netology-grafana.
2) Добавьте необходимые тома с данными и конфигурацией (конфигурация лежит в репозитории в директории 6-04/grafana.
3) Добавьте переменную окружения с путем до файла с кастомными настройками (должен быть в томе), в самом файле пропишите логин=<ваши фамилия и инициалы> пароль=netology.
4) Обеспечьте внешний доступ к порту 3000 c порта 80 докер-сервера.

### Решение:

Добавим в docker-compose.yml

```
  grafana:
    container_name: viktorovmv-netology-grafana
    image: grafana/grafana-enterprise:latest
    ports:
      - "80:3000"
    environment:
      - GF_PATHS_CONFIG=/etc/grafana/grafana.ini
      - GF_SECURITY_ADMIN_USER=viktorovmv
      - GF_SECURITY_ADMIN_PASSWORD=netology
    volumes:
      - ./6-04/grafana/grafana.ini:/etc/grafana/grafana.ini
      - grafana_data:/var/lib/grafana
    networks:
      - viktorovmv-my-netology-hw
    depends_on:
      - pushgateway
    restart: unless-stopped
```

---

### Задание 6

Выполните действия.

Настройте поочередность запуска контейнеров.
Настройте режимы перезапуска для контейнеров.
Настройте использование контейнерами одной сети.
Запустите сценарий в detached режиме.

### Решение:

Контейнеры запускаются поочередно: Pushgateway → Prometheus → Grafana. 
Добавлены режимы перезапуска always для всех контейнеров. 
Все контейнеры используют оду сеть viktorovmv-my-netology-hw. 
Сценарий запущен в detached-режиме с помощью `docker compose up -d`

---

### Задание 7

Выполните действия.

1) Выполните запрос в Pushgateway для помещения метрики <ваши фамилия и инициалы> со значением 5 в Prometheus: echo "<ваши фамилия и инициалы> 5" | curl --data-binary @- http://localhost:9091/metrics/job/netology.
2) Залогиньтесь в Grafana с помощью логина и пароля из предыдущего задания.
3) Cоздайте Data Source Prometheus (Home -> Connections -> Data sources -> Add data source -> Prometheus -> указать "Prometheus server URL = http://prometheus:9090" -> Save & Test).
4) Создайте график на основе добавленной в пункте 5 метрики (Build a dashboard -> Add visualization -> Prometheus -> Select metric -> Metric explorer -> <ваши фамилия и инициалы -> Apply.
5) В качестве решения приложите:

docker-compose.yml целиком;
скриншот команды docker ps после запуске docker-compose.yml;
скриншот графика, постоенного на основе вашей метрики.

### Решение:

Задания выполнены, я отправил несколько метрик чтобы построить небольшой график для наглядности.

Файл [docker-compose.yml](https://github.com/starikam/6-04-DevOps/blob/main/docker-compose.yml)

Скриншот команды docker ps после запуска docker-compose:
(https://github.com/starikam/6-04-DevOps/blob/main/screenshots/2025-12-19_18-49-20.png)
