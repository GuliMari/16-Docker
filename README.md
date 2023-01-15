# Домашнее задание: Docker, docker-compose, dockerfile
## 1.Dockerfile
- Создайте свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx)
- Определите разницу между контейнером и образом. Вывод опишите в домашнем задании.
- Ответьте на вопрос: Можно ли в контейнере собрать ядро?
- Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий.

## Выполнение ДЗ
Создаем `Dockerfile` для кастомного образа `nginx`:

```dockerfile
FROM alpine:latest
RUN apk -U upgrade && apk add nginx
COPY index.html /usr/share/nginx/html/
COPY default.conf /etc/nginx/http.d/
CMD ["nginx", "-g", "daemon off;"]
```
Собираем образ и запускаем контейнер:

```bash
tw4@tw4-mint:~/docker/nginx$ docker build -t myng .
Sending build context to Docker daemon  6.144kB
Step 1/5 : FROM alpine:latest
...
Successfully built 3f96c0d4362c
Successfully tagged myng:latest
tw4@tw4-mint:~/docker/nginx$ docker run -d -p 8080:80 myng
bb2431d40f58d0aee3c2052ef0749c23eb8e4ac674a483bd2139a84c11bfd50c
```

Проверяем работоспособность контейнера:
```bash
tw4@tw4-mint:~/docker/nginx$ curl localhost:8080
<!DOCTYPE html>
<html>
<head>
        <title> Custom NGINX </title>
</head>
<body>
        <body style=text-align:center;background-color:rgb(5,73,17);font-weight:900;font-size:20px;font-family:Helvetica,Arial,sans-serif>
        <img src="https://www.docker.com/wp-content/uploads/2022/03/Moby-logo.png">
        <h1> Welcome to my custom nginx webpage hosted in a Docker container </h1>
        <p> This container was deployed: <div id="date"></div></p>
</body>
</html>
```

Образ на Dockerhub https://hub.docker.com/repository/docker/mariguli/otus/general

### Разница между образом и контейнером
Образ - это шаблон из нескольких read-only слоев, из которого можно неограниченное количество раз создавать контейнер.    
Контейнер - это запущенный образ, поверх которого имеется read-write слой.

### Можно ли в контейнере собрать ядро?


