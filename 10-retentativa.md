# Retentativa

Por padrão, o Kafka tenta processar uma mensagem até 10 vezes. Caso, após esse número de tentativas, a mensagem ainda apresente erro, as próximas mensagens não serão lidas enquanto a mensagem com erro não for tratada.

```java
public class ReceiveKafkaMessage {

    private final KafkaTemplate<String, ShopDTO> kafkaTemplate;

    private static final String SHOP_TOPIC = "SHOP_TOPIC";
    private static final String SHOP_TOPIC_RETRY = "SHOP_TOPIC_RETRY";

    @KafkaListener(topics = SHOP_TOPIC, groupId = "group_retry")
    public void listenShopTopic(ShopDTO shopDTO) {
        if (shopDTO.getItems() == null || shopDTO.getItems().isEmpty()) {
            log.error("Compra sem itens");
            throw new Exception();
        }
    }
}
```

Para contornar esse problema, podemos utilizar um bloco try-catch e tratar o erro, por exemplo, apenas registrando um log:

```java
public class ReceiveKafkaMessage {

    private final KafkaTemplate<String, ShopDTO> kafkaTemplate;

    private static final String SHOP_TOPIC = "SHOP_TOPIC";
    private static final String SHOP_TOPIC_RETRY = "SHOP_TOPIC_RETRY";

    @KafkaListener(topics = SHOP_TOPIC, groupId = "group_retry")
    public void listenShopTopic(ShopDTO shopDTO) {
        try {
            if (shopDTO.getItems() == null || shopDTO.getItems().isEmpty()) {
                log.error("Compra sem itens");
                throw new Exception();
            }
        } catch (Exception e) {
            log.info("Erro na aplicação");
        }
    }
}
```

Com essa solução, o Kafka não para de ler as mensagens, mas o erro apenas será registrado no log.

> Podemos adicionar uma solução chamada **dead letter topic**, onde as mensagens que falharam vão para outro tópico e, posteriormente, podemos tratá-las.

```java
public class ReceiveKafkaMessage {

    private final KafkaTemplate<String, ShopDTO> kafkaTemplate;

    private static final String SHOP_TOPIC = "SHOP_TOPIC";
    private static final String SHOP_TOPIC_RETRY = "SHOP_TOPIC_RETRY";

    @KafkaListener(topics = SHOP_TOPIC, groupId = "group_retry")
    public void listenShopTopic(ShopDTO shopDTO) {
        try {
            if (shopDTO.getItems() == null || shopDTO.getItems().isEmpty()) {
                log.error("Compra sem itens");
                throw new Exception();
            }
        } catch (Exception e) {
            log.info("Erro na aplicação");
            kafkaTemplate.send(SHOP_TOPIC_RETRY, shopDTO);
        }
    }

    @KafkaListener(topics = SHOP_TOPIC_RETRY, groupId = "group_report")
    public void listenShopTopicRetry(ShopDTO shopDTO) {
        log.info("Retentativa de processamento: {}.", shopDTO.getIdentifier());
    }
}
```

Nesse exemplo, o Kafka tentará processar a mensagem até 10 vezes. Se não conseguir, enviará para o tópico de retentativa, onde será possível realizar um tratamento adicional, como enviar um e-mail ou registrar um log.


## Conclusão

Neste capítulo, aprendemos sobre o conceito de retentativa no Kafka e como lidar com mensagens que falham durante o processamento. Vimos como implementar um bloco try-catch para tratar erros e evitar que o Kafka pare de ler mensagens. Além disso, exploramos a solução de dead letter topic para lidar com mensagens que falharam após várias tentativas.
