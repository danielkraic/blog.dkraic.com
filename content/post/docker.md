+++
date = "2015-10-19T22:50:24+02:00"
title = "Docker"
tags = [ "dev", "docker", "devops" ]
+++

## Docker

### About

Docker allows you to package an application with all of its dependencies into a standardized unit for software development.

Docker containers wrap up a piece of software in a complete filesystem that contains everything it needs to run: code, runtime, system tools, system libraries – anything you can install on a server. This guarantees that it will always run the same, regardless of the environment it is running in.

##### Docker is lightweight
Containers running on a single machine all share the same operating system kernel so they start instantly and make more efficient use of RAM. Images are constructed from layered filesystems so they can share common files, making disk usage and image downloads much more efficient.

##### Docker is secure

Containers isolate applications from each other and the underlying infrastructure while providing an added layer of protection for the application.

#### Docker vs VM

How is this different from virtual machines?
Containers have similar resource isolation and allocation benefits as virtual machines but a different architectural approach allows them to be much more portable and efficient.

##### Virtual Machines
Each virtual machine includes the application, the necessary binaries and libraries and an entire guest operating system - all of which may be tens of GBs in size.

##### Containers
Containers include the application and all of its dependencies, but share the kernel with other containers. They run as an isolated process in userspace on the host operating system. Docker uses resource isolation features of the Linux kernel such as cgroups and kernel namespaces to allow independent "containers" to run within a single Linux instance, avoiding the overhead of starting and maintaining virtual machines. They’re also not tied to any specific infrastructure – Docker containers run on any computer, on any infrastructure and in any cloud.

### Build -> ship -> run

Build – build docker container application and all of its dependencies
Ship – push container to public/private docker repository
Run – pull container from repository a run it


### Examples

#### Create

```bash
docker create -i -t --name my_fedora fedora:22 bash
```
* -i  Interactive mode (keeps the STDIN open for interaction with the container)
* -t  Allocates a pseudo-TTY
* --name  Name of the container. If not specified, docker will add a hash string as a name.
* fedora:22  Image to be instantiated.
* bash  The command (process) to be run inside the container.

```bash
docker start -a -i my_fedora
```
* -a  Attach to the pseudo-TTY
* -i  Interactive mode (keeps the STDIN open for interaction with the container)
* --name  Name of the container.


#### Create + Start = Run

```bash
docker run hello-world
docker run docker/whalesay cowsay 'hello form docker'
docker run maryatdocker/docker-whale
# Interactive
docker run -i -t --name my_fedora fedora:22 /bin/bash
# Deamon
docker run -d --name MySQL mysql:5.7 /opt/mysql/mysqld
```

#### Expose ports

To expose a specific port use the -p switch. The port 80 inside the container is mapped to the outside as 8080.
```bash
docker run -d -p 8080:80 --name web nginx
```
To prevent conflicts between multiple containers, the -P switch tells docker to map ports inside containers to random ports outside the container.
```bash
docker run -d -P --name web nginx
```

#### Attach data

Exposing /var/www as /usr/share/nginx/html inside the container, which is where nginx stores static content in default setting.
:ro  Read only permission
```bash
docker run --name web -p 80:80 -d -v /var/www:/usr/share/nginx/html:ro nginx
```

#### Control containers

```bash
docker ps
docker ps -a
docker ps -a --filter 'exited=0'
docker stop my_fedora
docker kill my_fedora
docker rm my_fedora
```

#### Control images

```bash
# default repository: hub.docker.com
docker search nginx
docker pull nginx
docker pull debian:squeeze
docker pull 172.16.0.1/username/image_name[:tag]
docker images
docker rmi -f nginx
```

### Nginx static website

```bash
echo 'hello from docker' > index.html
docker run --name bal-nginx -p 8080:80 -v `pwd`:/usr/share/nginx/html:ro -d nginx
docker run --name web -p 80:80 -d -v /var/www:/usr/share/nginx/html:ro,Z nginx
lynx http://localhost:8080
```

#### Inspect containers

```bash
docker inspect CONTAINER|IMAGE
docker inspect CONTAINER | grep IPAddress
docker logs CONTAINER
docker top my_fedora
```

#### ENV

```bash
docker run -i -t -e VAR1=val1 -e VAR2=val2 fedora:22 bash
```

#### Foreman Demo
```bash
docker run -p 3333:3000 -i -t lzap/fedora-foreman-git-stable:1.3.1
# http://localhost:3333 (admin/changeme)
docker run -p 3333:3000 -i -t -entrypoint='/bin/bash' lzap/fedora-foreman-git-stable:1.3.1
```

#### Link containers

```bash
docker run --name DB -e MYSQL_ROOT_PASSWORD=admin -e MYSQL_DATABASE=admin -d mysql:5.7
docker run -p 8080:80 -e WORDPRESS_DB_PASSWORD=admin -d --name WP --link DB:mysql wordpress
http://localhost:8080
```

#### Docker Compose

Tool for defining and running multi-container applications with Docker:

- define Dockerfile
- define services that make up your app in docker-compose.yml
- run docker-compose up

### Cheatsheet

https://github.com/wsargent/docker-cheat-sheet

## Commands

### Containers

#### Lifecycle

| Command |Description|
| ------------- |-------------|
|docker create | creates a container but does not start it|
|docker run | creates and starts a container in one operation|
|docker stop | stops it|
|docker start | will start it again|
|docker restart | restarts a container|
|docker rm | deletes a container|
|docker kill | sends a SIGKILL to a container|
|docker attach | will connect to a running container|
|docker wait | blocks until container stops|

#### Info

| Command |Description|
| ------------- |-------------|
|docker ps | shows running containers|
|docker logs | gets logs from container|
|docker inspect | looks at all the info on a container (including IP address)|
|docker events | gets events from container|
|docker port | shows public facing port of container|
|docker top | shows running processes in container|
|docker stats | shows containers' resource usage statistics|
|docker diff | shows changed files in the container's FS|

### Images

#### Lifecycle

| Command |Description|
| ------------- |-------------|
|docker images | shows all images|
|docker import | creates an image from a tarball|
|docker build | creates image from Dockerfile|
|docker commit | creates image from a container|
|docker rmi | removes an image|
|docker load | loads an image from a tar archive as STDIN, including images an tags (as of 0.7)|
|docker save | saves an image to a tar archive stream to STDOUT with all parent layers, tags & versions (as of 0.7)|

### Info

| Command |Description|
| ------------- |-------------|
|docker history | shows history of image|
|docker tag | tags an image to a name (local or registry)|

### Registry & Repository

| Command |Description|
| ------------- |-------------|
|docker login | to login to a registry|
|docker search | searches registry for image|
|docker pull | pulls an image from registry to local machine|
|docker push | pushes an image to the registry from local machine|
