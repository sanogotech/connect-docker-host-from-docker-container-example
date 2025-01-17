# How to connect to the Docker host machine from within a Docker container?

## Prerequisites

Make sure to have:

* Java 11 [installed](https://sdkman.io/usage)
* Spring Boot CLI [installed](https://docs.spring.io/spring-boot/docs/current/reference/html/getting-started.html#getting-started.installing.cli) (make sure not to install version 3 or later, since the run command has been removed since 3.x)
* Docker [installed](https://docs.docker.com/get-docker/)

    ![Docker Host](https://github.com/sanogotech/connect-docker-host-from-docker-container-example/blob/main/dockerhost1.jpg)

## Blogpost

Small codebase with blogpost: [How to connect to the Docker host from inside a Docker container?](https://medium.com/@TimvanBaarsen/how-to-connect-to-the-docker-host-from-inside-a-docker-container-112b4c71bc66)

- https://medium.com/@TimvanBaarsen/how-to-connect-to-the-docker-host-from-inside-a-docker-container-112b4c71bc66

  ![Docker Host](https://github.com/sanogotech/connect-docker-host-from-docker-container-example/blob/main/dockerhost2.jpg)
  
## Run the most simple Spring Boot web application

```java
@RestController
class SpringBootWebApplication {

    @RequestMapping("/")
    String hello() {
        "Hello world container. You are able to reach the Docker host!\n"
    }
}
```

```
spring run helloWorld.groovy
```



A Spring Boot application will start at port 8080 on your host: [http://localhost:8080](http://localhost:8080)

## Start and attach to the Ubuntu container

```
docker run -it --add-host=host.docker.internal:host-gateway ubuntu bash
```

You have to specify the run flag `--add-host=host.docker.internal:host-gateway` (so just executing `docker run -it ubuntu bash`) otherwise your Ubuntu container can't resolve
`host.docker.internal` and you will get an error like:

```
curl: (6) Could not resolve host: host.docker.internal
```

### Install curl (inside the Ubuntu container):

```
apt update && apt install curl -y
```

## Reach to Spring Boot application that is running on the host

```
curl http://host.docker.internal:8080
```

Response:

```
Hello world container. You are able to reach the Docker host!
```

## Docker compose

Example config using Docker compose

```yml
version: "3.8"
services:

  ubuntu:
    image: ubuntu
    container_name: ubuntu
    extra_hosts:
      - "host.docker.internal:host-gateway"
    command: sleep infinity
```

Start the Ubuntu container using Docker compose:

```
docker-compose up -d
```

Attach to the Ubuntu container started using Docker compose:

```
docker exec -it ubuntu bash
```

Shut down the Ubuntu container:

```
docker-compose down -v
```
