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
