﻿# CI/CD для проекта API YAMDB

## Технологический стек
[![Django-app workflow](https://github.com/Spookyviking/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)](https://github.com/Spookyviking/yamdb_final/actions/workflows/yamdb_workflow.yml)
[![Python](https://img.shields.io/badge/-Python-464646?style=flat&logo=Python&logoColor=56C0C0&color=008080)](https://www.python.org/)
[![Django](https://img.shields.io/badge/-Django-464646?style=flat&logo=Django&logoColor=56C0C0&color=008080)](https://www.djangoproject.com/)
[![Django REST Framework](https://img.shields.io/badge/-Django%20REST%20Framework-464646?style=flat&logo=Django%20REST%20Framework&logoColor=56C0C0&color=008080)](https://www.django-rest-framework.org/)
[![PostgreSQL](https://img.shields.io/badge/-PostgreSQL-464646?style=flat&logo=PostgreSQL&logoColor=56C0C0&color=008080)](https://www.postgresql.org/)
[![JWT](https://img.shields.io/badge/-JWT-464646?style=flat&color=008080)](https://jwt.io/)
[![Nginx](https://img.shields.io/badge/-NGINX-464646?style=flat&logo=NGINX&logoColor=56C0C0&color=008080)](https://nginx.org/ru/)
[![gunicorn](https://img.shields.io/badge/-gunicorn-464646?style=flat&logo=gunicorn&logoColor=56C0C0&color=008080)](https://gunicorn.org/)
[![Docker](https://img.shields.io/badge/-Docker-464646?style=flat&logo=Docker&logoColor=56C0C0&color=008080)](https://www.docker.com/)
[![Docker-compose](https://img.shields.io/badge/-Docker%20compose-464646?style=flat&logo=Docker&logoColor=56C0C0&color=008080)](https://www.docker.com/)
[![Docker Hub](https://img.shields.io/badge/-Docker%20Hub-464646?style=flat&logo=Docker&logoColor=56C0C0&color=008080)](https://www.docker.com/products/docker-hub)
[![GitHub%20Actions](https://img.shields.io/badge/-GitHub%20Actions-464646?style=flat&logo=GitHub%20actions&logoColor=56C0C0&color=008080)](https://github.com/features/actions)
[![Yandex.Cloud](https://img.shields.io/badge/-Yandex.Cloud-464646?style=flat&logo=Yandex.Cloud&logoColor=56C0C0&color=008080)](https://cloud.yandex.ru/)

## Описание проекта
Проект **YaMDb** собирает **отзывы (Review)** пользователей на **произведения (Titles)**. Произведения делятся на категории: «Книги», «Фильмы», «Музыка». Список категорий (*Category*) может быть расширен.

Сами произведения в **YaMDb** не хранятся, здесь нельзя посмотреть фильм или послушать музыку.

В каждой категории есть **произведения**: книги, фильмы или музыка. Например, в категории «Книги» могут быть произведения «Винни Пух и все-все-все» и «Марсианские хроники», а в категории «Музыка» — песня «Давеча» группы «Насекомые» и вторая сюита Баха.

Произведению может быть присвоен **жанр (Genre)** из списка предустановленных (например, «Сказка», «Рок» или «Артхаус»). Новые жанры может создавать только администратор.

Благодарные или возмущённые читатели оставляют к произведениям текстовые **отзывы (*Review*)** и ставят произведению оценку в диапазоне от одного до десяти (целое число); из пользовательских оценок формируется усреднённая оценка произведения — **рейтинг** (целое число). На одно произведение пользователь может оставить только один отзыв.


## Техническое описание проекта

К проекту по адресу http://51.250.96.184/redoc/ подключена документация **API YaMDb**.
В ней описаны возможные запросы к API и структура ожидаемых ответов.
Для каждого запроса указаны уровни прав доступа: пользовательские роли, которым разрешён запрос.

## Технологии:
* Python 3.7
* Django 2
* Docker
* Nginx
* Github Action

## Описание Workflow

Workflow состоит из четырёх шагов:
1. Проверка кода на соответствие PEP8 и запуск тестов проекта;
2. Сборка и публикация образа на DockerHub;
3. Автоматический деплой на удаленный сервер;
4. Отправка telegram-ботом уведомления в чат.

## Установка:
1. Клонируйте репозиторий на локальную машину.
   ```https://github.com/Spookyviking/yamdb_final.git```
2. Установите виртуальное окружение в папке проекта.
```
cd yamdb_final
python -m venv venv
```
3. Активируйте виртуальное окружение.
   ```source venv\Scripts\activate```
4. Установите зависимости.
```
python -m pip install --upgrade pip
pip install -r api_yamdb\requirements.txt
```
## Запуск проекта в контейнерах
1. Перейдите в директорию `infra/`, заполните файл .env.template и после этого переименуйте его в .env
2. Выполните команду:
   ```docker-compose up -d --build```
3. Для остановки контейнеров из директории `infra/` выполните команду:
   ```docker-compose down -v```
5. ```(опционально) docker-compose exec web python manage.py makemigrations  docker-compose exec web python manage.py migrate```  
Создайте суперпользователя:
```
docker-compose exec web python manage.py createsuperuser
```
При необходимости наполните базу тестовыми данными из ../yamdb_final/api_yamdb/:
```
docker exec -i infra_web_1 python manage.py loaddata --format=json - < fixtures.json
```
или
```
docker-compose exec web python manage.py loaddata fixtures.json
```

Также можно выполнить эти действия внутри контейнера. Отобразите список работающих контейнеров:
```
sudo docker container ls -a
```
В списке контейнеров копируйте CONTAINER ID контейнера username/api_yamdb:latest (username - имя пользователя на DockerHub).   
Выполните вход в контейнер:
```
sudo docker exec -it <CONTAINER_ID> bash
```
Внутри контейнера выполните миграции:
```
python manage.py migrate
```
При необходимости наполните базу данных начальными тестовыми данными:
```
python3 manage.py shell
>>> from django.contrib.contenttypes.models import ContentType
>>> ContentType.objects.all().delete()
>>> quit()
python manage.py loaddata infra/fixtures.json
```
## Deploy проекта на удаленный сервер
Предварительно для автоматического деплоя необходимо подготовить сервер:
1. Установите соединение с сервером:

``` ssh username@server_address ```

2. Проверьте статус nginx:

``` sudo service nginx status ```

3. Если nginx запущен, остановите его:

``` sudo systemctl stop nginx ```

4. Установить docker:  
``` sudo apt install docker.io ```
5. Установите docker-compose:  

``` 
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

6. Проверьте корректность установки Docker-compose:

``` sudo  docker-compose --version ```

7. Скопируйте файлы docker-compose.yaml и nginx/default.conf из проекта на сервер в
home/<ваш_username>/docker-compose.yaml и home/<ваш_username>/nginx/default.conf соответственно.
8. В Secrets GitHub Actions форкнутого репозитория добавить переменные окружения:
   * SSH_KEY - ssh private key для доступа к удаленному серверу
   * HOST - public id хоста
   * USER - имя user-а на удаленном сервере
   * PASSPHRASE - пароль подтверждения подключения по ssh-key
   * DOCKER_USERNAME - username на DockerHub
   * DOCKER_PASSWORD - пароль на DockerHub
   * POSTGRES_USER - имя пользователя для базы данных
   * POSTGRES_PASSWORD - пароль для подключения к базе
   * DB_ENGINE - настойка подключения django-проекта к postgresql
   * DB_NAME - имя базы данных
   * DB_HOST - название сервиса (контейнера)
   * DB_PORT - порт для подключения к БД
   * TELEGRAM_TOKEN - token telegram-бота
   * TELEGRAM_TO - id пользователя, которому будут приходить оповещения
об успешном деплое.

При внесении любых изменений в проект, после коммита и пуша
```
git add .
git commit -m "..."
git push
```
запускается набор блоков команд jobs (см. файл yamdb_workflow.yaml), т.к. команда `git push` является триггером workflow проекта.

## Ссылка на проект
Проект развернут по адресу http://51.250.96.184/api/v1/
http://51.250.96.184/admin
http://51.250.96.184/redoc

## Автор:
[Максим Остапенко](https://github.com/Spookyviking)
