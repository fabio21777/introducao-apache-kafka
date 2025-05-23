# Usando docker para criar um ambiente de desenvolvimento


```yml

version: "3.6"
services:

  kafka:
    image: apache/kafka:latest
    environment:
      KAFKA_NODE_ID: 1
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_LISTENERS: PLAINTEXT://kafka:9092,CONTROLLER://localhost:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@localhost:9093
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1
      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1
      KAFKA_GROUP_INITIAL_REBALANCE_DELAY_MS: 0
      KAFKA_NUM_PARTITIONS: 3

  shop-api:
    image: shop-api
    container_name: shop-api
    ports:
      - 8080:8080
    depends_on:
      - kafka

  shop-validator:
    image: shop-validator
    container_name: shop-validator
    ports:
      - 8081:8081
    depends_on:
      - kafka

  shop-report:
    image: shop-report
    container_name: shop-report
    ports:
      - 8082:8082
    depends_on:
      - kafka

```

```dockerfile
FROM openjdk:21-jdk
VOLUME /tmp
ARG JAR_FILE=target/shop-api-1.0-SNAPSHOT.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```
