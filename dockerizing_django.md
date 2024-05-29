##  Steps

1. Create the Django app locally
2. Define app requirements in ``djangoapp/requirements.txt``
3. Define enviroment variables in ``dotenv/.env``
4. Define commands to be run after container is up in ``scripts/command.sh``
5. Create ``Dockerfile``
6. Create ``docker.compose.yaml``

## Defining the ``dotenv/.env`` :
The file will contain the enviroment variables that will be loaded into Django and ``docker_compose.yaml`` for setting up the database.

- If using PostgreSQL:
```
SECRET_KEY="CHANGE-ME"

# 0 False, 1 True
DEBUG="1"

ALLOWED_HOSTS="127.0.0.1, localhost"

# PostgreSQL settings
DB_ENGINE="django.db.backends.postgresql"
POSTGRES_DB="CHANGE-ME"
POSTGRES_USER="CHANGE-ME"
POSTGRES_PASSWORD="CHANGE-ME"
POSTGRES_HOST="localhost"
POSTGRES_PORT="5432"
```
- If using MySQL:
```
SECRET_KEY="CHANGE-ME"

# 0 False, 1 True
DEBUG="1"

DJANGO_ALLOWED_HOSTS="localhost,127.0.0.1,[::1]"

# MySQL settings
DB_ENGINE="django.db.backends.mysql"
MYSQL_DATABASE="CHANGE-ME"
MYSQL_USER="CHANGE-ME"
MYSQL_ROOT_PASSWORD="CHANGE-ME"
MYSQL_PASSWORD="CHANGE-ME"
MYSQL_HOST="localhost"
MYSQL_PORT="3306"
```
## Defining ``scripts/command.sh``
Create a file ``scripts/command.sh`` this bash commands will be run after the container creation checking if the database is up, making migrations and starting the Django development server.

- If using PostgreSQL
```
#!/bin/sh

# The shell will exit immediately if a command exits with a non-zero status
set -e

# Check if the PostgreSQL database is up and accepting connections
while ! nc -z $POSTGRES_HOST $POSTGRES_PORT; do
  echo "🟡 Waiting for Postgres Database Startup ($POSTGRES_HOST $POSTGRES_PORT) ..."
  sleep 2
done

echo "✅ Postgres Database Started Successfully ($POSTGRES_HOST:$POSTGRES_PORT)"

# Run Django management commands
python manage.py collectstatic --noinput
python manage.py makemigrations --noinput
python manage.py migrate --noinput

# Start the Django development server
python manage.py runserver 0.0.0.0:8000
```
- If using MySQL:
```
# The shell will exit immediately if a command exits with a non-zero status
set -e

# Check if the MySQL database is up and accepting connections
while ! nc -z $MYSQL_HOST $MYSQL_PORT; do
  echo "🟡 Waiting for MySQL Database Startup ($MYSQL_HOST:$MYSQL_PORT) ..."
  sleep 2
done

echo "✅ MySQL Database Started Successfully ($MYSQL_HOST:$MYSQL_PORT)"

# Run Django management commands
python manage.py collectstatic --noinput
python manage.py makemigrations --noinput
python manage.py migrate --noinput

# Start the Django development server
python manage.py runserver 0.0.0.0:8000
```
## Creating the ``Dockerfile``
```
FROM python:3.11.3-alpine3.18
LABEL mantainer="otavioaf97@gmail.com"

# ENV variable to stop python from writing the unwanted .pyc files.
ENV PYTHONDONTWRITEBYTECODE 1

# Defines that Python output will be shown in console with no buffering.
ENV PYTHONUNBUFFERED 1

COPY djangoapp /djangoapp
COPY scripts /scripts

# Set django project folder as working directory.
WORKDIR /djangoapp

# Expose port used by Django.
EXPOSE 8000

# Create a virtual enviroment, a user, and change ownership and permitions to the new user.
RUN python -m venv /venv && \
  /venv/bin/pip install --upgrade pip && \
  /venv/bin/pip install -r /djangoapp/requirements.txt && \
  adduser --disabled-password --no-create-home duser && \
  mkdir -p /data/web/static && \
  mkdir -p /data/web/media && \
  chown -R duser:duser /venv && \
  chown -R duser:duser /data/web/static && \
  chown -R duser:duser /data/web/media && \
  chmod -R 755 /data/web/static && \
  chmod -R 755 /data/web/media && \
  chmod -R +x /scripts

ENV PATH="/scripts:/venv/bin:$PATH"

USER duser

CMD ["commands.sh"]
```
#### 1. Define the base image. Here, we are using Python and Alpine Linux as our base images:

```
FROM python:3.11.3-alpine3.18
LABEL mantainer="otavioaf97@gmail.com"

# ENV variable to stop python from writing the unwanted .pyc files.
ENV PYTHONDONTWRITEBYTECODE 1

# Defines that Python output will be shown in console with no buffering.
ENV PYTHONUNBUFFERED 1
```

#### 2. Copy the folders that will be built in the container:
```
COPY djangoapp /djangoapp
COPY scripts /scripts

# Set django project folder as working directory.
WORKDIR /djangoapp
```
#### 3. Expose the port Django will use. The port will be available for external conection with the container
```
# Expose port used by Django.
EXPOSE 8000
```

#### 4. RUN will run shell commands to create a virtual enviroment, a user, and change ownership and permitions from the root to the new user:


- Create a virtual environment and installing dependencies.
  ```
  RUN \
    python -m venv /venv && \
    /venv/bin/pip install --upgrade pip && \
    /venv/bin/pip install -r /djangoapp/requirements.txt && \
  ```
- Add a new user with restricted permissions.
  ```
  adduser --disabled-password --no-create-home duser && \
  ```
- Create directories for static and media files:
  ```
  mkdir -p /data/web/static && \
  mkdir -p /data/web/media && \
  ```
- Change ownership and permissions to the new user:
  ```
  chown -R duser:duser /venv && \
  chown -R duser:duser /data/web/static && \
  chown -R duser:duser /data/web/media && \
  chmod -R 755 /data/web/static && \
  chmod -R 755 /data/web/media && \
  chmod -R +x /scripts
  ```

#### 5. Update PATH variable to ensure that the scripts and the binaries from the virtual environment are available in the container’s PATH.
```
ENV PATH="/scripts:/venv/bin:$PATH"
```
#### 6. Switch to the non-privileged user. This limits the permissions of the running processes, enhancing security.
```
USER duser
```
#### 7. Execute scripts/commands.sh
```
CMD ["commands.sh"]
```

