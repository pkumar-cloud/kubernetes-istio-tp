# Docker
[Docker website](https://docker.com/)

# Basic Docker Hands-On

```bash
# run hello-world
docker run hello-world

# run busybox:stable
docker run busybox:stable

# run nginx 
docker run nginx

# list all containers
docker ps -a

# remove containers
docker rm -f [container-id or container-name]

# run nginx : exposing container port 80 to host port 8888, give it name smiling_whale
docker run -d -p 8888:80 --name smiling_whale nginx

# Go to bash shell inside smiling_whale container
docker exec -it smiling_whale bash

# Create another nginx with name dancing_octopus and expose port 80 to host port 8899
docker run -d -p 8899:80 --name dancing_octopus nginx

# Go to bash shell inside dancing_octopus container
docker exec -it dancing_octopus bash

# Copy file from host to smiling_whale, folder /usr/share/nginx/html/ OR create a .HTML file
# Make sure you go to folder where atlantis.html located, or define full path to atlantis.html
docker cp atlantis.html smiling_whale:/usr/share/nginx/html/

# Copy file from host to dancing_octopus, folder /usr/share/nginx/html/
# Make sure you go to folder where atlantis.html located, or define full path to atlantis.html
docker cp atlantis.html dancing_octopus:/usr/share/nginx/html/

# Copy file from container smiling_whale to host
docker cp smiling_whale:/usr/share/nginx/html/whale.html whale_copy.html

# Mount folder from host into container smiling_whale /usr/share/nginx/html/
# Please adjust the folder d:/... into your own local folder
docker run -d -p 8888:80 --name smiling_whale -v d:/my-courses/nginx-html:/usr/share/nginx/html/ nginx

# Mount folder from host into container dancing_octopus /usr/share/nginx/html/
# Please adjust the folder d:/... into your own local folder
docker run -d -p 8888:80 --name dancing_octopus -v d:/my-courses/nginx-html:/usr/share/nginx/html/ nginx

```

# Building Docker Image - Demo

The java code and docker files are located under folder `kubernetes-istio\kubernetes-istio-java-code`, and in this part we use `release-1.0.0` sub-folder:

```bash
# Install Java 17
java -version

# Build java from gradle (note the separator, use \ or / according to your operating system)
# This is for windows OS (\) -> .\gradlew clean bootJar
# From kubernetes-istio\kubernetes-istio-java-code\release-1.0.0\devops.blue
cd ./kubernetes-istio-java-code/release-1.0.0/devops.blue

./gradlew clean bootJar

# Build docker image (note the dot as last character)
docker build --tag devops-blue:1.0.0 .

# Login to docker registry (just do this once)
docker login

# Tag local docker image
docker tag devops-blue:1.0.0 [your-docker-username]/devops-blue:1.0.0
docker tag devops-blue:1.0.0 pndrns/devops-blue:1.0.0 

# Push docker image to registry
docker push [your-docker-username]/devops-blue:1.0.0
docker push pndrns/devops-blue:1.0.0
```

To run the image, exposing at port 8888

```bash
docker run -d -p 8888:8111 --name my-devops-blue devops-blue:1.0.0
Access: http://localhost:8888/devops/blue/swagger-ui/index.html
Healthcheck: http://localhost:8888/devops/blue/actuator/health
HelloAPI: http://localhost:8888/devops/blue/api/hello

docker ps -a
docker rm -f <CID>
docker images
docker image rm -f <I-ID>
```
# Docker Compose
```bash
# On Windows or Mac, docker compose automatically installed when you install docker. On Linux, you need to install docker compose.
docker-compose version
cd docker-compose
docker-compose -f docker-compose-wordpress.yml up -d
#  -f: specify which file to use, -d: run the containers at background
# By default, docker compose engine will find file with name docker-compose.yml at current dir.
localhost:8000
docker-compose -f docker-compose-wordpress.yml down
```