Docker references
---
- [Container Linking Environment Variables](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/#environment-variables)
- [Docker commands](https://docs.docker.com/engine/reference/commandline/)
- [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
- [Why and How to Use Docker for Development](https://medium.com/iron-io-blog/why-and-how-to-use-docker-for-development-a156c1de3b24#.vmkg8o8jc)
- [Building good docker images](http://jonathan.bergknoff.com/journal/building-good-docker-images)
- [Protect the Docker daemon socket](https://docs.docker.com/engine/security/https/)

Docker recipes
---
- [Create And Deploy Spring-Based Java Application In Containers Using Docker](http://www.nirmata.com/2016/08/create-and-deploy-spring-based-java-application-in-containers-using-docker/)
- [Setting up a Docker container with Centos6 and Tomcat7](http://www.dev-garden.org/2014/12/27/setting-up-a-docker-container-with-centos6-and-tomcat7/)
- [9 Docker recipes for Java EE Applications](http://planet.jboss.org/post/9_docker_recipes_for_java_ee_applications_tech_tip_80)
- [WildFly/JavaEE7 and MySQL linked on two Docker containers](http://blog.arungupta.me/wildfly-javaee7-mysql-link-two-docker-container-techtip65/)
- [Deploy to WildFly and Docker from Eclipse](http://blog.arungupta.me/deploy-wildfly-docker-eclipse/)
- [Docker container linking across multiple hosts](http://blog.arungupta.me/docker-container-linking-across-multiple-hosts-techtip69/)
- [Build a Docker container for any app using Heroku Buildpacks](https://github.com/CenturyLinkLabs/building)
- [A Step By Step Guide For Dockerizing And Managing A Java App On 28 Different Application Stacks](https://www.linkedin.com/pulse/step-guide-dockerizing-managing-java-app-28-different-amjad-afanah)
- [Docker java](https://github.com/dchqinc/docker-java)
- [dchq-docker-java-solr-mongo-cassandra-example](https://github.com/dchqinc/dchq-docker-java-solr-mongo-cassandra-example)
- [docker-lamp](https://github.com/dchqinc/docker-lamp)
- [docker mean](https://github.com/meanjs/mean)
- [docker nodejs](https://github.com/dchqinc/docker-nodejs)
- [docker python](https://github.com/dchqinc/docker-python)
- [docker rails](https://github.com/dchqinc/docker-rails)
- [dchq-docker-java-example](https://github.com/dchqinc/dchq-docker-java-example)
- [Deploying NGINX and NGINX Plus with Docker](https://www.nginx.com/blog/deploying-nginx-nginx-plus-docker/)
- [Rails Development Environment for OS X Using Docker](https://allenan.com/docker-rails-dev-environment-for-osx/)
- [Running Docker Containers with Systemd](http://container-solutions.com/running-docker-containers-with-systemd/)
- [system docker](https://github.com/ibuildthecloud/systemd-docker)
- [Automatically start containers](https://docs.docker.com/engine/admin/host_integration/)
- [wait for start script](http://brunorocha.org/python/dealing-with-linked-containers-dependency-in-docker-compose.html)
- [Dockerfile generator](http://jrruethe.github.io/blog/2015/09/20/dockerfile-generator/)
- [tensorflow](https://github.com/somaticio/tensorflow.rb)
- [NAST cluster](http://nats.io/documentation/server/gnatsd-cluster/)
- [Open nsfw model](https://github.com/yahoo/open_nsfw)
- [Generate links that users can use to submit messages encrypted with your public key](https://github.com/whitesmith/hawkpost)

Docker images
---
- [Docker tomcat](https://github.com/tutumcloud/tomcat)
- [arungupta/javaee7-hol](https://github.com/arun-gupta/docker-images/blob/master/javaee7-hol/Dockerfile)
- [wildfly-mysql-javaee7](https://github.com/arun-gupta/docker-images/tree/master/wildfly-mysql-javaee7)
- [spring-boot-tomcat-mysql-app](https://github.com/meirwah/spring-boot-tomcat-mysql-app)
- [docker-compose-mysql-tomcat](https://github.com/witrdotnet/docker-compose-mysql-tomcat)
- [docker_centos_tomcat_mysql](https://github.com/bobdavisdev/docker_centos_tomcat_mysql)
- [Docker CentOs7 + Java 7 + Tomcat 8](https://github.com/kirillF/centos-tomcat)
- [Docker tomcat context example](https://github.com/ciandt-dev/docker-tomcat7-mysql)
- [Docker tomcat jdbc example](https://github.com/dmulligan/docker-example-tomcat-mysql)
- [Docker compose tomcat mysql example](https://github.com/dmulligan/docker-example-tomcat-mysql)
- [Minimal Docker tomcat](https://github.com/jeanblanchard/docker-tomcat)
- [bitnami tomcat](https://github.com/bitnami/bitnami-docker-tomcat)
- [mysql settings example](https://gist.github.com/feltnerm/bb6e23f531803896ca1e)
- [Continuous Deployment with Gradle and Docker example project](https://github.com/gesellix/pipeline-with-gradle-and-docker)
- [neural enhance](https://github.com/alexjc/neural-enhance)
- [lesspass](https://github.com/lesspass/lesspass)

Docker practice
- [docker_practice](https://github.com/yeasy/docker_practice)


Docker machine
---
```shell
docker-machine create
docker-machine start
docker-machine stop
```
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
# mount a directory from your virtual machine's file system into the container
docker run -d -P --name web -v /src/webapp:/opt/webapp:ro training/webapp python app.py
```
If you are using Docker Machine on Mac or Windows, your Docker daemon has only limited access to your OS X or Windows filesystem. Docker Machine tries to auto-share your **/Users** (OS X) or **C:\Users** (Windows) directory.

All other paths come from your virtual machine’s filesystem. For example, if you are using VirtualBox some other folder available for sharing, you need to do additional work. In the case of VirtualBox you need to make the host folder available as a shared folder in VirtualBox. Then, you can mount it using the Docker -v flag.
```shell
# OS X
docker run -v /Users/<path>:/<container path> ...
# on Windows
docker run -v /c/Users/<path>:/<container path> ...
```
Let’s create a new named container with a volume to share.
```shell
docker create -v /dbdata --name dbdata training/postgres /bin/true
```
You can then use the --volumes-from flag to mount the /dbdata volume in another container.
```shell
docker run -d --volumes-from dbdata --name db1 training/postgres
docker run -d --name db3 --volumes-from db1 training/postgres
```
To delete the volume from disk, you must explicitly call **docker rm -v** against the last container with a reference to the volume. 

[Top Level Volume Management](https://github.com/docker/docker/issues/14214)

Backup, restore, or migrate data volumes
```shell
docker run --volumes-from dbdata -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /dbdata
# to extract the data
docker run --volumes-from dbstore2 -v $(pwd):/backup ubuntu bash -c "cd /dbdata && tar xvf /backup/backup.tar"
```
Here we’ve launched a new container and mounted the volume from the dbdata container. We’ve then mounted a local host directory as /backup. Finally, we’ve passed a command that uses tar to backup the contents of the dbdata volume to a backup.tar file inside our /backup directory.

Docker host
---

```shell
#!/bin/bash
dev_docker_file="docker-compose.yml"

export localhost_ip="$(ifconfig en0 inet | grep "inet " | awk -F'[: ]+' '{ print $2 }')"

docker-compose -f $dev_docker_file build
docker-compose -f $dev_docker_file up
```

docker-compose.yml
```
version: '2'

services:
  nginx:
    image: nginx:latest
    ports:
      - 80:80
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
    extra_hosts:
      - "dockerhost:${localhost_ip}"
```

Build a Docker Image with Gradle
---
If you are using Gradle you need to add a new plugin like this:

`build.gradle`

```groovy
buildscript {
    ...
    dependencies {
        ...
        classpath('se.transmode.gradle:gradle-docker:1.2')
    }
}

group = 'springio'

...
apply plugin: 'docker'

task buildDocker(type: Docker, dependsOn: build) {
  push = true
  applicationName = jar.baseName
  dockerfile = file('src/main/docker/Dockerfile')
  doFirst {
    copy {
      from jar
      into stageDir
    }
  }
}
```

You can build a tagged docker image and then push it to a remote repository with Gradle in one command:
```
$ ./gradlew build buildDocker
```
