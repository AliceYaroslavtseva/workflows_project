![example branch parameter](https://github.com/github/docs/actions/workflows/main.yml/badge.svg?branch=workflows)
## 1. [Описание](#1)
## 2. [Запуск проекта](#2)
## 3. [Импорт базы данных](#3)
## 4. [Техническая информация](#4)
## 5. [Об авторе](#5)

---
## 1. Описание <a id=1></a>

Продолжение группового проекта, изучение Actions, Docker, docker-compose, настройка деплоя на удалённый сервер через workflows.

---
## 2. Запуск проекта <a id=2></a>

для Linux-систем все команды необходимо выполнять от имени администратора
- Склонировать репозиторий
```
git clone git@github.com:AliceYaroslavtseva/yamdb_final.git
```
- Выполнить вход на удаленный сервер
- Установить docker на сервер:
```
apt install docker.io 
```
- Установить docker-compose на сервер:
```
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```
- Локально отредактировать файл infra/nginx.conf, обязательно в строке server_name вписать IP-адрес сервера
- Скопировать файлы docker-compose.yml и nginx.conf из директории infra на сервер:
```
scp docker-compose.yml <username>@<host>:/home/<username>/docker-compose.yml
scp nginx.conf <username>@<host>:/home/<username>/nginx.conf
```
- Создать .env файл по предлагаемому шаблону. Обязательно изменить значения POSTGRES_USER и POSTGRES_PASSWORD
    ```
    DB_ENGINE=django.db.backends.postgresql
    DB_NAME=postgres
    POSTGRES_USER=postgres
    POSTGRES_PASSWORD=postgres
    DB_HOST=db
    DB_PORT=5432
    SECRET_KEY=<секретный ключ проекта django>
    ```
- Для работы с Workflow добавить в Secrets GitHub переменные окружения для работы:
    ```
    DB_ENGINE=<django.db.backends.postgresql>
    DB_NAME=<имя базы данных postgres>
    DB_USER=<пользователь бд>
    DB_PASSWORD=<пароль>
    DB_HOST=<db>
    DB_PORT=<5432>
    
    DOCKER_PASSWORD=<пароль от DockerHub>
    DOCKER_USERNAME=<имя пользователя>
    
    SECRET_KEY=<секретный ключ проекта django>

    USER=<username для подключения к серверу>
    HOST=<IP сервера>
    PASSPHRASE=<пароль для сервера, если он установлен>
    SSH_KEY=<ваш SSH ключ (для получения команда: cat ~/.ssh/id_rsa)>

    TELEGRAM_TO=<ID чата, в который придет сообщение>
    TELEGRAM_TOKEN=<токен вашего бота>
    ```
    Workflow состоит из четырёх шагов:
     - Проверка кода на соответствие PEP8
     - Сборка и публикация образа бекенда на DockerHub.
     - Автоматический деплой на удаленный сервер.
     - Отправка уведомления в телеграм-чат.
- собрать и запустить контейнеры на сервере:
    ```
    docker-compose up -d --build
    ```
- После успешной сборки выполнить следующие действия (только при первом деплое):
    * провести миграции внутри контейнеров:
    ```
    docker-compose exec web python manage.py migrate
    ```
    * собрать статику проекта:
    ```
    docker-compose exec web python manage.py collectstatic --no-input
    ```  
    * Создать суперпользователя Django, после запроса от терминала ввести логин и пароль для суперпользователя:
    ```
    docker-compose exec web python manage.py createsuperuser
    ```
### Примеры API-запросов
Подробные примеры запросов и коды ответов приведены в прилагаемой документации в формате ReDoc

---
## 3. Импорт базы данных <a id=3></a>

- Заполнить базу данными
- Создать резервную копию данных:
```
docker-compose exec web python manage.py dumpdata > fixtures.json
```
- Остановить и удалить неиспользуемые элементы инфраструктуры Docker:
```
docker-compose down -v --remove-orphans
```

---
## 4. Техническая информация <a id=4></a>

- Python 3
- DRF (Django REST framework)
- Django ORM
- Docker
- Docker-compose
- Gunicorn
- nginx
- Django
- PostgreSQL
- GIT

---
## 5. Об авторе <a id=5></a>

Алиса Ярославцева:
```
Telegram: t.me/hellfoxalice
GitHub: github.com/AliceYaroslavtseva
```
