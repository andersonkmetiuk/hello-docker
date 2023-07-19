# Hello Docker

<img src="assets/docker-logo.png" alt="docker-logo" style="padding: 10px" />

I'm following this tutorial: [Learn Docker in 7 Easy Steps - Fireship](https://www.youtube.com/watch?v=gAkwW2tuIqE). More details in this [site](https://fireship.io/lessons/docker-basics-tutorial-nodejs/).

## What is Docker?

> A way to package software so it can run in any hardware.

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

### FROM
We could start from scratch using "FROM Ubuntu". But for this case we would need to install Nodejs manually. 

As we have an officially supported nodejs image we are going to use it.

### WORKDIR
App source code. Like when you "cd" into a directory.

Usually it starts from this /app directory.

### COPY
2 arguments:
 - Local package.json location
 - Place where we want to copy inside the Container (current work directory)

### RUN
Used to run commands.

### COPY 
Now that we have our modules in the image we can then copy over our source code. Which we'll do by coping over all of our local files to the current working directory. But this generates a problem because we already have the "node_modules" in our local filesystem that will also be copied over to the image and override the modules that we installed there. So we need to have a ".dockerignore" to ignore those files.

### ENV
Used to set an environment variable to run our code. When we have a running container we also want it to be listening on port 8080. So we have this code inside the "index.js" file.
```
const port = process.env.PORT || 8080;
```
This way we can access the nodejs express app publicly

### CMD
There can only be one of these per Dockerfile! And this tells the container how to run the actual application. Which it does by starting a process to serve the express app. 

You will also notice that unlike run we've made this an array of strings. This is known as exec form and it's the preferred way to do things. Unlike a regular command it doesn't start a shell session.

## Image
For now we will run by using the docker build and the tag (-t) to give it a name so we can remember this easily to access it later.

Usually the tag name comes with your username on DockerHub and the name of the image like so

```
docker build -t YOUR_USERNAME/demoapp:1.0 .
```

The "period" at the end indicates the current working directory.

When this builds you will notice that it will have an id so we can use it to create other images or to run containers.

You can push it to a container registry so you can store it there. You can use DockerHub or any cloud provider. You would use "docker push" for that. And when you want to use it again you use the "docker pull" command.

## Container
To run the Container you use the command
 ```
 docker run image_id
 ```
 Note that this will host you container locally to the "localhost:8080". But if we open the browser and go to that address we don't see anything.

This happened because we exposed our 8080 port in our Dockerfile but by default it's not accessible to the outside world. So we need to use the "p" flag to implement port forwarding to the Docker Container to our local machine
```
docker run -p 5000:8080 <image-id>
```
For this example we used the port "5000". So this reads like this 
```
Local_Port : Container_Port
    5000   :       8080
```
So if we go to localhost:5000 we can se the app running there.

## Keep in mind 
The Docker Container will still be running even after we closed the terminal window. So you need to open the dashboard and stop the container.

When you stop the container any state or data that you created inside of it will be lost.

To store this data you can use Volumes. This way you can share data through multiple containers.

### Volume
Is a dedicated folder on the host machine. And inside this folder a container can create files that can be remounted into future containers or multiple containers at the same time!

To create a volume we use
```
docker volume create folder_name
```
When we create it when can mount this in our container when we run it.

Multiple containers can mount this simultaneously and access the same set of files. And the files stick around after all the containers are shutdown.

```
docker run \
--mount source=folder_name, target=/stuff
```

## Debugging
Inside the Docker Desktop you can see all the logs there. You can also run commands using the CLI button.

Another way to do it it's using the command line using
```
docker exec
```
This way you will be in the root of the file system of that container so you can use any Linux commands.

You should always think about writing simple and maintainable micro services for each container. The best way to to this is to remember that **a Container should only run 1 Process at a time.**

So if your app need **multiple processes** you should use **multiple containers!**

For that we have a tool called **Docker Compose.**

## Docker Compose
Create a "docker-compose.yml" file in the root of the project.
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
In this example "web" is for the nodejs app and "db" is for a database that you might want to use as well.

Here you can use "volume" to store the data as well.

To run it you just need
```
docker-compose up
```
This way both containers will run at the same time.

To end you can run
```
docker-compose down
```
This way you will shutdown all the containers together.

## Useful Commands

This command gives you a list of all the running containers on your system
```
docker ps
```



