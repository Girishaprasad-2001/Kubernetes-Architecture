## Docker A to Z Commands with Real-Time Examples

This guide covers the most important Docker commands used by DevOps Engineers, SREs, Cloud Engineers, and Kubernetes Administrators.

1. Check Docker Version
```
docker version
```

Output:

```
Client:
2
Version: 24.0
3
 
4
Server:
5
Engine:
6
Version: 24.0
```

Used to verify Docker installation.

2. Check Docker Information
```
docker info
```

Shows:

Containers running
Images
Storage driver
Docker Root Directory
CPU and Memory

Real-time use: When troubleshooting Docker daemon issues.

3. Search Docker Images
```
docker search nginx
```

Output:

```
nginx
2
nginxinc/nginx-unprivileged
3
bitnami/nginx
```

Find images from Docker Hub.

4. Pull Image

Download image from Docker Hub.

```
docker pull nginx
2
 
```

Specific version:

```
docker pull nginx:1.25
```

Real-world: Before deploying Nginx container.

5. List Images
```
docker images
```

Output:

```
REPOSITORY TAG
2
nginx latest
3
ubuntu 22.04
```
6. Run Container
```
docker run nginx
```

Runs container.

Background mode:

```
docker run -d nginx
```

Options:

```
-d = detached mode
2
-it = interactive terminal
3
--name = custom container name
```

Example:

```
docker run -d --name web nginx
```
7. List Running Containers
```
docker ps
```

Output:

```
CONTAINER ID
2
STATUS
3
PORTS
```
8. List All Containers
```
docker ps -a
```

Shows:

Running
Stopped
Exited containers
9. Container Logs
Shell
1
docker logs web
```

Live logs:

```
docker logs -f web
2
 
```

Real-time use:

Application troubleshooting.

10. Execute Commands Inside Container
```
docker exec -it web bash
```

or

```
docker exec -it web sh
```

Check files:

```
ls
```
11. Inspect Container
```
docker inspect web
2
 
```

Shows:

IP Address
Mounts
Environment variables
Network settings

Extract IP:

```
docker inspect web | grep IPAddress
```
12. Stop Container
```
docker stop web
```

Graceful shutdown.

13. Start Container
```
docker start web
```
14. Restart Container
```
docker restart web
```
15. Kill Container

Immediate stop:

```
docker kill web
```
16. Remove Container
```
docker rm web
```

Force remove:

```
docker rm -f web
2
 
```
17. Remove Image
```
docker rmi nginx
```

Force:

```
docker rmi -f nginx
```
18. Remove All Stopped Containers
```
docker container prune
2
 
```
19. Remove Unused Images
```
docker image prune
```

All unused images:

```
docker image prune -a
```
20. Docker Port Mapping

Run nginx:

```
docker run -d -p 8080:80 nginx
```

Meaning:

```
Host Port = 8080
2
Container Port = 80
3
 
```

Access:

```
http://server-ip:8080
```
21. Environment Variables
```
docker run -e APP_ENV=prod nginx
2
`
```

Multiple:

```
docker run \
2
-e DB_HOST=mysql \
3
-e DB_PORT=3306 nginx
```
22. Volume Mounting

Host path:

```
docker run -d \
2
-v /data:/app/data nginx
```

Meaning:

```
Host ---> Container
2
/data ---> /app/data
```

Persistent storage.

23. Create Docker Volume
```

docker volume create mydata
```

List volumes:
```
docker volume ls
```

Inspect:

```
docker volume inspect mydata
```
24. Use Docker Volume
```
docker run -d \
2
-v mydata:/var/lib/mysql \
3
mysql
```

Real-world: Database persistence.

25. Docker Networks

List networks:

Shell
1
docker network ls
```

Default:

```
bridge
2
host
3
none
4
 
```
26. Create Custom Network
```
docker network create app-net
```

Run container:

```
docker run -d --network app-net nginx
```

Inspect:

```
docker network inspect app-net
```
27. Docker Copy Files

Copy Host → Container

```
docker cp index.html web:/usr/share/nginx/html
```

Container → Host

```
docker cp web:/tmp/test.log .
```
28. View Resource Usage
```
docker stats
2
 
```

Output:

```
CPU %
2
MEM %
3
NET I/O
```

Real-time monitoring.

29. View Processes in Container
```
docker top web
```
30. Docker Build Image

Dockerfile:

Dockerfile
1
FROM nginx
2
COPY index.html /usr/share/nginx/html
```

Build:

```
docker build -t my-nginx .
```
31. Tag Image
```
docker tag my-nginx myrepo/my-nginx:v1
```
32. Login to Docker Hub
```
docker login
```
33. Push Image
```
docker push myrepo/my-nginx:v1
```

Real-world CI/CD step.

34. Docker Save Image
```
docker save nginx > nginx.tar
```
35. Load Image
```
docker load < nginx.tar
2
 
```

Useful in offline environments.

36. Docker History
```
docker history nginx
```

Shows image layers.

37. Docker Events
```
docker events
```

Real-time event monitoring.

38. Docker System Usage
```

docker system df
```

Shows:

```
Images
2
Containers
3
Volumes
4
Cache
```
39. Clean Everything
```
docker system prune -a
```

Removes:

Unused images
Networks
Containers
Dockerfile Important Instructions
FROM

Base image.

Dockerfile
1
FROM ubuntu:22.04
```
WORKDIR
Dockerfile
1
WORKDIR /app
```
COPY
Dockerfile
1
COPY . .
2
 
```
ADD
Dockerfile
1
ADD app.tar.gz /app
```
RUN

Build-time command.

Dockerfile
1
RUN apt update
```
CMD

Default command.

Dockerfile
1
CMD ["nginx","-g","daemon off;"]
```
ENTRYPOINT

Main executable.

Dockerfile
1
ENTRYPOINT ["python"]
```
EXPOSE
Dockerfile
1
EXPOSE 8080
```
ENV
Dockerfile
1
ENV APP_ENV=prod
```
Real-Time Scenario

Deploy Nginx Website

Pull Image
```
docker pull nginx
```
Run Container
```
docker run -d \
2
--name website \
3
-p 80:80 nginx
```
Verify
```
docker ps
```
Check Logs
```
docker logs website
2
```
Access
```
http://server-ip
```
Update Website Content
```
docker cp index.html website:/usr/share/nginx/html
```
Restart Container
```
docker restart website
```
Top 15 Interview Commands
```
docker version
2
docker info
3
docker images
4
docker ps
5
docker ps -a
6
docker run
7
docker logs
8
docker exec
9
docker inspect
10
docker stop
11
docker rm
12
docker build
13
docker pull
14
docker push
15
docker network ls
16
docker volume ls
```
## 2-Minute Interview Answer

Docker is a containerization platform used to package applications and their dependencies into portable containers. Common commands include docker pull for downloading images, docker run for creating containers, docker ps for listing containers, docker logs for troubleshooting, docker exec for accessing running containers, docker build for creating custom images, and docker push for uploading images to registries. Docker volumes provide persistent storage, while Docker networks enable communication between containers. These commands are used daily in CI/CD, Kubernetes, and cloud-native environments.

# Dockerfile Complete Guide (Interview + Practical Knowledge)

A Dockerfile is a text file containing instructions to build a Docker Image.

Flow:
```
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
### Example:

Dockerfile
```
FROM ubuntu:22.04

RUN apt-get update

CMD ["echo","Hello World"]
```

Build:

```
docker build -t myimage .
```

Run:

```
docker run myimage
2
 
```
Dockerfile Structure

Most common instructions:

Dockerfile
```
FROM
LABEL
WORKDIR
COPY
ADD
RUN
ENV
ARG
EXPOSE
VOLUME
USER
ENTRYPOINT
CMD
HEALTHCHECK
```


Let's understand each one.

## 1. FROM

Defines the base image.

Dockerfile
```
1
FROM ubuntu:22.04
```

or

Dockerfile
```
1
FROM nginx:latest
```

Every Dockerfile must start with FROM.

Example
Dockerfile
```
1
FROM python:3.11
```

This image already contains:

```
Linux OS
+
Python 3.11
+
Required libraries
```
## 2. LABEL

Adds metadata to an image.

Dockerfile
```
LABEL maintainer="reddy@example.com"
2
LABEL version="1.0"
3
 
```

Check:

```
docker inspect image_name
```
Usage
Dockerfile
```
1
LABEL project="Ecommerce"
2
LABEL author="Reddy"
```
## 3. WORKDIR

Sets the working directory.

Without WORKDIR:

Dockerfile
```
1
RUN mkdir /app
2
RUN cd /app
```

This doesn't persist between layers.

Better:

Dockerfile
```
1
WORKDIR /app
```

Now all commands run from:

```
/app
```

Example:

Dockerfile
```
1
WORKDIR /application
2
 
```

### 4. COPY

Copies files from local machine to image.

Dockerfile
```
1
COPY source destination
```

Example:

Dockerfile
```
1
COPY app.py /app/
2
```

Copy everything:

Dockerfile
```
1
COPY . .
2
 
```
Workflow
```
Local Files
      ↓
   COPY
      ↓
Docker Image
```

## 5. ADD

Similar to COPY but with extra features.

Dockerfile
```
1
ADD file.tar.gz /app
```

Automatically extracts:

```
file.tar.gz
   ↓
Extracted
   ↓
/app
```

Can also use URLs.

Dockerfile
```
1
ADD https://example.com/file.txt /tmp/
```
Difference: COPY vs ADD
COPY
Dockerfile
```
1
COPY app.py /app
```

Only copies files.

ADD
Dockerfile
```
1
ADD app.tar.gz /app
```

Can:
Extract tar files
Download URLs
## Interview Answer

Use COPY whenever possible. ADD only when you need archive extraction or URL downloading.

### 6. RUN

Executes commands during image build.

Dockerfile
```
1
RUN apt-get update
2
 
```

Example:

Dockerfile
```
1
RUN apt-get update && \
2
apt-get install -y curl
```
Workflow
```
docker build
     ↓
RUN executes
     ↓
New Image Layer created

Every RUN creates a new layer.
```
### 7. ENV

Sets environment variables.

Dockerfile
```
1
ENV APP_ENV=production
2
```

Example:

Dockerfile
```
1
ENV JAVA_HOME=/usr/lib/jvm/java-17
```

Use:

Dockerfile
```
1
RUN echo $JAVA_HOME
```

Container uses:

```
echo $JAVA_HOME
```
## 8. ARG

Build-time variable.

Dockerfile
```
1
ARG VERSION=1.0
```

Build:

```
docker build --build-arg VERSION=2.0 .
```
Difference: ARG vs ENV

ARG:

Dockerfile
```
1
ARG VERSION
```
Available only during build.
Not available inside running container.

ENV:

Dockerfile
```
1
ENV VERSION=1.0
```
Available during build.
Available inside container.
## 9. EXPOSE

Documents container port.

Dockerfile
```
1
EXPOSE 80
```

Example:

Dockerfile
```
1
EXPOSE 8080
```

Does NOT publish port automatically.

Need:

```
docker run -p 8080:8080 image
```
Difference

EXPOSE:

Dockerfile
```
1
EXPOSE 80
2
 
```

Just documentation.

Port Mapping:
```
docker run -p 80:80 image
```

Actually opens the port.

10. VOLUME

Creates persistent storage.

Dockerfile
```
1
VOLUME ["/data"]
```

Example:

Dockerfile
```
1
VOLUME ["/var/lib/mysql"]
```
Workflow
```
Container
    ↓
Writes Data
    ↓
Volume
    ↓
Persists after Container Delete
```
### 11. USER

Specifies which user runs container.

Default:

```
root
```

Example:

Dockerfile
```
1
RUN useradd appuser
2
 
3
USER appuser
4
 
```

Security best practice:

Dockerfile
```
1
USER 1001
```
12. CMD

Default command executed when container starts.

Dockerfile
```
1
CMD ["nginx","-g","daemon off;"]
```

Example:

Dockerfile
```
1
CMD ["python","app.py"]
```

Override:

```
docker run image bash
```

CMD gets replaced.

## 13. ENTRYPOINT

Sets a fixed executable.

Dockerfile
```
1
ENTRYPOINT ["python"]
```

Run:

```
docker run image app.py
2
 
```

Docker executes:

```
python app.py
```
Difference: CMD vs ENTRYPOINT
CMD
Dockerfile
```
1
CMD ["echo","hello"]
```

Can be completely overridden.

```
docker run image ls
```

Output:

```
ls
```
ENTRYPOINT
Dockerfile
```
1
ENTRYPOINT ["echo"]
```

Run:

```
docker run image hello
```

Output:

```
hello
```

hello is appended.

Best Practice
Dockerfile
```
1
ENTRYPOINT ["python"]
2
CMD ["app.py"]
```

Run:

```
docker run image
2
 
```

Executes:

```
python app.py
```

Override CMD:

```
docker run image test.py
```

Executes:

```
python test.py
```
## 14. HEALTHCHECK

Checks container health.

Dockerfile
```
1
HEALTHCHECK CMD curl -f http://localhost || exit 1
```

States:

```
starting
2
healthy
3
unhealthy
```

Check:

```
docker ps
```

or

```
docker inspect container
```
Multi-Stage Build

Used to reduce image size.

Without Multi-stage
Dockerfile
1
FROM golang:1.22
2
 
3
COPY . .
4
 
5
RUN go build app
Show more lines

Image becomes large.

Multi-stage
Dockerfile
1
FROM golang:1.22 AS builder
2
 
3
WORKDIR /app
4
 
5
COPY . .
6
 
7
RUN go build -o app
8
 
9
FROM ubuntu:22.04
10
 
11
COPY --from=builder /app/app /app
12
 
13
CMD ["/app"]
```
Result
```
Build Tools Removed
2
Smaller Image
3
Better Security
```
Complete Python Dockerfile
Dockerfile
1
FROM python:3.11
2
 
3
LABEL author="Reddy"
4
 
5
WORKDIR /app
6
 
7
COPY requirements.txt .
8
 
9
RUN pip install -r requirements.txt
10
 
11
COPY . .
12
 
13
ENV APP_ENV=production
14
 
15
EXPOSE 5000
16
 
17
CMD ["python","app.py"]
```
Docker Build Process Internally
```
Dockerfile
   ↓
FROM
   ↓
Layer 1

RUN
   ↓
Layer 2

COPY
   ↓
Layer 3

ENV
   ↓
Layer 4

CMD
   ↓
Final Image

```

Docker caches layers.

If only source code changes:

```
FROM Cached
2
RUN Cached
3
COPY Rebuilt
```

This makes builds faster.

Most Important Dockerfile Differences (Interview)
COPY vs ADD
```
COPY = Only copies files
2
ADD = Copies + Extracts tar + Downloads URLs
```
RUN vs CMD
```
RUN = Executes during image build
2
 
3
CMD = Executes when container starts
```
CMD vs ENTRYPOINT
```
CMD = Default command, can be replaced
2
 
3
ENTRYPOINT = Fixed executable, arguments appended
```
ARG vs ENV
```
ARG = Build-time variable
2
 
3
ENV = Runtime + Build-time variable
```
EXPOSE vs -p
```
EXPOSE = Documentation
2
 
3
-p = Actually publishes port
```
## Interview Answer (2-Minute Version)

A Dockerfile is a set of instructions used to build a Docker image. Common instructions include FROM (base image), COPY/ADD (copy files), RUN (execute build commands), WORKDIR (set working directory), ENV and ARG (variables), EXPOSE (document ports), VOLUME (persistent storage), USER (container user), CMD (default startup command), ENTRYPOINT (fixed executable), and HEALTHCHECK (health monitoring). Docker builds images layer by layer, and each instruction creates a new layer, enabling caching and faster builds.

## Common Interview Questions
CMD vs ENTRYPOINT

CMD

Provides default command/arguments
Can be overridden by docker run

ENTRYPOINT

Defines the main executable
Runs every time container starts
Can only be overridden using --entrypoint

## Best Answer for Interview

Use ENTRYPOINT to define the main application that the container should always run and use CMD to provide default arguments. Prefer the exec form (["command","arg"]) instead of shell form for proper signal handling and Kubernetes compatibility. A common best practice is to combine them, where ENTRYPOINT defines the executable and CMD supplies default parameters that can be overridden at runtime.
## Complete Enterprise Example
```
# Stage 1 - Compile
FROM maven:3.9-eclipse-temurin-17 AS compile

WORKDIR /app

COPY pom.xml .
COPY src ./src

RUN mvn compile


# Stage 2 - Testing
FROM compile AS testing

RUN mvn test


# Stage 3 - Build Package
FROM compile AS build

RUN mvn clean package -DskipTests


# Stage 4 - Runtime
FROM eclipse-temurin:17-jre AS runner

WORKDIR /app

COPY --from=build /app/target/*.jar app.jar

ENTRYPOINT ["java","-jar","app.jar"]
```

## For a Java application, a Docker multi-stage build can have dedicated stages such as:

AS build → Download dependencies
AS compile → Compile source code
AS testing → Run unit tests
AS builder → Package JAR/WAR
AS runner → Lightweight runtime image

Example: Maven-based Spring Boot Application

```
# -----------------------------
# Stage 1: Build Dependencies
# -----------------------------
FROM maven:3.9.8-eclipse-temurin-17 AS build

WORKDIR /app

COPY pom.xml .

# Download dependencies
RUN mvn dependency:go-offline

# -----------------------------
# Stage 2: Compile Source Code
# -----------------------------
FROM build AS compile

COPY src ./src

RUN mvn clean compile

# -----------------------------
# Stage 3: Run Unit Tests
# -----------------------------
FROM compile AS testing

RUN mvn test

# -----------------------------
# Stage 4: Package Application
# -----------------------------
FROM testing AS builder

RUN mvn package -DskipTests

# -----------------------------
# Stage 5: Runtime Image
# -----------------------------
FROM eclipse-temurin:17-jre AS runner

WORKDIR /app

COPY --from=builder /app/target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
```
## Workflow
Stage 1: build
Dockerfile
```
1
FROM maven:3.9.8-eclipse-temurin-17 AS build
2
 
```

Purpose:

Download Maven dependencies
Cache dependencies
Speeds up future builds
Dockerfile
```
1
COPY pom.xml .
2
RUN mvn dependency:go-offline
```
Stage 2: compile
Dockerfile
```
1
FROM build AS compile
```

Purpose:

Compile Java code
Dockerfile
```
1
COPY src ./src
2
RUN mvn clean compile
```

Generated:

```
target/classes
2
 
```
Stage 3: testing
Dockerfile
```
1
FROM compile AS testing
2
 
```

Purpose:

Run JUnit tests
Validate application before packaging
Dockerfile
```
1
RUN mvn test
```

If tests fail:

```
Docker build fails
```
Stage 4: builder
Dockerfile
```
1
FROM testing AS builder
```

Purpose:

Create final JAR/WAR
Dockerfile
```
1
RUN mvn package -DskipTests
```

Output:

```
target/myapp.jar
```
Stage 5: runner
Dockerfile
```
1
FROM eclipse-temurin:17-jre AS runner
```

Purpose:

Minimal runtime image
No Maven
No source code
No compiler
Dockerfile
```
1
COPY --from=builder /app/target/*.jar app.jar
```

Run application:

Dockerfile
```
1
ENTRYPOINT ["java","-jar","app.jar"]
```
Why Use Multiple Stages?
Single-Stage Build
```
JDK
Maven
Source Code
Tests
Dependencies
Application
```

Image Size:

```
800MB+
```
Multi-Stage Build
```
JRE
Application JAR
```
Image Size:

```
150MB-250MB
```

Benefits:

Smaller image
Faster deployment
Better security
Reduced attack surface
Cleaner production container
Build and Run

Build image:

```
docker build -t springboot-app:v1 .
```

Run container:

```
docker run -d -p 8080:8080 springboot-app:v1
```

Verify:

```
docker ps
```
```
curl http://localhost:8080
```
### Interview Answer

In a Java Docker multi-stage build, build downloads dependencies, compile compiles the source code, testing runs unit tests, builder packages the application into a JAR/WAR, and runner contains only the JRE and packaged artifact. The final stage copies the generated JAR from the builder stage, resulting in a smaller, more secure production image.
