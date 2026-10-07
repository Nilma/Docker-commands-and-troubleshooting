# Docker Learning Repository

A practical collection of Docker examples, commands, and troubleshooting
notes for learning and teaching Docker.

The repository focuses on using Docker from the terminal and
understanding the workflow from **source code → image → container →
Docker Hub**.

## What's Included

### Docker Commands & Troubleshooting Guide

The main reference is:

**[Docker Commands & Troubleshooting
Guide](Docker-Commands-and-Troubleshooting-Guide.md)**

It covers:

-   Docker images and containers
-   Essential terminal commands
-   Building images with a `Dockerfile`
-   Port mapping
-   Docker Hub: login, tag, push, and pull
-   Common image-tagging and push problems
-   Volumes and persistent data
-   Running MySQL with Docker
-   Docker Compose
-   `.env` files
-   Common errors and troubleshooting
-   Docker cleanup commands
-   Quick command cheat sheet


**[Docker Troubleshooting Guide For Windows
Guide](Docker-Troubleshooting-Guide-Windows.md)**

It covers:

-   Troubleshooting the Windows Hypervisor
-   Enabling the required Windows virtualization features
-   Configuring Hyper-V, Virtual Machine Platform, and Windows Hypervisor Platform
-   Configuring Windows Subsystem for Linux
-   Switching Docker Desktop to Docker VMM
-   Creating the docker-users group
-   Adding your Windows user to docker-users
-   Checking docker-users group membership
-   Restarting Windows after configuration changes
-   A quick Docker troubleshooting checklist


## Quick Start

Check Docker:

``` bash
docker --version
```

Build an image:

``` bash
docker build -t my-app:1.0 .
```

Run a container:

``` bash
docker run -p 5173:5173 my-app:1.0
```

See running containers:

``` bash
docker ps
```

Stop a container:

``` bash
docker stop CONTAINER_NAME
```

## Docker Hub Example

Build the image using your Docker Hub username:

``` bash
docker build -t username/my-app:1.0 .
```

Login:

``` bash
docker login
```

Push:

``` bash
docker push username/my-app:1.0
```

Another user can then run:

``` bash
docker pull username/my-app:1.0
docker run -p 5173:5173 username/my-app:1.0
```

## Docker Compose

For applications with several services:

``` bash
docker compose up --build
```

Stop the system:

``` bash
docker compose down
```

## Learning Path

A suggested order is:

1.  Understand **image vs. container**
2.  Run an existing Docker image
3.  Create a `Dockerfile`
4.  Build your own image
5.  Run and inspect containers
6.  Work with ports and volumes
7.  Push an image to Docker Hub
8.  Run a database in Docker
9.  Use Docker Compose for multiple services
10. Practice troubleshooting

## Rule of Thumb

> **Dockerfile** = how to build an image\
> **Image** = packaged application\
> **Container** = running image\
> **Docker Hub** = share images\
> **Volume** = persistent data\
> **Docker Compose** = run multiple services together

## Requirements

-   Docker Desktop or Docker Engine
-   Terminal
-   Docker Hub account if you want to push images

------------------------------------------------------------------------

## Copyright

Copyright © 2026 Nilma Abbas & Mark Svendstrup.

This material is provided for educational purposes.  
You may use and adapt it for teaching and learning with appropriate attribution.

Commercial redistribution or publication without permission is not permitted.
