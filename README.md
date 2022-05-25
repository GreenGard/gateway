
This gateway was made to handle microservices for our lab 3 that you can access ![image](https://user-images.githubusercontent.com/90625377/170195316-77ce35c9-64ff-4eff-ac33-b2b3b5bd2797.png)
but can of course be used for other services

Start with clone https://github.com/GreenGard/gateway.git

Open project locally and run mvn package

In Docker desktop
Run Dockerfile while standing in project root
```
docker build -t gateway:latest .
```
Start the Gateway container
```
docker run -d --name gateway -p 8000:8000 --network=<network with your microservice> -e CONSUL_HOST=consul gateway:latest
```
In Consul 
Add a new Key/Value config/gateway/data (.yml)
and write (for ex.)
server:
   port: 8000
spring:
   cloud:
      consul:
         discovery:
            register: false
            registerHealthCheck: false
         host: consul
      gateway:
      discovery:
      locator:
      enabled: true
      routes:
            - id: authentication
              uri: lb://authentication
              predicates:
                 - Path=/authentication/**
              filters:
                 - RewritePath=/authentication/(?<path>.*), /$\{path}
            - id: user-service
              uri: lb://user-service
              predicates:
                 - Path=/users/**
              filters:
                 - RewritePath=/users/(?<path>.*), /users/$\{path}
              
key:
 public: <your public key>
