# Usando chaves nas mensagens

O conceito de chaves é importante para garantir que um lote de mensagens seja enviado para a mesma partição do tópico. Isso é fundamental para assegurar que as mensagens sejam processadas na ordem em que foram enviadas, pelo menos para aquelas associadas à mesma chave.

> **Observação:** O Kafka não garante a ordem de entrega das mensagens entre diferentes partições. Porém, se for definida uma chave, todas as mensagens com a mesma chave serão enviadas para a mesma partição, garantindo a ordem de entrega dentro dessa partição.

## Mudança no cliente produtor

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class SendKafkaMessage {

    private final KafkaTemplate<String, ShopDTO> kafkaTemplate;

    private static final String SHOP_TOPIC_NAME = "SHOP_TOPIC";

    public void sendMessage(ShopDTO msg) {
        kafkaTemplate.send(SHOP_TOPIC_NAME, msg.getBuyerIdentifier(), msg); // 1
    }

}
```

1. **msg.getBuyerIdentifier()**: a chave da mensagem. Essa chave é usada pelo Kafka para determinar em qual partição a mensagem será enviada. Se duas mensagens tiverem a mesma chave, elas serão enviadas para a mesma partição, garantindo que sejam processadas na ordem em que foram enviadas.

## Mudança no cliente consumidor

```java
@KafkaListener(topics = SHOP_TOPIC_NAME, groupId = "group")
public void listenShopTopic(
    ShopDTO shopDTO,
    @Header(KafkaHeaders.RECEIVED_MESSAGE_KEY) String key,
    @Header(KafkaHeaders.RECEIVED_PARTITION_ID) String partitionId,
    @Header(KafkaHeaders.RECEIVED_TIMESTAMP) String timestamp
) {
    log.info("Compra recebida no tópico: {} com chave {} na partição {} hora {}.",
        shopDTO.getIdentifier(), key, partitionId, timestamp);
}
```

## Conclusão

Neste capítulo, aprendemos sobre o conceito de chaves nas mensagens do Kafka e como usá-las para garantir que um lote de mensagens seja enviado para a mesma partição do tópico. Isso é importante para garantir que as mensagens sejam processadas na ordem em que foram enviadas. Além disso, vimos como implementar isso tanto no cliente produtor quanto no consumidor.
