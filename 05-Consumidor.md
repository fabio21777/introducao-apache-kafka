# Consumidor

Implementando um consumidor para ler as mensagens do tópico "SHOP_TOPIC" e processá-las. O consumidor irá imprimir as mensagens recebidas no console. segue um exemplo de uma implentação der um serviço que ao mesmo tempo consome e produz mensagens em um outro tópico.


## kafka KafkaConfig

```java
@Configuration
public class KafkaConfig {

    @Value(value = "${kafka.bootstrapAddress:kafka:9092}")
    private String bootstrapAddress;

    public ProducerFactory<String, ShopDTO> producerFactory() {
        Map<String, Object> props = new HashMap<>();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
        props.put(ProducerConfig.BATCH_SIZE_CONFIG, "100");
        props.put(ProducerConfig.CLIENT_ID_CONFIG, "client-1");
        props.put(ProducerConfig.CONNECTIONS_MAX_IDLE_MS_CONFIG, 10000);
        return new DefaultKafkaProducerFactory<>(props);
    }

    @Bean
    public KafkaTemplate<String, ShopDTO> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }

    public ConsumerFactory<String, ShopDTO> consumerFactory() {
    	JsonDeserializer<ShopDTO> deserializer = new JsonDeserializer<>(ShopDTO.class);

        Map<String, Object> props = new HashMap<>();
        props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);

        return new DefaultKafkaConsumerFactory<>(props, new StringDeserializer(), deserializer);
    }

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, ShopDTO> kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, ShopDTO> factory = new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        return factory;
    }

}
```

### Consumidor/receiver - ReceiveKafkaMessage

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class ReceiveKafkaMessage {

	private static final String SHOP_TOPIC_NAME = "SHOP_TOPIC";
	private static final String SHOP_TOPIC_EVENT_NAME = "SHOP_TOPIC_EVENT";

	private final ProductRepository productRepository;

	private final KafkaTemplate<String, ShopDTO> kafkaTemplate;

	@KafkaListener(topics = SHOP_TOPIC_NAME, groupId = "group")
	public void listenShopTopic(ShopDTO shopDTO) {
	    log.info("Compra recebida no tópico: {}",
	    		shopDTO.getIdentifier());

	    boolean success = true;
	    for (ShopItemDTO item : shopDTO.getItems()) {
	    	Product product = productRepository
	    			.findByIdentifier(
	    					item.getProductIdentifier());
	    	if (!isValidShop(item, product)) {
			    shopError(shopDTO);
				success = false;
	    		break;
	    	}
	    }
	    if (success) {
		    shopSuccess(shopDTO);
	    }

	}

	// valida se a compra possui algum erro
	private boolean isValidShop(ShopItemDTO item, Product product) {
		return product != null &&
			product.getAmount() >= item.getAmount();
	}


	private void shopError(ShopDTO shopDTO) {
		log.info("Erro no processamento da compra {}.", shopDTO.getIdentifier());
		shopDTO.setStatus("ERROR");
		kafkaTemplate.send(SHOP_TOPIC_EVENT_NAME, shopDTO);
	}

	private void shopSuccess(ShopDTO shopDTO) {
		log.info("Compra {} efetuada com sucesso.", shopDTO.getIdentifier());
		shopDTO.setStatus("SUCCESS");
		kafkaTemplate.send(SHOP_TOPIC_EVENT_NAME, shopDTO);
	}

}
```
