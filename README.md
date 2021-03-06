# Django-PostgreSQL (Docker)
Repo template to build a Django app with PostgreSQL inside docker containers.

## Project Setup
Inside root project, run the next commands:
```shell
$ python3 -m venv env
$ source env/bin/activate

(env)$ pip install django==3.2.12
(env)$ django-admin startproject {my_project} ./app/
```

You deactivate virtual environment, and remove it.

```shell
(env)$ deactivate
$ sudo rm -r env
```

## Django Settings
There are to modify a few lines inside **app/{my_project}/settings.py** file, in order to can use
environment variables set on **.env** file, which will be used to build and run Docker containers.

```python
import os  # ensure to import os module

SECRET_KEY = os.environ.get("SECRET_KEY")
DEBUG = int(os.environ.get("DEBUG", default=0))
ALLOWED_HOSTS = os.environ.get("DJANGO_ALLOWED_HOSTS").split(" ")

DATABASES = {
    "default": {
        "ENGINE": os.environ.get("SQL_ENGINE", "django.db.backends.sqlite3"),
        "NAME": os.environ.get("SQL_DATABASE", BASE_DIR / "db.sqlite3"),
        "USER": os.environ.get("SQL_USER", "user"),
        "PASSWORD": os.environ.get("SQL_PASSWORD", "password"),
        "HOST": os.environ.get("SQL_HOST", "localhost"),
        "PORT": os.environ.get("SQL_PORT", "5432"),
    }
}
```

## Docker Setup
Ensure to set environment vars on **.env** file, such as project name, database name, database user, etc.

Once set all vars, run the next commands to build the Docker image, and run the containers.
```shell
$ cp ./prject_files/* ./app/
$ docker-compose build --pull
$ docker-compose up -d
$ docker-compose exec web python manage.py migrate
```

## Run the project and other tips
Run and stop de project
```shell
$ docker-comopose start
$ docker-compose stop
```
Execute queries inside Postgres container
```shell
$ docker-compose exec db psql -U {POSTGRES_USER} -d {POSTGRES_DB}
$ docker-compose exec db psql -U {POSTGRES_USER} -d {POSTGRES_DB} -c "\dt"
```
Flush and migrate the database
```shell
$ docker-compose exec web python manage.py flush --no-input
$ docker-compose exec web python manage.py migrate
```
