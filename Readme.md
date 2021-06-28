# Docker and Kubernetes knowledge

This repo contains a lot of commands, good practices, guides and others to improve your docker skills and kubernetes usage.
The info will be spliced in two main sections for which index is the following:

1. Core concepts
2. Dockers 
3. Kubernetes

## Core concepts

### Virtualization vs Containers

The approach to use containers or virtualization is to replicate the same environment in different computes across the developers and environments of staging, develop and production. To made it possible, the first approach is to use virtualization which run's a new S.O over the main S.O, this will take a lot of resources which is reflected on a bad performance of the projects in production environments. By the other hand, containers will run a docker emulator and over there the containers will take their own configuration and deploy all the projects you want to run with a good performance and optimized resources from computer.


| Docker                                             | Virtualization                                       |
|----------------------------------------------------|------------------------------------------------------|
|Low impact on S.O, very fast, minimal usage of disk | Bigger impact on S.O, higher disk space usage, slower|
|share build or configuration is easy                | Share build or configuration could be a challenge    |
|Encapsulate apps and environments                   | Encapsulate whole machine                            |





```python
  a = 2
  print(a)
```