# Folder kubernetes-istio-java-code

## Contains java source code (Spring boot) used for example applications.

### devops.blue:
```
Compile: 
# From kubernetes-istio\kubernetes-istio-java-code\release-1.0.0\devops.blue
cd ./kubernetes-istio-java-code/release-1.0.0/devops.blue
# Build java from gradle (note the separator, use \ or / according to your operating system)
# This is for windows OS (\) -> .\gradlew clean bootJar
./gradlew clean bootjar

Build image: docker build --tag devops-blue:1.0.0 .

Run: docker run -d -p 8888:8111 --name my-devops-blue devops-blue:1.0.0

Access: http://localhost:8888/devops/blue/swagger-ui/index.html
Healthcheck: http://localhost:8888/devops/blue/actuator/health

Push:
docker tag devops-blue:1.0.0 pndrns/devops-blue:1.0.0  #Tagging using my account
docker push pndrns/devops-blue:1.0.0

docker ps -a
docker rm -f <CID>
docker images
docker image rm if <I-ID>

```

# Folder kubernetes-istio-scripts

## Contains kubernetes and istio scripts (yaml declarative files) used throughout the course.

# kubernetes-istio-postman-collection.json

## To use it this postman collection:

- Download and install postman
- Import postman collection