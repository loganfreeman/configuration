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
