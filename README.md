# Домашнее задание к занятию "`Введение. Экосистема. Архитектура. Жизненный цикл Docker-контейнера`" - `Никулин Михаил Сергеевич`



---

### Задание 1

Скачиваем образ nginx:
```
root@epdu92ubn4tgb40jihfj:~/docker# docker pull nginx
```
Создаем dockerfile:
```
root@epdu92ubn4tgb40jihfj:~/docker# nano dockerfile
FROM nginx
RUN echo '<html><head>Hey, Netology</head><body><h1>I am DevOps Engineer!</h1></body></html>' > \
    /usr/share/nginx/html/index.html
```

Делаем fork образа:
```
root@epdu92ubn4tgb40jihfj:~/docker# docker build -f dockerfile -t nikulinms/devops-netology:5.3 .
```

Подключаемся к hub.docker.com:
```
root@epdu92ubn4tgb40jihfj:~/docker# docker login -u <username> -p <password>
```

Пушим образ в репозиторий на hub.docker.com:
```
root@epdu92ubn4tgb40jihfj:~/docker# docker push nikulinms/devops-netology:5.3
```
[Ссылка на образ](https://hub.docker.com/repository/docker/nikulinms/devops-netology/general)\
[Ссылка на образ 2](https://hub.docker.com/layers/nikulinms/devops-netology/5.3/images/sha256-5a6abc5330a66a03c621c7350d42ee2d9e561668f995144b72477504f1458dea?context=repo)

Запускаем контейнер с пробросом на 8080 порт хоста:
```
root@epdu92ubn4tgb40jihfj:~/docker# docker run -d -p 8080:80 nikulinms/devops-netology:5.3
```

![task_1.png](img%2Ftask_1.png)

---

### Задание 2

- Высоконагруженное монолитное java веб-приложение;
> Физическая машина, чтобы не расходовать ресурсы на виртуализацию и из-за монолитности не будет проблем с разворачиванием на разных машинах.
- Nodejs веб-приложение;
> Docker, для более простого воспроизведения зависимостей в рабочих средах
- Мобильное приложение c версиями для Android и iOS;
> Виртуальные машины, проще для тестирования, размещения на одной хостовой машине
- Шина данных на базе Apache Kafka;
> Docker, есть готовые образы для apache kafka, на руку изолированность приложений, а также легкий откат на стабильные версии в случае обнаружения багов в продакшене
- Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;
> Docker, Elasticsearch доступен для установки как образ docker, проще удалять логи, удобнее при кластеризации - меньше времени на запуск контейнеров.
- Мониторинг-стек на базе Prometheus и Grafana;
> Docker. Есть готовые образы, приложения не хранят данные, что удобно при контейниризации, удобно масштабировать и быстро разворачивать.
- MongoDB, как основное хранилище данных для java-приложения;
> Физическая машина как наиболее надежное, отказоустойчивое решение. Либо виртуальный сервер.
- Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry
> Могут быть применены все варианты, в зависимости от наличия соответствующих ресурсов. Но для большей изолированности лучше использовать docker.

---

### Задание 3

- Запускаем первый контейнер из образа ***centos*** c любым тэгом в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера:  
```
root@epdu92ubn4tgb40jihfj:~/docker# docker run -v /data:/data --name centos-container -d -t centos
```

- Запускаем второй контейнер из образа ***debian*** в фоновом режиме, подключив папку ```/data``` из текущей рабочей директории на хостовой машине в ```/data``` контейнера:  
```
root@epdu92ubn4tgb40jihfj:~/docker# docker run -v /data:/data --name debian-container -d -t debian
```

- Смотрим запущенные контейнеры:
```
root@epdu92ubn4tgb40jihfj:~/docker# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
0b6b2a7ae1fc   debian    "bash"        2 minutes ago   Up 2 minutes             debian-container
4787af1b11e5   centos    "/bin/bash"   4 minutes ago   Up 4 minutes             centos-container
```

- Подключаемся к первому контейнеру с помощью ```docker exec``` и создаем текстовый файл в ```/data```:  
```
root@epdu92ubn4tgb40jihfj:~/docker# docker exec centos-container /bin/bash -c "echo test>/data/readme_centos.md"
```

- Добавляем еще один файл в папку ```/data``` на хостовой машине:
```
root@epdu92ubn4tgb40jihfj:~/docker# echo test_2 > /data/readme_host.md
```

- Подключаемся во второй контейнер и отображаем листинг и содержание файлов в ```/data``` контейнера.  
```
root@epdu92ubn4tgb40jihfj:~/docker# docker exec -it debian-container /bin/bash
root@0b6b2a7ae1fc:/# cd /data/
root@0b6b2a7ae1fc:/data# ls -l
total 8
-rw-r--r-- 1 root root 5 May 21 19:08 readme_centos.md
-rw-r--r-- 1 root root 7 May 21 19:11 readme_host.md
root@0b6b2a7ae1fc:/data# cat readme_centos.md
test
root@0b6b2a7ae1fc:/data# cat readme_host.md
test_2
```


## Дополнительные задания (со звездочкой*)


### Задание 4*

- Создаем Dockerfile и вставляем в него код из лекции:
- ошибка в коде docker/Dockerfile, срока 24 ```COPY ansbile.cfg /ansible/``` должно быть ```COPY ansible.cfg /ansible/```
```
root@epdu92ubn4tgb40jihfj:~/task_4# nano dockerfile
```

- Сбираем образ:
```
root@epdu92ubn4tgb40jihfj:~/task_4# docker build -t nikulinms/ansible:2.9.24 .
```