# Admin client no Kafka

O Kafka possui uma API para clientes administrativos, permitindo criar, listar e deletar tópicos, listar e deletar grupos de consumidores, descrever tópicos e clusters, entre outras operações administrativas.

Adicione a dependência no seu projeto:

```xml
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>3.8.0</version>
</dependency>
```

## Exemplo de uso

```java
package com.santana.kafka.admin;

import java.util.Properties;
import java.util.concurrent.ExecutionException;
import org.apache.kafka.clients.admin.AdminClient;
import org.apache.kafka.clients.admin.AdminClientConfig;

public class Main {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        Properties properties = new Properties();
        properties.put(AdminClientConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        AdminClient adminClient = AdminClient.create(properties);

        KafkaAdmin.create("topico-1", 2, (short) 1, adminClient);
        KafkaAdmin.create("topico-2", 2, (short) 1, adminClient);
        KafkaAdmin.list(adminClient);
        KafkaAdmin.describe("topico-1", adminClient);
        KafkaAdmin.delete("topico-1", adminClient);
        KafkaAdmin.delete("topico-2", adminClient);
        KafkaAdmin.listCG(adminClient);
        KafkaAdmin.deleteCG("group", adminClient);
        KafkaAdmin.describeCluster(adminClient);
    }
}
```

---

### Listando os tópicos

```java
public static void list(AdminClient adminClient) throws InterruptedException, ExecutionException {
    ListTopicsResult topics = adminClient.listTopics();
    topics.names().get().forEach(System.out::println);
}
```

### Criando um tópico

```java
public static void create(String topicName, int partitions, short replicationFactor, AdminClient adminClient)
        throws InterruptedException, ExecutionException {
    final NewTopic newTopic = new NewTopic(topicName, partitions, replicationFactor);
    List<NewTopic> topics = new ArrayList<>();
    topics.add(newTopic);
    try {
        final CreateTopicsResult result = adminClient.createTopics(topics);
        result.all().get();
    } catch (final Exception e) {
        throw new RuntimeException("Failed to create topic: " + topicName, e);
    }
}
```

### Descrevendo um tópico

```java
public static void describe(String topicName, AdminClient adminClient)
        throws InterruptedException, ExecutionException {
    List<String> topicNames = new ArrayList<>();
    topicNames.add(topicName);
    DescribeTopicsResult topics = adminClient.describeTopics(topicNames);
    topics.all().get().forEach((x, y) -> System.out.println(x + " " + y.topicId() + " " + y.partitions()));
}
```

### Deletando um tópico

```java
public static void delete(String topicName, AdminClient adminClient)
        throws InterruptedException, ExecutionException {
    List<String> topicNames = new ArrayList<>();
    topicNames.add(topicName);
    try {
        DeleteTopicsResult topics = adminClient.deleteTopics(topicNames);
        topics.all().get();
    } catch (final Exception e) {
        throw new RuntimeException("Failed to delete topic: " + topicName, e);
    }
}
```

### Listando os grupos

```java
public static void listCG(AdminClient adminClient) throws InterruptedException, ExecutionException {
    ListConsumerGroupOffsetsResult listConsumerGroupOffsetsResult = adminClient.listConsumerGroupOffsets("group");
    listConsumerGroupOffsetsResult.all().get().forEach(cg -> {
        System.out.println(cg.groupId() + " " + cg.topicPartitionOffsets());
    });
}
```

### Deletando um grupo

```java
public static void deleteCG(String groupId, AdminClient adminClient)
        throws InterruptedException, ExecutionException {
    List<String> groupIds = new ArrayList<>();
    groupIds.add(groupId);
    try {
        DeleteConsumerGroupsResult deleteConsumerGroupsResult = adminClient.deleteConsumerGroups(groupIds);
        deleteConsumerGroupsResult.all().get();
    } catch (final Exception e) {
        throw new RuntimeException("Failed to delete group: " + groupId, e);
    }
}
```

### Descrevendo um cluster

```java
public static void describeCluster(AdminClient adminClient)
        throws InterruptedException, ExecutionException {
    DescribeClusterResult cluster = adminClient.describeCluster();
    System.out.println(cluster.clusterId().get());
}
```
