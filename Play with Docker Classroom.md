# Getting Started Walk-through for IT Pros and System Administrators

# Stage 1: The Basics
## First Alpine Linux Containers

### 1.0 Running your first container

```bash
docker container run hello-world
```

![alt text](image.png)

### 1.1 Docker Images

```bash
docker image pull alpine
```
![alt text](image-1.png)

```bash
docker image ls
```
![alt text](image-2.png)

### 1.1 Docker Container Run

```bash
docker container run alpine ls -l
```
![alt text](image-3.png)

```bash
docker container run alpine echo "hello from alpine"
```
![alt text](image-4.png)

```bash
docker container run alpine /bin/sh
```

![alt text](image-5.png)

```bash
docker container run -it alpine /bin/sh
```

![alt text](image-6.png)


```bash
docker container ls
```

![alt text](image-7.png)

```bash
docker container ls -a
```

![alt text](image-8.png)

### 1.2 Container Isolation

```bash
docker container run -it alpine /bin/ash
```

![alt text](image-9.png)

```bash
docker container run alpine ls
```

![alt text](image-10.png)

```bash
docker container ls -a
```

![alt text](image-11.png)

```bash
docker container start 81f
docker container start 684
```

```bash
docker container ls
```

![alt text](image-12.png)

```bash
docker container exec 81f ls
```

```bash
docker container exec 684 ls
```

![alt text](image-13.png)


## Doing More With Docker Images

### 1.0 Image creation from a container

```bash
docker container run -ti ubuntu bash

apt-get update
apt-get install -y figlet
figlet "hello docker"
```

![alt text](image-14.png)

```bash
docker container ls -a
```

![alt text](image-15.png)

```bash
docker container commit CONTAINER_ID
docker image ls
```

![alt text](image-16.png)


```bash

docker image tag 3c580260bacf ourfiglet
docker image ls
```

![alt text](image-17.png)

```bash
docker container run ourfiglet figlet hello
```

![alt text](image-18.png)


### 2.0 Image creation using a Dockerfile

```bash
docker image build -t hello:v0.1 .
```

![alt text](image-19.png)
![alt text](image-20.png)

```bash
docker container run hello:v0.1
```

![alt text](image-21.png)

### 3.0 Image layers

```bash
docker image ls
docker image history 2b
```

![alt text](image-22.png)


```bash
echo "console.log(\"this is v0.2\");" >> index.js
docker image build -t hello:v0.2 .
```

![alt text](image-23.png)

### 4.0 Image Inspection

```bash
docker image pull alpine
docker image inspect alpine
```
![alt text](image-24.png)

```bash
docker image inspect --format "{{ json .RootFS.Layers }}" alpine
```
![alt text](image-25.png)

```bash
docker image inspect --format "{{ json .RootFS.Layers }}" 97044ff13e5a
```
![alt text](image-26.png)

## Swarm Mode Introduction for IT Pros

### 1.0 Initialize Your Swarm

```bash
docker swarm init --advertise-addr $(hostname -i)
```

![alt text](image-27.png)


```bash 
docker swarm join --token SWMTKN-1-5bmrgxt0uwb1hsbs661io0ho2s91lfnv3w513wgq2n6ia10liw-cggqjsbiw7buyg2uxfyplvvfq 192.168.0.17:2377
```
![alt text](image-29.png)

### 2.0 Show Swarm Members

```bash
docker node ls
```

![alt text](image-28.png)

### 3.0 Clone the Voting App
```bash
git clone https://github.com/docker/example-voting-app
cd example-voting-app
```

![alt text](image-30.png)

### 4.0 Deploy a Stack

```bash
cat docker-stack.yml
```
![alt text](image-31.png)

```bash
docker stack deploy --compose-file=docker-stack.yml voting_stack
```
![alt text](image-32.png)

```bash
docker stack ls
```
![alt text](image-33.png)


```bash
docker stack services voting_stack
```
![alt text](image-34.png)


```bash
docker service ps voting_stack_vote
```

![alt text](image-35.png)

### 5.0 Scaling An Application

```bash
docker service scale voting_stack_vote=5
```

![alt text](image-36.png)

# Stage 2: Digging Deeper

## Seccomp profiles Security Lab: Seccomp

### Step 1: Clone the labs GitHub repo

```bash
git clone https://github.com/docker/labs
cd labs/security/seccomp
```

![alt text](image-37.png)

### Step 2: Test a seccomp profile

```bash
docker run --rm -it --cap-add ALL --security-opt apparmor=unconfined --security-opt seccomp=seccomp-profiles/deny.json alpine sh```

![alt text](image-38.png)

```bash
cat seccomp-profiles/deny.json
```

![alt text](image-39.png)


### Step 3: Run a container with no seccomp profile

```bash
docker run --rm -it --security-opt seccomp=unconfined debian:jessie sh
```

![alt text](image-40.png)

```bash 
whoami

unshare --map-root-user --user
whoami

exit
exit
```

![alt text](image-41.png)


```bash
apk add --update strace
strace -c -f -S name whoami 2>&1 1>/dev/null | tail -n +3 | head -n -2 | awk '{print $(NF)}'
```

![alt text](image-42.png)


```bash
strace whoami
```
![alt text](image-43.png)

### Step 4: Selectively remove syscalls

```bash
docker run --rm -it --security-opt seccomp=./seccomp-profiles/default-no-chmod.json alpine sh
```

```bash
chmod 777 / -v
```

![alt text](image-44.png)


```bash
docker run --rm -it --security-opt seccomp=./seccomp-profiles/default.json alpine sh
```

```bash
chmod 777 / -v
```

![alt text](image-46.png)

```bash
exit

cat ./seccomp-profiles/default.json | grep chmod

cat ./seccomp-profiles/default-no-chmod.json | grep chmod
```

![alt text](image-47.png)

### Step 5: Write a seccomp profile

```bash
strace -c -f -S name ls 2>&1 1>/dev/null | tail -n +3 | head -n -2 | awk '{print $(NF)}'
```

![alt text](image-48.png)

### Step 6: A few gotchas

![alt text](image-49.png)

## Linux Kernel Capabilities and Docker Security Lab: Capabilities

### Step 1: Introduction to capabilities

![alt text](image-50.png)

### Step 2: Working with Docker and capabilities

![alt text](image-51.png)

### Step 3: Testing Docker capabilities

```bash
docker run --rm -it alpine chown nobody /
```
![alt text](image-52.png)

```bash
docker run --rm -it --cap-drop ALL --cap-add CHOWN alpine chown nobody /
```

![alt text](image-53.png)


```bash
docker run --rm -it --cap-drop CHOWN alpine chown nobody /
```

![alt text](image-54.png)

```bash
docker run --rm -it --cap-add chown -u nobody alpine chown nobody /
```
![alt text](image-55.png)


### Step 4: Extra for experts

```bash
docker run --rm -it alpine sh -c 'apk add -U libcap; capsh --print'
```

![alt text](image-56.png)

```bash
docker run --rm -it alpine sh -c 'apk add -U libcap;capsh --help'
```
![alt text](image-57.png)

## Docker Networking Hands-on Lab
### Section #1 - Networking Basics
### Step 1: The Docker Network Command

```bash
docker network
```
![alt text](image-58.png)

### Step 2: List networks
```bash
docker network ls
```

![alt text](image-59.png)

### Step 3: Inspect a network

```bash
docker network inspect bridge
```

![alt text](image-60.png)

### Step 4: List network driver plugins

```bash
docker info
```

![alt text](image-61.png)

### Section #2 - Bridge Networking
### Step 1: The Basics

```bash
docker network ls
```
![alt text](image-62.png)

```bash
apk update
apk add bridge
```

![alt text](image-63.png)

```bash
brctl show
```

![alt text](image-64.png)

```bash
ip a
```
![alt text](image-65.png)

### Step 2: Connect a container

```bash
docker run -dt ubuntu sleep infinity
```
![alt text](image-66.png)

```bash
docker ps
```
![alt text](image-67.png)

```bash
brctl show
```

![alt text](image-68.png)


```bash
docker network inspect bridge
```
![alt text](image-69.png)

### Step 3: Test network connectivity

```bash
docker ps
```
![alt text](image-70.png)

```bash
docker exec -it 9b /bin/bash
apt-get update && apt-get install -y iputils-ping
ping -c5 www.github.com
```

![alt text](image-72.png)


### Step 4: Configure NAT for external connectivity

```bash
docker run --name web1 -d -p 8080:80 nginx
```
![alt text](image-73.png)

```bash
docker ps
```
![alt text](image-74.png)

```bash
curl 127.0.0.1:8080
```
![alt text](image-75.png)

### Section #3 - Overlay Networking
### Step 1: The Basics

```bash
docker swarm init --advertise-addr $(hostname -i)
```

![alt text](image-76.png)

```bash
docker swarm join --token SWMTKN-1-05pqqmsx3frv1ar23ui9o2ndqwfhlhe5vp2kmr9h5dmg2j9chh-4qu1my5cm981etzukcbq1e3ur 192.168.0.18:2377
```

![alt text](image-77.png)

```bash
docker node ls
```
![alt text](image-78.png)

### Step 2: Create an overlay network

```bash
docker network create -d overlay overnet
```
![alt text](image-79.png)

```bash
docker network ls
```

![alt text](image-80.png)


```bash
docker network ls
```

![alt text](image-81.png)

```bash
docker network inspect overnet
```
![alt text](image-82.png)

### Step 3: Create a service

```bash
docker service create --name myservice \
--network overnet \
--replicas 2 \
ubuntu sleep infinity
```

![alt text](image-83.png)

```bash
docker service ls
```

![alt text](image-84.png)


```bash
docker service ps myservice
```
![alt text](image-85.png)

```bash
docker network ls
```
![alt text](image-86.png)

```bash
docker network inspect overnet
```

![alt text](image-87.png)

### Step 4: Test the network

```bash
docker network inspect overnet
```

![alt text](image-89.png)

```bash
docker ps
```

![alt text](image-90.png)

```bash
docker exec -it b388 /bin/bash
```

![alt text](image-91.png)

```bash
ping -c5 10.0.0.3
```
![alt text](image-92.png)

### Step 5: Test service discovery

```bash
cat /etc/resolv.conf
```
![alt text](image-93.png)

```bash
docker service inspect myservice
```
![alt text](image-94.png)

### Cleaning Up

```bash
docker service rm myservice
```

![alt text](image-95.png)
```bash
docker ps
```
![alt text](image-96.png)

```bash
docker swarm leave --force
```

![alt text](image-97.png)
```bash
docker swarm leave --force
```
![alt text](image-98.png)

## Docker Orchestration Hands-on Lab

### Section 1: What is Orchestration
![alt text](image-99.png)
### Section 2: Configure Swarm Mode

```bash
docker run -dt ubuntu sleep infinity
```

![alt text](image-100.png)

```bash
docker ps
```

![alt text](image-101.png)

### Step 2.1 - Create a Manager node

```bash
docker swarm init --advertise-addr $(hostname -i)
```

![alt text](image-102.png)

```bash
docker info
```

![alt text](image-103.png)

### Step 2.2 - Join Worker nodes to the Swarm

```bash
docker swarm join --token SWMTKN-1-5h68elcxitpddq3gmxhyc34gcb4v72mc7evuwksyul6346fj7m-357o1rhdv3wesufdvc6jmm37f 192.168.0.13:2377
```

![alt text](image-104.png)

```bash
docker node ls
```
![alt text](image-105.png)


### Section 3: Deploy applications across multiple hosts

### Step 3.1 - Deploy the application components as Docker service

```bash
docker service create --name sleep-app ubuntu sleep infinity
```

![alt text](image-106.png)

```bash
docker service ls
```

![alt text](image-107.png)

### Section 4: Scale the application

```bash
docker service update --replicas 7 sleep-app
```

![alt text](image-108.png)
```bash
docker service ps sleep-app
```

![alt text](image-109.png)


```bash
docker service update --replicas 4 sleep-app
```
![alt text](image-110.png)

```bash
docker service ps sleep-app
```
![alt text](image-111.png)

### Section 5: Drain a node and reschedule the containers

```bash
docker node ls
```

![alt text](image-112.png)

```bash
docker ps
```

![alt text](image-113.png)

```bash
docker node ls
```
![alt text](image-114.png)

```bash

docker node update --availability drain 5tas04ybl72rmgfxrgjesqo5z

docker node ls
```

![alt text](image-115.png)


```bash
docker ps
```
![alt text](image-116.png)

```bash
docker service ps sleep-app
```
![alt text](image-117.png)

### Cleaning Up

```bash
docker service rm sleep-app
```
![alt text](image-118.png)

```bash
docker ps
```
![alt text](image-119.png)

```bash
docker kill 57549475bedc
```
![alt text](image-120.png)

```bash
docker swarm leave --force
docker swarm leave --force
docker swarm leave --force
```

![alt text](image-121.png)

# Getting Started Walk-through for Developers

## Stage 1: The Basics
## Docker for Beginners - Linux
### Task 0: Prerequisites

```bash
git clone https://github.com/dockersamples/linux_tweet_app
```
![alt text](image-122.png)

### Task 1: Run some simple Docker containers
```bash
docker container run alpine hostname
```

![alt text](image-123.png)
```e52dd2ebd9aa```
```bash
docker container ls --all
```

![alt text](image-124.png)

#### Run an interactive Ubuntu container

```bash
docker container run --interactive --tty --rm ubuntu bash
```
![alt text](image-125.png)

```bash
ls /
ps aux
cat /etc/issue
```

![alt text](image-126.png)

```bash
exit

cat /etc/issue
```
![alt text](image-127.png)

#### Run a background MySQL container

```bash
docker container run \
 --detach \
 --name mydb \
 -e MYSQL_ROOT_PASSWORD=my-secret-pw \
 mysql:latest
```

![alt text](image-128.png)

```bash
docker container ls
```
![alt text](image-129.png)

```bash
docker container logs mydb
```

![alt text](image-130.png)

```bash
docker container top mydb
```
![alt text](image-131.png)

```bash
docker exec -it mydb \
 mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version
```
![alt text](image-132.png)

```bash
docker exec -it mydb sh
```

![alt text](image-133.png)

```bash
mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version
```
![alt text](image-134.png)

### Task 2: Package and run a custom app using Docker

#### Build a simple website image

```bash
cd ~/linux_tweet_app
cat Dockerfile
```
![alt text](image-135.png)

```bash
export DOCKERID=rancerenly
echo $DOCKERID

docker image build --tag $DOCKERID/linux_tweet_app:1.0 .
```

![alt text](image-136.png)

![alt text](image-137.png)

```bash
docker container run \
 --detach \
 --publish 80:80 \
 --name linux_tweet_app \
 $DOCKERID/linux_tweet_app:1.0
```

 ![alt text](image-138.png)

```bash
docker container rm --force linux_tweet_app
```

![alt text](image-139.png)

### Task 3: Modify a running website

```bash
docker container run \
 --detach \
 --publish 80:80 \
 --name linux_tweet_app \
 --mount type=bind,source="$(pwd)",target=/usr/share/nginx/html \
 $DOCKERID/linux_tweet_app:1.0
 ```

 ![alt text](image-140.png)


```bash
cp index-new.html index.html


docker rm --force linux_tweet_app

 docker container run \
 --detach \
 --publish 80:80 \
 --name linux_tweet_app \
 $DOCKERID/linux_tweet_app:1.0

 docker rm --force linux_tweet_app
```

![alt text](image-141.png)


#### Update the image

```bash
docker image build --tag $DOCKERID/linux_tweet_app:2.0 .

docker image ls
```

![alt text](image-142.png)

#### Test the new version

```bash
 docker container run \
 --detach \
 --publish 80:80 \
 --name linux_tweet_app \
 $DOCKERID/linux_tweet_app:2.0
 ```
 
```bash
docker container run \
 --detach \
 --publish 8080:80 \
 --name old_linux_tweet_app \
 $DOCKERID/linux_tweet_app:1.0
 ```

![alt text](image-143.png)

#### Push your images to Docker Hub

```bash

docker image ls -f reference="$DOCKERID/*"
docker login

*** 

docker image push $DOCKERID/linux_tweet_app:1.0
docker image push $DOCKERID/linux_tweet_app:2.0
```

![alt text](image-144.png)


## Application Containerization and Microservice Orchestration

### Stage Setup

![alt text](image-145.png)

### Step 0: Basic Link Extractor Script

```bash
git checkout step0
tree

cat linkextractor.py

./linkextractor.py http://example.com/

ls -l linkextractor.py

python3 linkextractor.py
```

![alt text](image-146.png)

### Step 1: Containerized Link Extractor Script


```bash
git checkout step1
tree

cat Dockerfile

docker image build -t linkextractor:step1 .
```

![alt text](image-148.png)

```bash
docker image ls
```
![alt text](image-149.png)

```bash
docker container run -it --rm linkextractor:step1 http://example.com/
```
Вывод:
```bash
http://www.iana.org/domains/example
```

### Step 2: Link Extractor Module with Full URI and Anchor Text



```bash
git checkout step2
tree

cat linkextractor.py

docker image build -t linkextractor:step2 .

docker image ls
```

![alt text](image-150.png)

```bash
docker container run -it --rm linkextractor:step2 http://example.com/
```

Вывод:

```bash
[More information...](http://www.iana.org/domains/example)
```

### Step 3: Link Extractor API Service

```bash

git checkout step3
tree

cat Dockerfile

cat main.py

docker image build -t linkextractor:step3 .

docker container run -d -p 5000:5000 --name=linkextractor linkextractor:step3

```

![alt text](image-151.png)

```bash
curl -i http://localhost:5000/api/http://example.com/
```

```bash
HTTP/1.0 200 OK
Content-Type: application/json
...
[{"href":"http://www.iana.org/domains/example","text":"More information..."}]
```

### Step 4: Link Extractor API and Web Front End Services

```bash

git checkout step4
tree

cat docker-compose.yml

cat www/index.php

docker-compose up -d --build
```

![alt text](image-152.png)
![alt text](image-153.png)

```bash
docker container ls
```

![alt text](image-154.png)

```bash
curl -i http://localhost:5000/api/http://example.com/
-----

-----
HTTP/1.0 200 OK
Content-Type: application/json
...

```

### Step 5: Redis Service for Caching

```bash

git checkout step5
tree

cat www/Dockerfile

cat api/main.py

cat docker-compose.yml

docker-compose up -d --build
```

![alt text](image-155.png)
![alt text](image-156.png)


### Step 6: Swap Python API Service with Ruby

```bash

git checkout step6
tree

cat api/linkextractor.rb

cat api/Dockerfile

cat docker-compose.yml

docker-compose up -d --build
```

![alt text](image-157.png)
![alt text](image-158.png)


```bash
curl -i http://localhost:4567/api/http://example.com/

HTTP/1.1 200 OK
Content-Type: application/json
...
Connection: Keep-Alive

[
  {
    "text": "More information...",
    "href": "http://www.iana.org/domains/example"
  }
]

```

## Deploying a Multi-Service App in Docker Swarm Mode Swarm stack introduction


### Init your swarm

```bash
docker swarm init --advertise-addr $(hostname -i)
```

![alt text](image-159.png)

### Show members of swarm
```bash
docker node ls
```

![alt text](image-160.png)

### Clone the voting-app

```bash
git clone https://github.com/docker/example-voting-app
cd example-voting-app
```
![alt text](image-161.png)

### Deploy a stack

```bash
docker stack deploy --compose-file=docker-stack.yml voting_stack

docker stack ls
```
![alt text](image-162.png)
![alt text](image-163.png)

```bash
docker stack services voting_stack
```
![alt text](image-164.png)

```bash
docker service ps voting_stack_vote
```
![alt text](image-165.png)