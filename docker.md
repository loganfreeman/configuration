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
- docker logs - Shows us the standard output of a container.
- docker stop - Stops running containers.
