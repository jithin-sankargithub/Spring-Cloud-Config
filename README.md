# Spring-Cloud-Config
The repository deals with Spring Cloud config server, Spring Actuator refresh, Spring Cloud bus and Spring cloud config monitor


Config Server Steps

Adding config server to Microservice setup
1) Create new Spring project by adding Spring cloud config server and actuator dependency
2) Add the project to class path and add EnableConfigServer property in Application file
3) Server port to be pointed to 8071

Three ways for add config
- ClassPath
spring:
  profiles:
    active: native #for classpath searching of config files
  cloud:
    config:
      server:
        native:
          search-locations: "classpath:/config"
- File system
 search-locations: "file:/{FilePath}"
- Github - Recommended
1) Create Git repo and add all config files into a folder
  cloud:
    config:
      server:
#       native:
#         search-locations: "classpath:/config"
#         search-locations: "file:/filepath"
        git:
          uri: "https://github.com/jithin-sankargithub/Spring-Cloud-Config.git"
          default-label: master
          timeout: 5
          clone-on-start: true
          force-pull: true
		  
For encrypting
1) Add encrypt:key - in application.yml and use http://localhost:8071/encrypt - to encrypt
		  
		  
Refreshing config using actuator path
1) Note - The configuration properties should be a class
2) Enable actuator endpoint paths
3) Make changes in config files which are available in git
4) Trigger post method of refresh from actuator endpoint - localhost:8080/actuator/refresh

Refreshing config using Spring cloud Bus
1) Run the command to run rabbit mq in our local to get started without installing rabbitmq directly 
# latest RabbitMQ 3.13
docker run -it --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3.13-management
2) Add dependency for spring cloud bus in amqp in all microservices
3) Make sure to expose busRefresh path in actuator endpoint url
4) Add connection details of rabbitmq in all microservices
spring: 
  rabbitmq: 
    host: "localhost"
    port: 5672
    username: "guest"
    password: "guest"
5) Trigger busrefresh api to any one of the running Microservices 

Refreshing config using Spring cloud config monitor
1) Add dependency of Spring cloud config monitor dependency in config server pom.xml
2) Add actuator endpoint exposure in config server
3) Add rabbit mq configurations
4) Create a webhook - User hookdeck to create a sandbox instead of using localhost
