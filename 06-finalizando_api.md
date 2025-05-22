# Finalizando api de compras

Nos capítulos anteriores, implementamos uma API de compras que utiliza o Apache Kafka para processar mensagens de forma assíncrona. o shop api envia a solicitação de compra para o tópico SHOP_TOPIC, e o consumidor da api de validação de compras consome essas mensagens e processa as compras. Neste capítulo, vamos finalizar a implementação da API de compras, adicionando um listeners para o tópico SHOP_TOPIC_EVENT, que irá processar as mensagens de eventos de compras e mudar o status da compra para "COMPLETED" ou "FAILED". Além disso, vamos adicionar um endpoint para consultar o status da compra e um endpoint para cancelar a compra.

```java
public class ReceiveKafkaMessage {

	private final ShopRepository shopRepository;

	private static final String SHOP_TOPIC_EVENT_NAME = "SHOP_TOPIC_EVENT";

	@KafkaListener(topics = SHOP_TOPIC_EVENT_NAME, groupId = "group")
	public void listenShopEvents(ShopDTO shopDTO) {
		try {
		    log.info("Status da compra recebida no tópico: {}.", shopDTO.getIdentifier());

		    Shop shop = shopRepository.findByIdentifier(shopDTO.getIdentifier());
		    shop.setStatus(shopDTO.getStatus());
		    shopRepository.save(shop);
		} catch(Exception e) {
			log.error("Erro no processamento da compra {}", shopDTO.getIdentifier());
		}
	}

}
```
Com essa implementação, o consumidor irá escutar o tópico SHOP_TOPIC_EVENT e processar as mensagens de eventos de compras. Quando uma mensagem é recebida, o consumidor atualiza o status da compra no banco de dados. Se ocorrer algum erro durante o processamento, uma mensagem de erro será registrada no log.
