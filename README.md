## myaccount Docker setup for local development

### Start here...

https://docs.docker.com/docker-for-mac/

... to install Docker and to get an overview of available features and commands. You might want to remove a previous Docker installation on your machine, if it exists, as Docker would run native on your Mac now, which makes it run much faster.

### Then read the documentation for Docker Compose

https://docs.docker.com/compose/overview/

### Launching a sample application in docker

       $ git clone git@gitlab.int.slingtv.com:account/frontend.git
       $ cd frontend
       $ docker-compose build nginx    # optional
       $ docker-compose build webpack  # optional
       $ docker-compose up

The `docker-compose up` command would do the following, based on instructions in `docker-compose.yml`:
 - build images for all pieces of myaccount application, if they didn't exist already,
 - create a Docker network called `myaccount_default`,
 - spin up containers based on the images,
 - link all containers together.


#### Default configuration

`docker-compose up` brings up the following containers:

- nginx
- webpack

#### Running with redis and postgres containers included

      $ docker-compose -f docker-compose-with-postgres-redis.yml up

...would build and bring up the following containers:

- nginx
- webpack
- postgres
- redis


### Useful commands

#### If you need to rebuild a particular container, i.e. webmock, then you could do the following:

      $ docker rm -f webmock            # removes webmock container
      $ docker rmi -f myaccount/webmock # removes webmock image
      $ docker-compose build webmock    # rebuilds webmock image
      $ docker-compose up
      # ...or in detached mode
      $ docker-compose up -d

#### List and delete images

      $ docker images
      $ docker rmi -f <image_id>

#### List containers, both running and stopped, and restart them:

      $ docker ps -a
      $ docker-compose start <container_label>

#### Access a running container's shell

      $ docker exec -it <container_label> /bin/sh

#### Spin up a new container from an image and access its shell (the container will be removed once you exit)

      $ docker run --rm -it myaccount/<container_label> /bin/sh

### List of aliases you might want to create in your ~/.bash_profile

        alias cdock='cd ~/workspace/myaccount/docker'
        alias cdapi='cd ~/workspace/myaccount/sling-api'
        alias cdui='cd ~/workspace/myaccount'
        alias di='docker images'
        alias dlsv='docker volume ls -f dangling=true'
        alias drmi='docker rmi -f'
        alias drm='docker rm -f'
        alias drm-all='docker rm $(docker ps -a -q)'
        alias drmn='docker network rm'
        alias drmv='docker volume rm $(docker volume ls -qf dangling=true)'
        alias dps='docker ps -a'
        alias dcp='docker cp'
        alias dstop='docker stop'
        alias dc='docker-compose'


### Docker Cheat Sheet

https://github.com/wsargent/docker-cheat-sheet


