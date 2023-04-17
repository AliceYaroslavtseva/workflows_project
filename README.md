![example branch parameter](https://github.com/github/docs/actions/workflows/main.yml/badge.svg?branch=workflows)
## yamdb_final
Проект YaMDb собирает отзывы пользователей на произведения.

## Стек технологий:
- Python 3
- DRF (Django REST framework)
- Django ORM
- Docker
- Gunicorn
- nginx
- Django
- PostgreSQL
- GIT

## Шаблон наполнения .env
```
# указываем, с какой БД работаем
DB_ENGINE=django.db.backends.postgresql
# имя базы данных
DB_NAME=
# логин для подключения к базе данных
POSTGRES_USER=
# пароль для подключения к БД (установите свой)
POSTGRES_PASSWORD=
# название сервиса (контейнера)
DB_HOST=
# порт для подключения к БД
DB_PORT=
```

## Описание команд для запуска приложения в контейнерах
Для запуска проекта в контейнерах используем **docker-compose** : ```docker-compose up -d --build```, находясь в директории (infra) с ```docker-compose.yaml```

После сборки контейнеров выполяем:
```bash
# Выполняем миграции
docker-compose exec web python manage.py migrate
# Создаем суперппользователя
docker-compose exec web python manage.py createsuperuser
# Собираем статику со всего проекта
docker-compose exec web python manage.py collectstatic --no-input
```
## Автоматизация развертывания серверного ПО
Ниже представлен Dockerfile - файл с инструкцией по разворачиванию Docker-контейнера веб-приложения:
```Dockerfile
FROM python:3.7-slim

WORKDIR /app

COPY . .

RUN pip3 install -r requirements.txt --no-cache-dir

CMD ["gunicorn", "api_yamdb.wsgi:application", "--bind", "0:8000" ]
```

В файле «docker-compose.yml» описываются запускаемые контейнеры: веб-приложения, СУБД PostgreSQL и сервера Nginx.
```yaml
version: '3.8'

services:
  db:
    image: postgres:13.0-alpine

    volumes:
      - dbdata:/var/lib/postgresql/data
    env_file:
      - ./.env
  web:
    image: godleib/yamdb_api:latest
    restart: always

    volumes:
    - static_value:/app/static/
    - media_value:/app/media/
    depends_on:
      - db
    env_file:
      - ./.env

  nginx:
    image: nginx:1.21.3-alpine

    ports:
      - "80:80"

    volumes:
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf

      - static_value:/var/html/static/

      - media_value:/var/html/media/

    depends_on:
      - web
volumes:
  static_value:
  media_value:
  dbdata:
```