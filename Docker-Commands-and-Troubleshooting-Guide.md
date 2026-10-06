# Docker Commands & Troubleshooting Guide

A practical Docker reference for development, teaching, and
troubleshooting.

------------------------------------------------------------------------

## 1. Docker in One Minute

Docker packages an application and its dependencies into an **image**.

An **image** is used to create a **container**, which is a running
instance of that image.

``` text
Dockerfile
    ↓
docker build
    ↓
Docker image
    ↓
docker run
    ↓
Container
```

A useful rule of thumb:

> **Dockerfile builds an image. Docker runs containers. Docker Compose
> runs systems of containers.**

------------------------------------------------------------------------

## 2. Check Docker

Check that Docker is installed:

``` bash
docker --version
```

Check that Docker is running:

``` bash
docker info
```

On macOS and Windows, make sure **Docker Desktop** is running.

------------------------------------------------------------------------

## 3. Essential Docker Commands

### Images

List images:

``` bash
docker images
```

Build an image:

``` bash
docker build -t image-name .
```

Build with a version tag:

``` bash
docker build -t image-name:1.0 .
```

Remove an image:

``` bash
docker rmi image-name
```

### Containers

Run a container:

``` bash
docker run image-name
```

Run it in the background:

``` bash
docker run -d image-name
```

List running containers:

``` bash
docker ps
```

List all containers, including stopped ones:

``` bash
docker ps -a
```

Stop a container:

``` bash
docker stop container-name
```

Start an existing container:

``` bash
docker start container-name
```

Remove a stopped container:

``` bash
docker rm container-name
```

View container logs:

``` bash
docker logs container-name
```

Follow logs continuously:

``` bash
docker logs -f container-name
```

Open a shell inside a running container:

``` bash
docker exec -it container-name sh
```

Some images include Bash:

``` bash
docker exec -it container-name bash
```

------------------------------------------------------------------------

## 4. Port Mapping

Containers have their own network environment.

To access an application from your computer, map a host port to a
container port:

``` bash
docker run -p 5173:5173 image-name
```

The syntax is:

``` text
-p HOST_PORT:CONTAINER_PORT
```

For example:

``` bash
docker run -p 8080:80 web-app
```

means:

``` text
localhost:8080 → container:80
```

------------------------------------------------------------------------

## 5. Dockerfile

A `Dockerfile` contains the instructions Docker uses to build an image.

Example for a Vite application:

``` dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5173

CMD ["npm", "run", "dev", "--", "--host", "0.0.0.0"]
```

Build it:

``` bash
docker build -t my-app:1.0 .
```

Run it:

``` bash
docker run -p 5173:5173 my-app:1.0
```

### Common Dockerfile instructions

  Instruction   Purpose
  ------------- -------------------------------------------------------
  `FROM`        Selects the base image

  `WORKDIR`     Sets the working directory

  `COPY`        Copies files into the image

  `RUN`         Executes a command while building

  `ENV`         Defines an environment variable

  `EXPOSE`      Documents the container port
  
  `CMD`         Defines the default command when the container starts

------------------------------------------------------------------------

## 6. `.dockerignore`

A `.dockerignore` file prevents unnecessary files from being copied into
the build context.

Example:

``` text
node_modules
.git
.gitignore
README.md
.env
npm-debug.log
```

This can make builds faster and images cleaner.

------------------------------------------------------------------------

## 7. Docker Hub

Docker Hub is a registry for Docker images.

### Login

``` bash
docker login
```

### Recommended workflow

Build the image using your Docker Hub username from the beginning:

``` bash
docker build -t username/lemonade-react:1.0 .
```

Push it:

``` bash
docker push username/lemonade-react:1.0
```

Another person can then pull it:

``` bash
docker pull username/lemonade-react:1.0
```

and run it:

``` bash
docker run -p 5173:5173 username/lemonade-react:1.0
```

### Use version tags

Prefer:

``` text
1.0
1.1
2.0
```

rather than relying only on:

``` text
latest
```

------------------------------------------------------------------------

## 8. Troubleshooting: Cannot Push an Image to Docker Hub

A common problem is building an image under a local name:

``` bash
docker build -t lemonade-react .
```

and then trying to push:

``` bash
docker push username/lemonade-react:1.0
```

Docker cannot push it because there is no local image with the exact
name:

``` text
username/lemonade-react:1.0
```

### Solution 1 --- Tag the existing image

Check your images:

``` bash
docker images
```

Then tag the image:

``` bash
docker tag lemonade-react:latest username/lemonade-react:1.0
```

Push it:

``` bash
docker push username/lemonade-react:1.0
```

You can also tag using an image ID:

``` bash
docker tag IMAGE_ID username/lemonade-react:1.0
```

### Solution 2 --- Better approach

Give the image the correct name when you build it:

``` bash
docker build -t username/lemonade-react:1.0 .
docker push username/lemonade-react:1.0
```

------------------------------------------------------------------------

## 9. Troubleshooting: Port Is Already Allocated

You may see an error similar to:

``` text
port is already allocated
```

Check running containers:

``` bash
docker ps
```

Stop the container using the port:

``` bash
docker stop container-name
```

Or use another host port:

``` bash
docker run -p 5174:5173 image-name
```

Now access:

``` text
http://localhost:5174
```

------------------------------------------------------------------------

## 10. Troubleshooting: Container Name Already Exists

If you run:

``` bash
docker run --name my-app image-name
```

and a container called `my-app` already exists, Docker will reject the
new container.

Check:

``` bash
docker ps -a
```

Remove the old container:

``` bash
docker rm my-app
```

Or choose another name:

``` bash
docker run --name my-app-2 image-name
```

------------------------------------------------------------------------

## 11. Troubleshooting: Code Changes Do Not Appear

A Docker image is a snapshot created at build time.

If you change your source code, the existing image does not
automatically change.

Rebuild it:

``` bash
docker build -t my-app:1.1 .
```

Then run the new version:

``` bash
docker run -p 5173:5173 my-app:1.1
```

For development workflows, bind mounts or Docker Compose can be used
when you want source changes available inside the container.

------------------------------------------------------------------------

## 12. Troubleshooting: Vite Runs but Cannot Be Opened

A Vite development server may bind only to localhost inside the
container.

Run Vite with:

``` bash
npm run dev -- --host 0.0.0.0
```

For a Dockerfile:

``` dockerfile
CMD ["npm", "run", "dev", "--", "--host", "0.0.0.0"]
```

Then map the port:

``` bash
docker run -p 5173:5173 image-name
```

------------------------------------------------------------------------

## 13. Troubleshooting: Docker Hub Permission Denied

First check that you are logged in:

``` bash
docker login
```

Then check the image name:

``` bash
docker images
```

The repository name normally needs your Docker Hub namespace:

``` text
username/image-name:tag
```

For example:

``` bash
docker build -t username/my-app:1.0 .
docker push username/my-app:1.0
```

------------------------------------------------------------------------

## 14. Volumes

Containers can be deleted. Data that must survive the container should
therefore be stored separately.

Create a volume:

``` bash
docker volume create my-data
```

List volumes:

``` bash
docker volume ls
```

Inspect a volume:

``` bash
docker volume inspect my-data
```

Remove a volume:

``` bash
docker volume rm my-data
```

Run a container with a volume:

``` bash
docker run -v my-data:/data image-name
```

------------------------------------------------------------------------

## 15. Run a Database with Docker

Docker can run databases directly from the terminal.

Example with MySQL:

``` bash
docker run --name mysql-demo \
  -e MYSQL_ROOT_PASSWORD=rootpass \
  -e MYSQL_DATABASE=schooldb \
  -p 3306:3306 \
  -v mysql-data:/var/lib/mysql \
  -d mysql:8
```

Check that it is running:

``` bash
docker ps
```

Enter MySQL:

``` bash
docker exec -it mysql-demo mysql -u root -p
```

Then use SQL normally:

``` sql
SHOW DATABASES;

USE schooldb;

CREATE TABLE students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);

INSERT INTO students (name, email)
VALUES ('Anna', 'anna@example.com');

SELECT * FROM students;
```

Exit MySQL:

``` sql
exit;
```

Stop the database:

``` bash
docker stop mysql-demo
```

Start it again:

``` bash
docker start mysql-demo
```

Because the example uses the `mysql-data` volume, the database data can
persist independently of the container.

------------------------------------------------------------------------

## 16. Docker Compose

Docker Compose is useful when an application contains multiple services.

For example:

``` text
React frontend
      ↓
Node API
      ↓
MySQL database
```

Example `docker-compose.yml`:

``` yaml
services:
  frontend:
    build: ./frontend
    ports:
      - "5173:5173"
    depends_on:
      - api

  api:
    build: ./api
    ports:
      - "3001:3001"
    depends_on:
      - db

  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
    volumes:
      - mysql-data:/var/lib/mysql

volumes:
  mysql-data:
```

Start everything:

``` bash
docker compose up
```

Build before starting:

``` bash
docker compose up --build
```

Run in the background:

``` bash
docker compose up -d
```

See the services:

``` bash
docker compose ps
```

View logs:

``` bash
docker compose logs
```

Stop and remove the Compose containers/network:

``` bash
docker compose down
```

Remove the Compose volumes too:

``` bash
docker compose down -v
```

Be careful with `-v` when the volumes contain database data you want to
keep.

------------------------------------------------------------------------

## 17. `.env` Files

Avoid placing passwords directly in `docker-compose.yml`.

Create a `.env` file:

``` text
MYSQL_ROOT_PASSWORD=rootpass
MYSQL_DATABASE=schooldb
```

Reference the variables:

``` yaml
environment:
  MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
  MYSQL_DATABASE: ${MYSQL_DATABASE}
```

Add `.env` to `.gitignore`:

``` text
.env
```

Do not commit real secrets to GitHub.

For teaching projects, an `.env.example` file can document the required
variables without containing real credentials:

``` text
MYSQL_ROOT_PASSWORD=change-me
MYSQL_DATABASE=schooldb
```

------------------------------------------------------------------------

## 18. Useful Inspection Commands

Inspect a container:

``` bash
docker inspect container-name
```

Inspect an image:

``` bash
docker inspect image-name
```

See resource usage:

``` bash
docker stats
```

See Docker disk usage:

``` bash
docker system df
```

See processes inside a container:

``` bash
docker top container-name
```

------------------------------------------------------------------------

## 19. Cleaning Up Docker

Remove stopped containers:

``` bash
docker container prune
```

Remove unused images:

``` bash
docker image prune
```

Remove unused networks:

``` bash
docker network prune
```

Remove unused volumes:

``` bash
docker volume prune
```

Remove unused Docker resources:

``` bash
docker system prune
```

More aggressive cleanup:

``` bash
docker system prune -a
```

Use cleanup commands carefully. In particular, do not remove volumes if
they contain data you need.

------------------------------------------------------------------------

## 20. Quick Command Cheat Sheet

  Goal                    Command
  ----------------------- ----------------------------------
  Check Docker            `docker --version`

  List images             `docker images`

  Build image             `docker build -t my-app .`

  Run container           `docker run my-app`

  Run with port           `docker run -p 5173:5173 my-app`

  Run in background       `docker run -d my-app`

  Running containers      `docker ps`

  All containers          `docker ps -a`

  Stop container          `docker stop NAME`

  Start container         `docker start NAME`

  Remove container        `docker rm NAME`

  Remove image            `docker rmi IMAGE`

  Logs                    `docker logs NAME`

  Enter container         `docker exec -it NAME sh`

  Docker Hub login        `docker login`
  
  Push image              `docker push username/image:tag`

  Pull image              `docker pull username/image:tag`

  List volumes            `docker volume ls`

  Compose start           `docker compose up`

  Compose build + start   `docker compose up --build`

  Compose stop            `docker compose down`

  Disk usage              `docker system df`

------------------------------------------------------------------------

## 21. Mental Model

Keep this flow in mind:

``` text
Source code
    ↓
Dockerfile
    ↓
docker build
    ↓
Image
    ↓
docker run
    ↓
Container
```

For sharing:

``` text
Image
  ↓
docker push
  ↓
Docker Hub
  ↓
docker pull
  ↓
Another computer
  ↓
Container
```

For multi-container applications:

``` text
Dockerfile(s)
     ↓
Images
     ↓
docker-compose.yml
     ↓
docker compose up
     ↓
Application system
```

------------------------------------------------------------------------

## Common Rule of Thumb

> **Dockerfile = how to build one image**\
> **Docker image = packaged application**\
> **Container = running image**\
> **Docker Hub = share images**\
> **Volume = persistent data**\
> **Docker Compose = run multiple services together**
