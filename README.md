# Домашнее задание: Docker, docker-compose, dockerfile
## 1.Dockerfile
- Создайте свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx)
- Определите разницу между контейнером и образом. Вывод опишите в домашнем задании.
- Ответьте на вопрос: Можно ли в контейнере собрать ядро?
- Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий.


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
Теоретически да, скачав исходники ядра и установив все необходимые пакеты для компиляции. 

## 2. Docker-compose
- Создайте кастомные образы nginx и php, объедините их в docker-compose.
- После запуска nginx должен показывать php info.
- Все собранные образы должны быть в docker hub

Создадим папку `nginx-php` со следующим содержимым:
```bash
tw4@tw4-mint:~/docker/nginx-php$ tree
.
├── default.conf
├── docker-compose.yml
└── html
    └── index.php
```


Создадим файл `docker-compose.yml`:

```yml
version: "3"
services:
  web:
    image: nginx:latest
    ports:
      - 8080:80
    volumes:
      - ./default.conf:/etc/nginx/conf.d/default.conf
      - ./html:/var/html
    networks:
      - nginxphp

  php:
    image: php:fpm
    expose:
      - 9000
    volumes:
      - ./html:/var/html
    networks:
      - nginxphp

networks:
  nginxphp:
```

С помощью `docker-compose` запускаем наши контейнеры:
```bash
w4@tw4-mint:~/docker/nginx-php$ docker-compose up -d
Creating network "nginx-php_nginxphp" with the default driver
Creating nginx-php_web_1 ... done
Creating nginx-php_php_1 ... done
tw4@tw4-mint:~/docker/nginx-php$ docker-compose ps
     Name                  Command             State             Ports          
--------------------------------------------------------------------------------
nginx-php_php_1   docker-php-entrypoint php-   Up      9000/tcp                 
                  fpm                                                           
nginx-php_web_1   /docker-entrypoint.sh ngin   Up      0.0.0.0:8080-            
                  ...                                  >80/tcp,:::8080->80/tcp 
```

Проверяем работоспособность:
```bash
tw4@tw4-mint:~/docker/nginx-php$ curl localhost:8080
<!DOCTYPE html>
<head>
    <title>Test</title>
</head>

<body>
    <h1>PHPINFO</h1>
    <p><!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml"><head>
<style type="text/css">
body {background-color: #fff; color: #222; font-family: sans-serif;}
pre {margin: 0; font-family: monospace;}
a:link {color: #009; text-decoration: none; background-color: #fff;}
a:hover {text-decoration: underline;}
table {border-collapse: collapse; border: 0; width: 934px; box-shadow: 1px 2px 3px rgba(0, 0, 0, 0.2);}
.center {text-align: center;}
.center table {margin: 1em auto; text-align: left;}
.center th {text-align: center !important;}
td, th {border: 1px solid #666; font-size: 75%; vertical-align: baseline; padding: 4px 5px;}
th {position: sticky; top: 0; background: inherit;}
h1 {font-size: 150%;}
....
```


