---
title: "Docker cheatsheet"
date: 2021-10-28T14:29:24-04:00
draft: false
---

### Common commands


|  Commands  |  Description  |
| ---------- | ------------- |
| docker ps [-a] | list (all) containers |
| docker run | start a container |
| docker stop | stop a container |
| docker rm | remove a container |
| docker rmi | remove image |
| docker images | list all images | 

### More on docker run:

`-d` run the container in a detached mode. You will still be able to use the console

`-t` run with a pseudo terminal

`docker run -p 80:5000 name` map the docker host's port to the containers port so that it can be accessed from outside the docker host

### Others:

`docker build Dockerfile -t image-name` build an image

`docker attach name/id of container` to reattach the container

`docker logs name/id of container` show logs

`docker inspect name/id of container` inspect the properties of a running container

`docker exec -u 0 -it container_name /bin/bash` get into container as root user

`docker kill $(docker ps -q) && docker rm $(docker ps -a -q)` stop and remove all running containers

`docker rmi $(docker images -q)` remove all images


