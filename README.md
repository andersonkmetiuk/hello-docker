# Hello Docker

## First Docker project

<img src="assets/docker-logo.png" alt="docker-logo" style="padding: 10px" />

I'm following this tutorial: [Learn Docker in 7 Easy Steps - Fireship](https://www.youtube.com/watch?v=gAkwW2tuIqE). More details in this [site](https://fireship.io/lessons/docker-basics-tutorial-nodejs/).

## What is Docker?

A way to package software so it can run in any software.

### Dockerfile

Blueprint for building a Docker image.

### Images

A template for running Docker containers.

### Containers

A running process. For this example we have a Node application.

---

We need to have a server that is running the same version of Node and that has also installed this dependencies.

The whole point of Docker is to solve problems by reproducing environments so the applications can run without breaking.

Then, any developer can use this image to rebuild the environment. 

Images can be uploaded to the cloud in both private or public registries.

One image file can be used to spawn the same process multiple times in multiple environments. In this case tools like Kubernetes and Swarm come into play to scale containers to an infinite workload.

## Installation

[Docker Desktop](https://www.docker.com/products/docker-desktop/)

[Docker Extension in VSCode](https://code.visualstudio.com/docs/containers/overview)

## Dockerfile

```
FROM node:12

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

ENV PORT=8080

EXPOSE 8080

CMD [ "npm", "start" ]
```

## Docker Compose 
```
version: '3'
services:
  web:
    build: .
    ports:
      - "8080:8080"
  db:
    image: "mysql"
    environment: 
      MYSQL_ROOT_PASSWORD: password
    volumes:
      - db-data:/foo

volumes:
  db-data:

```

## Useful Commands

This command gives you a list of all the running containers on your system
```
docker ps
```



