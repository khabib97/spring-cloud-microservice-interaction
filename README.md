# spring-cloud-microservice-interaction

This repo contains four project. 

Mainly these repos are created for demonstrating - "How do different microservices communicate with each other". Also, attempts have been made to introduce Spring boot and Docker..  

Four submodules:
1. currency-exchange-service 
2. currency-conversion-service
3. naming-server(eureka)
4. api-gateway(spring-boot-api-gateway)

![Desing Overview](https://raw.githubusercontent.com/khabib97/spring-cloud-microservice-interaction/master/overview.png)

# Project setup

- Download this repository 
```
git clone --recursive https://github.com/khabib97/spring-cloud-microservice-interaction.git
```
- Make submodules to track their respective remote branches (instead of being in detached HEAD state):
```
git submodule foreach -q --recursive 'git checkout $(git config -f $toplevel/.gitmodules submodule.$name.branch || echo master)'
```

---

### Currency Exchange Service
Currency Exchange Service convert one currency to another currency. Conversion rates are stored in H2 database.
- Clone currency-exchange-service repo
- Import as maven porject
- And run as java application
- Creating multiple instance of this service, go Run Configurations -> Arguments(tab) -> Add VM arguments `-Dserver.port=8001`

```
http://localhost:8000/currency-exchange/from/EUR/to/BDT
```

### Currency Conversion Service
Currency conversion service take an amount of currency and convet its value to another currency using Currency Exchange Service 
- Clone currency-conversion-service repo
- Import as maven porject
- And run as java application
```
http://localhost:8100/currency-conversion-feign/from/USD/to/BDT/quantity/10
```

###  Name Server
Naming server is created using netflix-eureka-server. Currency Exchange Service and Currency Conversion Service register them to Naming Server as eureka-client. 

Using `feign client` currency conversion service calls currency exchange service. 

It also perform client side load balancing

- Clone naming-server
- Import as maven porject
- And run as java application

To view register services:
```
http://localhost:8761/
```

### Api Gateway
Api gate way uses spring-boot-api-gateway. It gives an easy interface to talk with different microservices. 

Now  we can call Currency Exchange Service and Currency Conversion Service from:
```
http://localhost:8765/currency-exchange/from/EUR/to/BDT
http://localhost:8765/currency-conversion-feign/from/USD/to/BDT/quantity/10
```
Here we can convert modify our uri, implement authentication...

- Clone api-gateway
- Import as maven porject
- And run as java application

# Docker Version

Now we move to docker. Modifying our repositories so that we can run our services as containers.

*For non docker version, after downloading set head to `v1.0-non.dockerized` tag*

### How to a create docker image?

- In very sub-module, just add the below configuration in `pom.xml` file, inside `build.plugins.plugin` block:
```xml
<configuration>
	<image>
		<name>khabib97/${project.artifactId}:${project.version}</name>
	</image>
	<pullPolicy>IF_NOT_PRESENT</pullPolicy>
</configuration>
```
- Then from eclipse, `right click projct` >>  `run as` >>  `maven build...` >> in `Main` tab's `Goals` >> add `spring-boot:build-image -DskipTests` [skipTests is not a good practice but for quicker image creating] >> `run`

It will create your docker image for that submodule. Create all submodule.

### Zipkin and Rabbit MQ
- Zipkin is used for Distributed Tracing
- Incase of zipkin failure, rabbit mq is used for queueing

### 10000 feet architectural overview: 

![Final Design Overview](https://raw.githubusercontent.com/khabib97/spring-cloud-microservice-interaction/master/overview-zipkin-rabbitmq.png)

### Run 

- After creating all images for the sub modules, run the docker-compose.yaml file,
```
$docker-compose up
```
- It will up and run all containers(magic)
- All previous url will be similar in that case.
- *Zipkins* URL: http://127.0.0.1:9411/
- *Rabbit MQ* URL: http://127.0.0.1:15672/

# Kubernetes Version

Here we modify `currency-exchange-service` and `currency-conversion-service` in such a way, so that we can run it on kubernetes (Here Google Kubernetes Engine).


To get kubernetes version, checkout to `kubernetes` branch of these two repositories. 

- We remove cloud-starter-config, eureka, zipkin, redis from pom.xml
- Then create image and upload image to `dockerhub`
- Create cluster in GKE
- Connect it to `cloud console`
```
$kubectl create deployment currency-exchange --image=khabib97/currency-exchange-service:0.0.2-SNAPSH

$kubctl expose deployment currency-exchange

$kubectl create deployment currency-conversion --image=khabib97/currency-conversion-service:0.0.2-SNAPSH

$kubctl expose deployment currency-conversion
```

- Then go url and enjoy.

---
**Special thanks to *[Ranga Rao Karanam](https://www.linkedin.com/in/rangakaranam/)* for his tutorials**  



















