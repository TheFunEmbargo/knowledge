# Docker

## Commands

Get all containers

`docker ps -a`

Get all running Containers

`docker ps`

Start / Stop

`docker stop [CONTAINER ID]`

`docker start [CONTAINER ID]`

Connect to a container Interactively 

[docker] [execute] [interactively] [container id] [command to execute]

`docker exec -it 41a0f89993e4 bash`

Cleanup old containers (...and images?)

`docker system prune`

flag for root user

`--user 0`
 
## Containerising Database seeds

Running DBs in dockerised containers for development, allowing you to swap between up'd containers to test different database seeds.

```bash
docker run --name my_postgres_container \
    -e POSTGRES_DB=name_my_database \
    -e POSTGRES_USER=myuser \
    -e POSTGRES_PASSWORD=mypassword \
    -p external_port:container_port \
    -v /path/to/local:/docker-entrypoint-initdb.d \
    -d [postgis/postgis:14-3.3-alpine](https://hub.docker.com/search?q=)
``` 

-v mounts a volume, in this instance a sql dump from another DB (see postgres.md).

-d runs the container in detached mode (in the background).

Initialisation scripts can be placed in [/docker-entrypoint-initdb.d](https://hub.docker.com/_/postgres). However you could mount anything anywhere in the container...

Connect to the container and load the sql in, if not done so on startup. (see postgres.md)


## Dockerising a generic Fast API, Alembic, Postgres Stack

Dockerise the alembic migrations `/app/docker/Dockerfile.alembic`
```
FROM python:3.10-slim

# POSTGRES system dependencies
# build-essential for psycopg2
# libpq-dev for pg_config
RUN apt-get update \
    && apt-get install -y \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
RUN pip install poetry

# poetry configuration files
COPY pyproject.toml poetry.lock* /app/
RUN poetry install --only migration

# alembic configuration and migration scripts
COPY alembic.ini /app/
COPY alembic /app/alembic

# run alembic migrations
CMD ["poetry", "run", "alembic", "upgrade", "head"]

```

dockerise the FastAPI app `/app/docker/Dockerfile.app`

```
FROM python:3.10-slim
WORKDIR /app

# POSTGRES system dependencies
# build-essential for psycopg2
# libpq-dev for pg_config
RUN apt-get update \
    && apt-get install -y \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# poetry configuration files
COPY pyproject.toml poetry.lock* /app/
RUN poetry install

# application code
COPY . .

# expose port and run
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

combine the services to be handled witha single command `docker-compose.production.yml`
```yml
services:
  app:
    build:
      context: .
      dockerfile: ./docker/Dockerfile.app
    env_file:
      - .env
    ports:
      - "${PORT-8000}:8000"
    volumes:
      - /var/log/app/:/logs/
  alembic:
    build:
      context: .
      dockerfile: ./docker/Dockerfile.alembic
    env_file:
      - .env
    command: ["poetry", "run", "alembic", "upgrade", "head"]
  db:
    image: postgres:15
    restart: always
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: database
    ports:
      - "5432:5432"
```

modify `alembic/env.py` to connect to postgres

```python
database_url = f"postgresql+psycopg2://{username}:{password}@{host}:{port}/{database}"

# this is the Alembic Config object, which provides
# access to the values within the .ini file in use.
config = context.config
config.set_main_option("sqlalchemy.url", database_url)
```

`docker compose up -f docker-compose.production.yml -d --build` run as daemon (in the background) & build to ensure latest changes
`docker compose down -f docker-compose.production.yml` kill the service

