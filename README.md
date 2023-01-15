# Домашнее задание: Docker, docker-compose, dockerfile
## 1.Dockerfile
- Создайте свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx)
- Определите разницу между контейнером и образом. Вывод опишите в домашнем задании.
- Ответьте на вопрос: Можно ли в контейнере собрать ядро?
- Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий.

## Выполнение ДЗ
Создаем `Dockerfile` для кастомного образа `nginx`:

```dockerfile
FROM alpine:3.16
RUN apk add nginx
COPY default.conf /etc/nginx/http.d/
COPY index.html /usr/share/nginx/html
CMD ["nginx", "-g", "daemon off;"]
```
