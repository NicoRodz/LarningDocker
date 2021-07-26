# Docker and Kubernetes knowledge

This repo contains a lot of commands, good practices, guides and others to improve your docker skills and kubernetes usage.
The info will be spliced in two main sections for which index is the following:

1. Core concepts
2. Dockers 
3. Kubernetes

Also, it includes all snippets and plugins used to improve the quality of your configurations like.
1. Docker
2. Yaml

docker command --help show all options for each command

## Core concepts

### Virtualization vs Containers

The approach to use containers or virtualization is to replicate the same environment in different computes across the developers and environments of staging, develop and production. To made it possible, the first approach is to use virtualization which run's a new S.O over the main S.O, this will take a lot of resources which is reflected on a bad performance of the projects in production environments. By the other hand, containers will run a docker emulator and over there the containers will take their own configuration and deploy all the projects you want to run with a good performance and optimized resources from computer.

How docker runs over the S.O: 
1. Your operating S.0
2. OS Built-in / Emulated Container Support
3. Docker Engine
4. A lot of containers running

Docker image is an specification and not running piece of code
Docer container is an running piece of code based on an image


| Docker                                             | Virtualization                                       |
|----------------------------------------------------|------------------------------------------------------|
|Low impact on S.O, very fast, minimal usage of disk | Bigger impact on S.O, higher disk space usage, slower|
|share build or configuration is easy                | Share build or configuration could be a challenge    |
|Encapsulate apps and environments                   | Encapsulate whole machine                            |

## Docker

The main artifacts to understand what is docker, are the images and containers. Containers run a unit of software while the images are the template for them, these `images contains the code, required tools and runtime` to run, if you want, multiple containers with that image.

### Dockerfile
Contains the instruction for docker that will be executed when we build the image

The tags contains two parts, the name and the image version. This could be defined on local machine or be fetched from the docker hub repository. A combination of these is a unique identifier

### Docker Cheat Sheet
| Main Command           |                Sub Command   |                  Comment           |
|------------------------|------------------------------|----------------------------------- |
| docker run             | -it                          | Use an interactive terminal (i, interactive)(t, for tty) (input output)        |
|                        | -ot                          |                                    |
|                        | build-image-tag              | Run the cmd in docker file for a image (not accessible by port) (Attached)
|                        | -p "3000:80" build-image-tag | Expose from the local to internal port
|                        |-p "3000:80" -d build-image-tag| Works detached
|                        |-p "3000:80" -d --rm build-image-tag| rm will remove the container when it stop
|                        |-p "3000:80" -d --rm --name demoapp build-image-tag| will add a specific name
|                        |-p "3000:80" -d --rm --name demoapp app-name:tag | this will use the image previously created in your local machine
|                        |all+commands + -v volume-name:/path/to/folder | This will add a volume in specified folder
| docker attach          |        container-id          | Attach the console to container |
| docker image           | replace image for images     | Fetch images in your computer |
|                        | inspect image-id             | Look for the image definition |
|                        | prune -a                     | Remove all images |
| docker build           |  .                           | Build the image in the same folder |
| docker build           |  -t app-name:tag .           | Build the image with name and tag |
| docker ps              |                              | Watch all running containers       |
|                        |    -a                        | Watch all containers               |
| docker stop            |      container-name          | stop container by the name        |
| docker start           | container-id or name         | start a stopped container, this does not block the terminal (Detached)|
|                        | -a container-id or name      | start a stopped container, this does not block the terminal (Detached)|
|                        | -a -i container-id/name      | Allow to interact with the terminal|
| docker logs            | container-id                 | watch the logs from the container |
| docker rm              | separed containers name      | Remove all containers in the list
| docker rmi             | separed images id            | Remove all images in the list
| docker cp              | local_folder/. container_name:/test| dot for all content, you can use exclusive file, then the name of the container and the folder
|                        | container_name:/test local_folder  | Do the reverse copy, from the container to the local folder


## Push and Pull Images
| Main Command           |                Sub Command   |                  Comment           |
|------------------------|------------------------------|----------------------------------- |
| docker push            | slaas/slaas-image-name:tag   | slaas is the id for docker account
| docker tag             | old_name:tag slaas/slaas-image-name:tag | Re-tag already created image 
| docker pull            | slaas/slaas-image-name:tag   | Download the image from this repository
| docker run             | slaas/slaas-image-name:tag

```python
  FROM node # First will look up for the image in local, then from the internet
  WORKDIR /app # This tells to docker to run all commands in this folder (npm install for example)
  # COPY . /app # Copy the folder at the Dockerfile path (without docker file), the second "." is the path in the image directory
  COPY . ./ # In this case, /app is not necessary since WORKDIR define the root path for image
  RUN npm install
  EXPOSE 80 # Required to connect your local machine to the isolated docker image
  # RUN node server.js #Never do this, the Dockerfile is to compile the docker image
  CMD ["node", "server.js"] # This will not be executed when the server is created, only when a container is started based on the image
```
(i) We will start the runtime if we create a container, not at the build of image

## Docker layers
When you make the Dockerfile, each command is identified by docker as a layer. As that, if you see the previous definition you will notice that the project is copied before to install the requirements of the project, so for each change that could be done in the source code, the repository will require a new build for all the layers. A good solution for this problem is the following

```python
FROM node
WORKDIR /app

COPY package.json /app  # This will generate a new layer with only the packages in .json file
RUN npm install # So if you change a file in source code, this will not be modified. 
# Info: This only will be reloaded if you made a change in package.json

COPY . /app
EXPOSE 80
CMD ["node", "server.js"]
```
(i) Check out in the console, when you run `docker build .` there are a lot of packages which uses "cache"


# Volumes

There exist two ways to store data in containers. One is store data inside of the container which will live in the container storage system (Not accessible from your local machine), and this data will be lost if the container is deleted. An important note here is if you stop the container and run it again, this data will persist in the container file system.
Another way with a long term for storage is to use a volume, which will create a folder in your file system and store the data in this folder. This folder could be shared between all containers which want to use this volume.

- If you add a volume to container, this volume will not be removed if the container is deleted.

About the external storage, there exist two kinds of storages. 

- Volumes (Managed by docker)
- Bind mounts (Managed by you)

One way to use volumes in Docker is include it in Dockerfile like `VOLUME ["app/volume"]`, but this is not persisted and can not be shared with other apps, to do that you need to create a named volume.
- Include in the docker run command the following: -v volume-name:/path/to/folder

This volume will not be deleted when the container is down

| Main Command           |                Sub Command   |                  Comment           |
|------------------------|------------------------------|------------------------------------|
| docker volume          | --help                       | To get all the options for volumes |
|                        | ls                           | To get all created volumes         |
|                        | rm volume-id                 | Remove volume manually             |
|                        | inspect volume-name          | Get more info about the volume     |


Note: If you crate an antonymous volume without --rm tag, this volume will persist in memory and you will need to remove it manually

## Bind Volumes
The main difference between docker volumes and bind volumes is we don't know where is the folder that uses the docker volume, but we can specify the folder for a bind volume

To include a bind mount volume you need to explicit define the absolute path, not the relative path to the folder in which you want to mount the volume in the local machine, and point it to the folder in docker container:
Include in the run command: `-v $(pwd):/app``

!important note: This bind volume with the  respective configuration will allow the code to be fresh in the containers with the local changes, but this doesn't handle the reload for the "node server" for example, you will need to include  nodemon or something like that, a package which update the changes in files and update the server.
If you do that, remember to replace the CMD in Dockerfile with something like ["npm", "start"] and include it in scripts from package.json file

## In resume for volumes
| Command           |        Comment                          |
|-------------------|-----------------------------------------|
| docker run - app/data                   | Anonymous volume  |
| docker run -v volume:app/data           | Named volume      |
| docker run -v /path/to/code:app/code    | Bind mount        |

If in the bind volume command you write /somepath:/app:ro, this latest ro tells to docker to use a read only volume 

# Docker ignore file

.dockerignore is a file which will allow you to avoid copy files or folders with COPY command inside of Dockerfile. For example, to avoid copy nodemodules file makes sense because this file could be not updated with the latest changes. This will avoid to copy files from your local storage to the resource files in container.

In addition, you can add `.git, Dockerfile, .gitignore` and othersa

# Environment variables

Docker accept arguments an environment values in runtime which could be set with:
- docker build --build--arg
- docker build --env   or simply -e

You can add environment variables in dockerfile with `ENV PORT 80`for example, this will register PORT varibale so you can do also an `EXPOSE $PORT` command

You can use a `.env` variable with all the environment variables and use --env-file .env command to include it int docker run execution.

!important: Remember to include this file in .dockerignore file

Arguments must be defined in dockerfile with `ARG DEFAULT_PORT = 80` for example, and can be used as an argument only in the docker file, with `ENV PORT = $DEFAULT_PORT`.
`docker build -t feedback-node:dev --build-arg DEFAULT_PORT=80 .`

!important: Put this configuration before the layers which will not be modified by this change, otherwise the layer configuration from docker will reload and recompile all the code in these bad handled layers 

# Containers and networks

## From container to the local machine
To connect from the container to the local machine network (for example, an instance of database), we could use `host.docker.internal` url which will be interpreted by the docker with our local machine ip.
```
mongodb://host.docker.internal:27017/dbname
http://host.docker.internal:PORT
```

## From container to container
As example, you can pull a mongodb image from dockerhub and create a new container based on this image, this image will expose a brand new database with ports and all the configuration, imagine you call this container as `mongodb`. After that, you can do `docker container inspect mongod` and get the networkSettings. 

## Networks and containers

You can create a network which contains a lot of containers with `--network my-net`, this allow the containers to communicate between them.

Before you use a network, you need to create this with `docker network create my-net`, also you can use `docker network ls` to see all created networks 

In addition, if you define a name for container in the same network of the other container, you can use the name for this container to make request to a "static" ip

## Data Persistance

If you include an instance of mongo db with `docker run mongodb` and all required parameters, this data stored in this database will be lost if the container is destroyed. So to persist this information you can simply add a named volume with the path which is specified in the official documentation and persist your data instead if you destroy the container.
In particular, this image allows you to use username and password from environment variables

# Docker compose

Docker compose is a tool that allow you to replace `docker build, run` command with one orchestration file and commands. You can use a lot of builds or run commands inside one dockercompose configuration.

Docker compose NOT replace Dockerimage, these work together and handle multiple containers in the same machine. Docker compose comes to replace all the commands that you need to execute from the console to assign networks, environments, etc.

[file versions resource](docs.docker.com/compose/compose-file)
```yaml
version: "2" #File version
  services:
    mongodb:
      # container_name: mongodb # not required
      image: 'mongo'
      volumes:
        - data:/data/db
      #environment:
      #  MONGO_SECRET: secret
      env_file:
        - ./env/mongo.env
      #networks: this network is completely optional.
      #  - goals-net
    backend:
      ports:
        - '3000:80' #Host port : Container internal port
      # build: ./backend # relative path to know where is the dockerfile
      build:
        context: ./backend
        dockerfile: Dockerfile-dev
        args:
          some-arg: some-value
      volumes:
        - logs:/app/logs
        - ./backend:/app
        - /app/node_modules
      env_file:
        - ./env/backend.env
      depends_on:
        - mongodb
    frontend:
      build: ./frontend
      ports:
        - '3000:3000'
      volumes:
        - ./frontend/src:/app/src
      stdin_open: true
      tty: true
      # this combination replace the -it command option required by frontend to work property
      depends_on:
        - backend
volumes:  #Anonymous volume and bind volumes don't need to be specified here
  data:
  logs:
```

All these services are working in the same network created automatically by docker, but also you can include an another network for each service 
In a docker-compose file you can also define a ENTRYPOINT command, which will overload the previous defined ENTRYPOINT in the image

```sh
docker-compose up
docker-compose up --build # force rebuild for all images and containers
docker-compose build # rebuild missed images and not started containers 
docker-compose up -d # to start detached 
docker-compose down # this doesn't delete volumes
docker-compose down -v # to remove volumes
docker-compose run --rm app-name  # To run only this app from docker compose file
docker-compose run -d app1name app2name # This only run specified apps in the command
```

# Utility containers

with `docker run -it node` we can use an interactive terminal with node running time, this could be useful to prepare others containers with images which belongs from complex applications which needs some configuration in addition. 

we can do:

```bash
docker run -it -d node 
dockere exec container_name npm init
dockere exec -it container_name npm init
```

Since this is a Utility container, it's good idea to use a lighted image for 'node'. For example, you can use
```Dockerfile
FROM node:14-alphine

WORKDIR /app

# CMD npm init
ENTRYPOINT ["npm"] # this will allow to run container and include, for example, init command
```
Then use
```
docker build -t npm-util .
docker run npm-util npm init
```

## With docker compose approach
```yaml
version: "3.8"
services:
  npm:
    build: ./
    stdin_open: true
    tty: true
    volumes:
      - ./:/app
```
 Then we can execute the following command: `docker-compose run npm init` which makes npm services run and give a parameter required by node 

# Docker permissions errors
When using Docker on Linux, you might face permission errors when adding a bind mount as shown bellow, you can fix this as the following:

```Dockerfile
FROM php:7.4-fpm-alpine
 
WORKDIR /var/www/html
 
COPY src .
 
RUN docker-php-ext-install pdo pdo_mysql
 
RUN addgroup -g 1000 laravel && adduser -G laravel -g laravel -s /bin/sh -D laravel
 
USER laravel
```
```Dockerfile
FROM composer:latest
 
RUN addgroup -g 1000 laravel && adduser -G laravel -g laravel -s /bin/sh -D laravel
 
USER laravel
 
WORKDIR /var/www/html
 
ENTRYPOINT [ "composer", "--ignore-platform-reqs" ]
```

This User instruction replace CHOWN commands. These steps should ensure that all files which are created by the Composer container are assigned to a user named "laravel" which exists in all containers which have to work on the files.

Also see this Q&A thread: https://www.udemy.com/course/docker-kubernetes-the-practical-guide/learn/#questions/12986850/

# AWS CLI
To make it work's, you need to instal the cli in your computer and include the credentials to authenticate your machine with aws. You can do that with `aws configure` command or configuring it from the file manually.

```bash
file: ~/.aws/credentials

[default]
aws_access_key_id=
aws_secret_access_key=
```

After that, maybe you want to use an specific available cluster, so you can use the following commands:
```bash
~ aws eks list-clusters
{
    "clusters": [
        "cluster1",
        "cluster"
    ]
}

~ aws eks update-kubeconfig --name cluster1
~ aws eks update-kubeconfig --name cluster2
```
This configuration will allow you to use `kubectl` commands from your computer
# Terraform

terraform apply -var-file var-file.json


# SOPS 
sops -e -k arn:aws:kms:us-east-2:.... test.yaml  (encrypt)