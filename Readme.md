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

### Docker Cheat Sheet
| Main Command           |                Sub Command   |                  Comment           |
|------------------------|------------------------------|----------------------------------- |
| docker run             | -it                          | Use an interactive terminal (i, interactive)(t, for tty) (input output)        |
|                        | -ot                          |                                    |
|                        | build-image-tag              | Run the cmd in docker file for a image (not accessible by port) (Attached)
|                        | -p "3000:80" build-image-tag | Expose from the local to internal port
|                        |-p "3000:80" -d build-image-tag| Works detached
| docker attach          |        container-id          | Attach the console to container |
| docker build           |  .                           | Build the image in the same folder |
| docker ps              |                              | Watch all running containers       |
|                        |    -a                        | Watch all containers               |
| docker stop            |      container-name          | stop container by the name        |
| docker start           | container-id or name         | start a stopped container, this does not block the terminal (Detached)|
|                        | -a container-id or name      | start a stopped container, this does not block the terminal (Detached)|
|                        | -a -i container-id/name      | Allow to interact with the terminal|
| docker logs            | container-id                 | watch the logs from the container |
|                        | -f container-id              | watch the logs attached to the container |


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