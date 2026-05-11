# Complete Docker Notes

# Table of Contents

1. Introduction to Docker
2. Why Docker is Used
3. Docker Architecture
4. Dockerfile
5. Docker Images
6. Docker Containers
7. CMD vs ENTRYPOINT
8. Common Dockerfile Instructions
9. Docker Commands
10. Docker Compose
11. docker-compose.yml
12. Docker Networking
13. Docker Volumes
14. Port Mapping
15. SQL Database Connection in Docker
16. Docker Problems and Solutions
17. Docker vs Virtual Machine
18. Best Practices
19. Complete Mental Model

---

# 1. Introduction to Docker

Docker is a containerization platform used to package applications along with:

* source code
* runtime
* dependencies
* configurations
* system libraries

into a portable unit called a container.

Docker helps applications run consistently across:

* local machines
* servers
* cloud platforms
* production environments

---

# 2. Why Docker is Used

Without Docker:

```text
Developer Machine
    ↓
Manual setup required
```

Problems:

* dependency conflicts
* version mismatch
* operating system differences
* “works on my machine” issue

With Docker:

```text
Application + Dependencies + Runtime
```

Everything is packaged together.

Benefits:

* consistent environments
* easier deployment
* scalable applications
* simplified CI/CD
* isolation between applications
* portability

---

# 3. Docker Architecture

## High-Level Architecture

```text
User
  ↓
Docker CLI
  ↓
Docker Daemon
  ↓
Docker Engine
  ↓
Images / Containers / Networks / Volumes
  ↓
Host Operating System
  ↓
Hardware
```

---

## Docker CLI

Commands used by developers:

```bash
docker build
docker run
docker ps
```

CLI sends requests to Docker Daemon.

---

## Docker Daemon

Background service responsible for:

* building images
* running containers
* managing networking
* managing storage
* downloading images

---

## Docker Image

A Docker image is a read-only packaged blueprint.

Contains:

* application code
* dependencies
* runtime
* libraries
* startup instructions

---

## Docker Container

A container is a running instance of a Docker image.

Image = blueprint
Container = running application

---

## Docker Registry

Stores Docker images.

Examples:

* Docker Hub
* private registries

---

# 4. Dockerfile

A Dockerfile is a text file containing instructions for building Docker images.

Example:

```dockerfile
FROM node:20

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

---

## Docker Build Process

```text
Dockerfile
   ↓
docker build
   ↓
Docker Image
   ↓
docker run
   ↓
Container
```

---

# 5. Docker Images

Docker images contain:

* source code
* dependencies
* runtime environment
* startup commands
* system packages

Images are built using:

```bash
docker build -t myapp .
```

---

## Image Layers

Docker images are layered.

Example:

```text
Linux Layer
Node.js Layer
Dependencies Layer
Application Layer
```

Benefits:

* caching
* faster builds
* reusable layers

---

# 6. Docker Containers

Containers are isolated runtime environments.

They contain:

* isolated filesystem
* isolated process space
* isolated networking
* environment variables

Containers share the host OS kernel.

---

## Container Lifecycle

```bash
docker run myapp
docker stop container
docker start container
docker rm container
```

---

# 7. CMD vs ENTRYPOINT

## CMD

Defines default command.

Example:

```dockerfile
CMD ["npm", "start"]
```

Can be overridden.

---

## ENTRYPOINT

Defines fixed executable.

Example:

```dockerfile
ENTRYPOINT ["python"]
```

Usually combined with CMD.

---

## Combined Example

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Final command:

```text
python app.py
```

---

## Difference

| CMD                | ENTRYPOINT         |
| ------------------ | ------------------ |
| Default command    | Fixed executable   |
| Easily overridden  | Harder to override |
| Provides arguments | Defines executable |

---

# 8. Common Dockerfile Instructions

## FROM

Defines base image.

```dockerfile
FROM node:20
```

---

## WORKDIR

Sets working directory.

```dockerfile
WORKDIR /app
```

---

## COPY

Copies files into image.

```dockerfile
COPY . .
```

---

## RUN

Executes command during build.

```dockerfile
RUN npm install
```

---

## CMD

Default startup command.

```dockerfile
CMD ["npm", "start"]
```

---

## ENTRYPOINT

Defines executable.

```dockerfile
ENTRYPOINT ["python"]
```

---

## EXPOSE

Documents container port.

```dockerfile
EXPOSE 3000
```

---

## ENV

Sets environment variables.

```dockerfile
ENV NODE_ENV=production
```

---

## VOLUME

Creates persistent storage mount point.

```dockerfile
VOLUME /data
```

---

# 9. Important Docker Commands

## Build Image

```bash
docker build -t myapp .
```

---

## Run Container

```bash
docker run myapp
```

---

## Run with Port Mapping

```bash
docker run -p 3000:3000 myapp
```

---

## Run in Background

```bash
docker run -d myapp
```

---

## View Containers

```bash
docker ps
```

---

## Stop Container

```bash
docker stop container_name
```

---

## Remove Container

```bash
docker rm container_name
```

---

## Remove Image

```bash
docker rmi image_name
```

---

## View Logs

```bash
docker logs container_name
```

---

## Enter Container

```bash
docker exec -it container_name sh
```

---

## Docker Compose Commands

```bash
docker compose up
docker compose down
docker compose logs
```

---

# 10. Docker Compose

Docker Compose manages multiple containers together.

Used for:

* frontend
* backend
* database
* cache
* queues

---

## Why Compose is Needed

Without Compose:

```bash
docker run mongodb
docker run backend
docker run frontend
```

Manual setup becomes difficult.

---

## With Compose

```bash
docker compose up
```

Starts complete application stack.

---

# 11. docker-compose.yml

Main configuration file for Docker Compose.

Example:

```yaml
services:

  backend:
    build: ./backend
    ports:
      - "8080:8080"
    environment:
      DB_HOST: mysql
      DB_PORT: 3306
    depends_on:
      - mysql

  mysql:
    image: mysql
    ports:
      - "3307:3306"
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```

---

## Important Sections

### services

Defines containers.

---

### build

Builds image using Dockerfile.

---

### image

Uses existing Docker image.

---

### ports

Maps host and container ports.

---

### environment

Defines environment variables.

---

### depends_on

Controls startup order.

---

### volumes

Defines persistent storage.

---

# 12. Docker Networking

Docker automatically creates internal networks.

Containers communicate using service names.

Example:

```text
backend → mysql
```

Backend connects using:

```text
mysql:3306
```

NOT:

```text
localhost:3306
```

because localhost inside container refers to itself.

---

## Network Commands

```bash
docker network ls
docker network inspect network_name
```

---

# 13. Docker Volumes

Volumes provide persistent storage outside container lifecycle.

---

## Why Volumes Are Needed

Without volumes:

```text
Container deleted
   ↓
Data deleted
```

With volumes:

```text
Container deleted
   ↓
Volume survives
   ↓
Data survives
```

---

## Create Volume

```bash
docker volume create myvolume
```

---

## Use Volume

```bash
docker run -v myvolume:/data mongo
```

---

## Docker Compose Volume

```yaml
services:
  mysql:
    image: mysql
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```

---

# 14. Port Mapping

Port mapping exposes container ports to host machine.

Example:

```bash
docker run -p 8080:3000 myapp
```

Meaning:

```text
Host Port 8080
      ↓
Container Port 3000
```

---

## Multiple Ports

```bash
docker run -p 8080:8080 -p 9090:9090 myapp
```

---

## Port Conflict

Problem:

```text
Port already allocated
```

Solution:

```yaml
ports:
  - "3307:3306"
```

---

# 15. SQL Database Connection in Docker

Best practice:

* backend in one container
* database in another container

---

## Example Architecture

```text
Frontend
   ↓
Backend
   ↓
MySQL
```

---

## Example Compose

```yaml
services:

  backend:
    build: .
    environment:
      DB_HOST: mysql
      DB_PORT: 3306

  mysql:
    image: mysql
```

---

## Backend Connection String

### Spring Boot

```properties
spring.datasource.url=jdbc:mysql://mysql:3306/mydb
```

---

## Important Concept

Use:

```text
mysql
```

NOT:

```text
localhost
```

inside containers.

---

# 16. Docker Problems and Solutions

## Port Conflicts

### Problem

Same host port already used.

### Solution

Use different host port.

```yaml
ports:
  - "3307:3306"
```

---

## Memory Issues

### Problem

Container crashes with OOM.

### Solution

```bash
docker run -m 512m myapp
```

Monitor:

```bash
docker stats
```

---

## Container Networking Problems

### Problem

Containers cannot communicate.

### Solution

Use service names instead of localhost.

---

## Large Image Sizes

### Solution 1

Use Alpine images.

```dockerfile
FROM node:20-alpine
```

---

### Solution 2

Use `.dockerignore`.

```text
node_modules
.git
.env
```

---

### Solution 3

Use multi-stage builds.

---

## Database Persistence Problems

### Solution

Use Docker volumes.

```yaml
volumes:
  - mysql_data:/var/lib/mysql
```

---

## Slow Docker Builds

### Bad

```dockerfile
COPY . .
RUN npm install
```

---

### Better

```dockerfile
COPY package*.json ./
RUN npm install
COPY . .
```

Uses Docker layer caching.

---

# 17. Docker vs Virtual Machine

## Virtual Machine

```text
Hardware
↓
Host OS
↓
Hypervisor
↓
Guest OS
↓
Application
```

Each VM contains full operating system.

---

## Docker

```text
Hardware
↓
Host OS
↓
Docker Engine
↓
Containers
↓
Applications
```

Containers share host OS kernel.

---

## Difference

| Virtual Machine | Docker        |
| --------------- | ------------- |
| Full OS         | Shared kernel |
| Heavy           | Lightweight   |
| Slower          | Fast startup  |
| Large storage   | Small images  |

---

# 18. Best Practices

## Use Specific Versions

```dockerfile
FROM node:20.11
```

Avoid:

```dockerfile
FROM latest
```

---

## Use Alpine Images

```dockerfile
FROM node:20-alpine
```

---

## Use Multi-Stage Builds

Smaller and secure images.

---

## Use .dockerignore

Avoid unnecessary files.

---

## One Process Per Container

Examples:

* backend container
* database container
* redis container

---

## Use Volumes for Databases

Prevent data loss.

---

## Use Docker Compose

Simplifies multi-container applications.

---

# 19. Complete Mental Model

## Dockerfile

```text
Recipe for creating image
```

---

## Docker Image

```text
Packaged application blueprint
```

---

## Docker Container

```text
Running isolated application
```

---

## Docker Compose

```text
Manages complete multi-container application
```

---

## Docker Volume

```text
Persistent storage outside container lifecycle
```

---

# Final Summary

Docker helps package applications with:

* code
* dependencies
* runtime
* configurations

into portable containers.

Main Docker concepts:

```text
Dockerfile → Image → Container
```

Modern applications commonly use:

* Docker Compose
* Volumes
* Networks
* Port Mapping
* Multi-container architecture

Docker solves:

* dependency conflicts
* environment inconsistency
* deployment complexity
* scalability problems

and is one of the most important technologies in modern software development.
