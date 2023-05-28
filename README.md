# Проект YaMDB
![Github actions](https://github.com/xiba21/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)

### Описание
Проект YaMDB собирает отзывы пользователей на произведения (книги, фильмы, музыку). Сами произведения здесь не хранятся. Незарегистрированные пользователи могут просматривать информацию о произведениях, жанрах и категориях произведений, а также читать отзывы и комментарии.
Зарегистрированные пользователи могут оставлять отзывы и комментарии на произведения, а также выставлять оценку от 1 до 10. Право добавлять произведения, жанры и категории есть только у администраторов проекта. 

----

### Шаблон заполнения файла .env

В директорию infra необходимо добавить файл .env. Пример заполнения файла можно найти в файле .env.example в директории infra.

----

### Как запустить проект локально:

Клонировать репозиторий и перейти в него в командной строке:

```
git@github.com:Xiba21/yamdb_final.git
```

```
cd infra
```

Запустить docker-compose:

```
docker-compose up
```

После окончания сборки контейнеров выполнить миграции:
```
docker-compose exec web python manage.py migrate
```

Создать суперпользователя:

```
docker-compose exec web python manage.py createsuperuser
```

Загрузить статику:

```
docker-compose exec web python manage.py collectstatic --no-input 
```

Заполнить базу данными:

```
docker-compose exec web python manage.py loaddata fixtures.json 
```

Проверить работу проекта по ссылке:

```
http://localhost/admin/ 
```
----

### Как запустить проект на боевом сервере:

Установить на сервере docker и docker-compose.
Скопировать на сервер файлы docker-compose.yaml и default.conf:
```
scp docker-compose.yaml <логин_на_сервере>@<IP_сервера>:/home/<логин_на_сервере>/docker-compose.yaml
scp default.conf <логин_на_сервере>@<IP_сервера>:/home/<логин_на_сервере>/nginx/docker-compose.yaml
```

Добавить в Secrets на Github следующие данные:
```
DB_ENGINE=django.db.backends.postgresql # указать, что проект работает с postgresql
DB_NAME=postgres # имя базы данных
POSTGRES_USER=postgres # логин для подключения к базе данных
POSTGRES_PASSWORD=postgres # пароль для подключения к БД
DB_HOST=db # название сервиса БД (контейнера) 
DB_PORT=5432 # порт для подключения к БД
DOCKER_PASSWORD= # Пароль от аккаунта на DockerHub
DOCKER_USERNAME= # Username в аккаунте на DockerHub
HOST= # IP удалённого сервера
USER= # Логин на удалённом сервере
SSH_KEY= # SSH-key компьютера, с которого будет происходить подключение к удалённому серверу
PASSPHRASE= #Если для ssh используется фраза-пароль
TELEGRAM_TO= #ID пользователя в Telegram
TELEGRAM_TOKEN= #ID бота в Telegram
```

Выполнить команды:
* git add .
* git commit -m "<commit>"
* git push

После этого будут запущены процессы workflow:
* проверка кода на соответствие стандарту PEP8 (с помощью пакета flake8) и запуск pytest
* сборка и доставка докер-образа для контейнера web на Docker Hub
* автоматический деплой проекта на боевой сервер
* отправка уведомления в Telegram о том, что процесс деплоя успешно завершился

После успешного завершения процессов workflow на боевом сервере необходимо выполнить следующие команды:
```
sudo docker-compose exec web python manage.py migrate
```
```
sudo docker-compose exec web python manage.py createsuperuser
```
```
sudo docker-compose exec web python manage.py collectstatic --no-input 
```

----


### Как импортировать данные из csv-файлов в базу данных

Предусмотрены два способа загрузки данных из csv-файлов:

* Через админ-панель (использована библиотека django-import-export).
* С помощью management-команд. Для того, чтобы загрузить все данные, из корневой директории проекта выполните команду:

```
python3 manage.py load_data_csv --use_default_dataset
```

Чтобы загрузить данные из определённого файла для конкретной модели, выполните команду:

```
python3 manage.py load_data_csv --path <путь к csv-файлу> --model_name <имя модели> --app_name <название приложения>
```

Например, для импорта данных о пользователях из файла 'users.csv' команда будет следующей:

```
python3 manage.py load_data_csv --path static/data/users.csv --model_name user --app_name users
```

Данные из файлов необходимо загружать в следующем порядке:
* users.csv
* genre.csv
* category.csv
* titles.csv
* genre_title.csv
* review.csv
* comments.csv

----

### Авторы проекта

Александр Розинков
