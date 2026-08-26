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
