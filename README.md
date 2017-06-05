## myaccount Docker setup for local development

### Start here...

https://docs.docker.com/docker-for-mac/

... to install Docker and to get an overview of available features and commands. You might want to remove a previous Docker installation on your machine, if it exists, as Docker would run native on your Mac now, which makes it run much faster.

### Then read the documentation for Docker Compose

https://docs.docker.com/compose/overview/

### Launching a sample application in docker

       $ git clone git@gitlab.int.slingtv.com:account/frontend.git
       $ cd frontend
       $ docker rm $(docker ps -a -q)      # make sure there are no old containers
       $ docker rmi $(docker images -a -q) # make sure there are no old images
       $ docker-compose build              # optional
       $ docker-compose up

The `docker-compose up` command would do the following, based on instructions in `docker-compose.yml`:
 - build images for all pieces of myaccount application, if they didn't exist already,
 - create a Docker network called `myaccount_default`,
 - spin up containers based on the images,
 - link all containers together.


Once you're done with your work, stop your containers with `docker-compose stop`. Or just delete them all with `docker-compose down`. Containers are disposable and fast to rebuild. See some useful aliases at the end fo this readme for more cleanup commands.

#### Default configuration

`docker-compose up` brings up the following containers:

- nginx
- webpack
- mock

#### Using mock service to simulate API responses

A mock service is running in the `mock` container that should be accessible to both, your application, as `mock`, and externally, on `localhost`, on port 4000. A sample `api.raml` file is provided. Once your containers are up, point the browser to `http://localhost:4000/starships/` in order to see it. Modify the `docker-compose.yml` file to point the mock service to the actual location of your raml file. You do that by mapping its local relative path to `/raml/api.raml` in the container, i.e.

```
...
    volumes:
      - ../api-contracts/my_account.raml:/raml/api.raml
...
```

Then make sure to rebuild your containers again with `docker-compose build`.

The mock service replaces external uri with `localhost:4000` in baseUri, so `https://api.slingtv.com/v1/account/subscription` would become accessble on `http://localhost:4000/v1/account/subscription`.


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

Now open it in your browser

http://localhost:8080


#### List and delete images

      $ docker images
      $ docker rmi -f <image_id>
      $ docker rmi $(docker images -a -q)

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
        alias drmi-all='docker rmi $(docker images -a -q)'
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


