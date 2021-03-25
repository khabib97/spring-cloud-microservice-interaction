# spring-cloud-microservice-interaction

This repo contains four project. Mainly these repos are created for demonstrating, how do different microservices communicate with each other. 

Four submodules:
1. currency-exchange-service 
2. currency-conversion-service
3. naming-server(eureka)
4. api-gateway(spring-boot-api-gateway)

![Desing Overview](https://raw.githubusercontent.com/khabib97/spring-cloud-microservice-interaction/master/overview.png)

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








