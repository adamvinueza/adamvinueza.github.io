---
layout: post
title:  "Docker in five minutes"
date:   2020-01-10 11:23:59 -0700
category: docker learning
description: "You have to start working with Docker ASAP. The Docker docs are
pretty good, but maybe you need something a bit more practical, i.e., fast? Here
it is."
---
## What is Docker?

[Docker](https://www.docker.com) allows you to run your software inside a thing
called a container that can be built according to a recipe you can specify
(called a "Dockerfile"), and that will run on any machine that runs Docker
itself.

## Why is Docker useful?

Software that runs in a Docker container can be run by anyone running Docker,
and will generally consume fewer resources. This makes it ideal for automating
builds and deployments, and for running software in cloud environments, where
cost depends on resource usage.

## How do I use Docker?

Docker runs _containers_ in which you can run your software. [A container is
created from an _image_](https://docs.docker.com/engine/docker-overview/#docker-objects),
which is basically a series of instructions for creating a small virtual
environment--OS, shell commands, etc. You can build an individual container to
hold your program, but will probably create other containers to hold your
program's dependencies; for example, if your program uses a MySql database,
you'll want to run MySql inside its own container.  Containers can communicate
with one another in the same way that different programs commicate with one
another--via the same protocols and over the same ports.

Docker has a repository of pre-built images for general use, called [Docker
Hub](https://hub.docker.com). These are images of things like standard operating
systems (e.g., Ubuntu Linux), commonly used resources such as MySql and Redis,
and operating systems with programming environments pre-installed and
configured, such as NodeJS and Golang. Generally you install your programs on
one of the OS images, or those with OS-and-programming-environments, and at
runtime connect those programs to resources on other containers.

Docker is generally run from a shell, using the command line. These are the
probably the most commonly run Docker CLI (command-line interface) commands:

#### docker pull
Pulls an image from Docker Hub.

#### docker build
Creates an image from a Dockerfile.

#### docker run
Creates a container from an image and starts it.

#### docker ps
Lists the containers currently running.

#### docker exec
Provides access to a running container.

#### docker stop
Stops a running container, gracefully.

#### docker kill
Stops a running container, gracelessly.

#### docker commit
If you have made changes to a container, this will create an image from it.

#### docker push
Pushes an image to Docker Hub, or to a private repository you specify.

#### docker images
Lists the images stored on your machine.

#### docker rm
Deletes a (stopped) container.

#### docker rmi
Deletes a stored image.

#### docker system df
Provides the disk usage taken up by your Docker environment.

#### docker system prune
Removes unused Docker resources (containers and/or images).
To get the basic usage of these commands, you can generally type
```
docker help [command]
```
and get useful information.

## Wait, that's all your going to say about the commands?

I could explain them in detail, but that would take a lot longer than five
minutes, and you're much better off just starting to use Docker and figuring
stuff out.

## Can I debug my code in a Docker container?
In general, the answer is: Yes! The details will vary depending on the debugging
tool you use, your development environment, and the programming language.
Sometimes it will be easy, and sometimes it will be a pain.
