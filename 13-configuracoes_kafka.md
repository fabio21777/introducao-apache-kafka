# Configurações do Kafka

O Kafka possui três tipos principais de configuração:

1. **Configurações gerais (broker)**
2. **Configurações dos consumidores**
3. **Configurações dos produtores**

---

## 1. Configurações Gerais (Broker)

Definidas no arquivo `server.properties` ou na inicialização do broker.

- `num.partitions`: número padrão de partições.
- `log.dir`: diretório dos logs.
- `log.retention.hours`: tempo de retenção dos logs.
- `log.retention.bytes`: tamanho máximo dos logs.
- `delete.topic.enable`: permite exclusão de tópicos.

---

## 2. Configurações dos Consumidores

Definidas na inicialização do consumidor, geralmente em um `Map<String, Object> props`.

Principais configurações:

- **Bootstrap servers**
  ```java
  props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
  ```

- **Serialização/Deserialização**
  Define como as mensagens serão convertidas para/da aplicação.
  ```java
  props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
  props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, JsonDeserializer.class);
  ```

- **Criação automática de tópicos**
  ```java
  props.put(ConsumerConfig.ALLOW_AUTO_CREATE_TOPICS_CONFIG, false);
  ```

- **Quantidade máxima de registros por poll**
  ```java
  props.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, 1000);
  ```

- **Offset inicial (início ou fim do tópico)**
  ```java
  props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
  ```

- **Consumer Group**
  ```java
  props.put(ConsumerConfig.GROUP_ID_CONFIG, "group");
  ```

- **Heartbeat (intervalo de notificação do consumidor)**
  ```java
  props.put(ConsumerConfig.HEARTBEAT_INTERVAL_MS_CONFIG, 1000);
  ```

- **Session timeout (tempo máximo sem heartbeat)**
  ```java
  props.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, 10000);
  ```

- **Commit automático**
  ```java
  props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false);
  ```

---

## 3. Configurações dos Produtores

Definidas na inicialização do produtor, também em um `Map<String, Object> props`.

Principais configurações:

- **Bootstrap servers**
  ```java
  props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
  ```

- **Serialização**
  ```java
  props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
  props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
  ```

- **Tamanho do batch (envio em lote)**
  ```java
  props.put(ProducerConfig.BATCH_SIZE_CONFIG, 16384);
  ```

- **Identificação do produtor**
  Facilita rastreamento e debug.
  ```java
  props.put(ProducerConfig.CLIENT_ID_CONFIG, "client-1");
  ```

- **Timeout de conexão ociosa**
  Tempo máximo que a conexão pode ficar sem uso.
  ```java
  props.put(ProducerConfig.CONNECTIONS_MAX_IDLE_MS_CONFIG, 10000);
  ```

---

## Resumo

- **Broker:** configurações globais do servidor Kafka.
- **Consumidor:** controle de leitura, grupos, offset, commit e performance.
- **Produtor:** controle de envio, identificação, batch e performance.

Mantenha as configurações objetivas e ajuste conforme a necessidade da sua aplicação para garantir desempenho, rastreabilidade e segurança.
