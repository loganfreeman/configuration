Docker can't connect to docker daemon
---
If you are running Docker on OSX, running the following eval
```shell
eval "$(docker-machine env default)"
```
If you'd prefer not to have to run this eval statement on every terminal session, you can add this to your bash_profile:
```shell
#Docker
eval "$(docker-machine env default)"
```

Docker basic
---
- docker ps - Lists containers.
- docker logs -f - Shows us the standard output of a container. -f acts like tail -f
- docker stop - Stops running containers.
- docker start - start the container
- docker rm - remove the container
- docker-machine ip my_vm_name - get the IP of the virtual host 
- docker port container_id - get the port mapping 
- docker top container_id - examine the running process 
- docker images - list images
- docker run -t -i ubuntu:14.04 /bin/bash - run an image
- docker pull centos - pull an image
- docker search sinatra - search for an image
- docker commit -m "Added json gem" -a "Kate Smith" 0b2616b0e5a8 ouruser/sinatra:v2 - create a new image
- docker build -t ouruser/sinatra:v2 . - build an image from a local Dockerfile
- docker tag 5db5f8471261 ouruser/sinatra:devel - set tag on an image
- docker rmi training/sinatra - remove an image not used anymore, not the same as removing a container that is based on an image
- docker exec -it your_container_name_or_id bash - open a shell to your container

Docker network
---
Docker Engine natively supports both bridge networks and overlay networks. A bridge network is limited to a single host running Docker Engine. An overlay network can include multiple hosts and is a more advanced topic.
```
docker network create -d bridge my-bridge-network
docker network ls
docker network inspect my-bridge-network
```
The **-d** flag tells Docker to use the bridge driver for the new network. You could have left this flag off as bridge is the default value for this flag.

You can add containers to a network when you first run a container.
Launch a container running a PostgreSQL database and pass it the --net=my-bridge-network flag to connect it to your new network:
```shell
 docker run -d --net=my-bridge-network --name db training/postgres
 docker inspect --format='{{json .NetworkSettings.Networks}}'  db
 # get the IP address 
 docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' web
 ```
Docker networking allows you to attach a container to as many networks as you like.
```shell
docker network connect my-bridge-network web
```
Data volumes
---
Data volumes are designed to persist data, independent of the container’s life cycle. Docker therefore never automatically delete volumes when you remove a container, nor will it “garbage collect” volumes that are no longer referenced by a container.

To add a data volume
```shell
docker run -d -P --name web -v /webapp training/webapp python app.py
# to set a volume readonly 
docker run -d -P --name web -v /opt/webapp:ro training/webapp python app.py
```
If you are using Docker Machine on Mac or Windows, your Docker daemon has only limited access to your OS X or Windows filesystem. Docker Machine tries to auto-share your **/Users** (OS X) or **C:\Users** (Windows) directory.
```shell
# OS X
docker run -v /Users/<path>:/<container path> ...
# on Windows
docker run -v /c/Users/<path>:/<container path> ...
```
