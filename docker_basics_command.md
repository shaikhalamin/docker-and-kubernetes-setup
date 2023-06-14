```javascript

//show all created docker images
docker images

//remove docker images
docker rmi {id}

//show all running stopped container
docker ps

//stop specific container
docker container stop {container_id}

//build image from terminal using Dockerfile
docker build . -t shaikh/test-docker

//create container from terminal
docker run -p 8080:3000 shaikh/test-docker

//create docker network before build
docker network create nest-api-net

//build images abd container from docker-compose file
docker-compose up --build

//docker compose build with volume mount
docker-compose up --build -V

//stop all container build from docker-compose
docker-compose down

//stop all container detach volume
docker-compose down -v

//ssh to docker container from direct image like mysql
docker exec -it db40fd416dd5 bash

//ssh to docker container from build image with workdir
docker exec -it db40fd416dd5 sh

//excecute command on service level
docker-compose run --rm php8_service php bin/console doctrine:database:create

//docker inspect a specific container
docker inspect nest_database_container

//check container log
docker logs nest_database_container -f

//docker inspect a network
docker network inspect nest-api-net

// docker remove all images at onece (be aware)
docker rmi -f $(docker images -a -q)

// launch multiple with single command
docker-compose up -d --scale nest_app=10

//when working on local machine and editing not working for docker container then run below command
sudo chown -R shaikhalamin laravel-microservice-admin-rmqp/

```
