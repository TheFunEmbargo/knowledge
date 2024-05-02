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
 
## Concepts

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
