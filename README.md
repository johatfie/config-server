# config-server
Configuration Server for Widgets Are Us microservices project

## Frameworks and Dependencies
- Spring Boot 2.4.3
- Spring Cloud 2020.0.1
- Docker
- Logstash
- RabbitMQ

## Details

- Uses Github as the source for config files for all the microservices
- Uses RabbitMQ to push configuration updates to the microservices with changed configuration
- Logging is handled by an ElasticSearch Logstash Kibana (ELK) stack
- Zipkin is used for visualizing user transactions across multiple services
