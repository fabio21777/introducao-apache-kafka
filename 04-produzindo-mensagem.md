# Produzindo mensagens

## Configuração do Kafka

para adicionar o kafka ao projeto, adicione a dependência abaixo no arquivo pom.xml:
```xml
<dependency>
<groupId>org.springframework.kafka</groupId>
<artifactId>spring-kafka</artifactId>
</dependency>
```

## IMPLEMENTANDO O PRODUTOR

Para implementar o produtor, precisamos de duas novas classes: a classe de configuração e a classe do produtor. A classe de configuração é responsável por configurar o KafkaTemplate, que é a classe responsável por enviar mensagens para o Kafka. A classe do produtor é responsável por enviar as mensagens para o Kafka.

1. **bean kafkaTemplate** -
   - O KafkaTemplate é a classe responsável por enviar mensagens para o Kafka. Ele é configurado com o produtor e o serializador de chave e valor. O serializador de chave e valor é responsável por converter os objetos em bytes, que é o formato que o Kafka espera.
   - O KafkaTemplate é um bean do Spring, o que significa que ele pode ser injetado em outras classes do Spring. Isso facilita a injeção de dependências e torna o código mais limpo e organizado.
2. **KafkaConfig** -
   - A classe KafkaConfig é responsável por configurar o KafkaTemplate. Ela é anotada com @Configuration, o que significa que ela é uma classe de configuração do Spring. Ela também é anotada com @EnableKafka, o que habilita o suporte ao Kafka no Spring.
   - O método kafkaTemplate() cria um novo KafkaTemplate e o configura com o produtor e o serializador de chave e valor. O serializador de chave e valor é configurado para usar o StringSerializer, que é o serializador padrão do Kafka.


```java

package com.santana.events;

import java.util.HashMap;
import java.util.Map;

import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringDeserializer;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.config.ConcurrentKafkaListenerContainerFactory;
import org.springframework.kafka.core.ConsumerFactory;
import org.springframework.kafka.core.DefaultKafkaConsumerFactory;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;
import org.springframework.kafka.support.serializer.JsonDeserializer;
import org.springframework.kafka.support.serializer.JsonSerializer;

import com.santana.dto.ShopDTO;

@Configuration
public class KafkaConfig {

    @Value(value = "${kafka.bootstrapAddress:kafka:9092}")//1
    private String bootstrapAddress;

    public ProducerFactory<String, ShopDTO> producerFactory() {
        Map<String, Object> props = new HashMap<>();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapAddress);
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
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



```java

package com.santana.events;

import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Service;

import com.santana.dto.ShopDTO;

import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

@Slf4j
@Service
@RequiredArgsConstructor
public class SendKafkaMessage {

	private final KafkaTemplate<String, ShopDTO> kafkaTemplate;

	private static final String SHOP_TOPIC_NAME = "SHOP_TOPIC";

	public void sendMessage(ShopDTO msg) {
	    kafkaTemplate.send(SHOP_TOPIC_NAME, msg.getBuyerIdentifier(), msg);
	}

}

```

## Enviando os objeto para um tópico

Para enviar os objetos para o tópico, precisamos criar um método que será responsável por enviar as mensagens. Esse método será chamado quando quisermos enviar uma mensagem para o Kafka. O método sendMessage() é responsável por enviar a mensagem para o Kafka. Ele usa o KafkaTemplate para enviar a mensagem para o tópico "SHOP_TOPIC". O método também registra a mensagem enviada no log.

```java
package com.santana.controller;

import com.santana.dto.ShopDTO;
import com.santana.events.SendKafkaMessage;
import com.santana.model.Shop;
import com.santana.model.ShopItem;
import com.santana.repository.ShopRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.*;

import java.time.LocalDate;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;

@RestController
@RequestMapping("/shop")
@RequiredArgsConstructor
public class ShopController {

    private final ShopRepository shopRepository;
    private final SendKafkaMessage sendKafkaMessage;

    @PostMapping
    public ShopDTO saveShop(@RequestBody ShopDTO shopDTO) {
    	shopDTO.setIdentifier(UUID.randomUUID().toString());
    	shopDTO.setDateShop(LocalDate.now());
    	shopDTO.setStatus("PENDING");

    	Shop shop = Shop.convert(shopDTO);
    	for (ShopItem shopItem : shop.getItems()) {
    		shopItem.setShop(shop);
    	}

        shopDTO = ShopDTO.convert(shopRepository.save(shop));
        sendKafkaMessage.sendMessage(shopDTO);
        return shopDTO;

    }
}
```

## Verificando o tópico do kafka

executando o post contra api com o seguinte payload:

```json
{
   "items":[
	  {
		 "productIdentifier":"123456789",
		 "amount":100,
		 "price":"1000"
	  },
	  {
		 "productIdentifier":"123456789",
		 "amount":100,
		 "price":"1000"
	  }
   ],
   "buyerIdentifier":"123456789"
}
```
 objeto enviado para o kafka:

```json
{
"identifier":"d351ea15-345d-4068-8f8d-e48812c8169b",
"dateShop":"10:07:04.848385",
"items":[
		{
			"productIdentifier":"123456789",
			"amount":100,
			"price":1000.0
		},
		{
			"productIdentifier":"123456789",
			"amount":100,
			"price":1000.0
		}
	]
}

```

### Verificando o tópico do kafka

Para verificar o tópico do kafka, podemos usar o comando kafka-console-consumer.sh. Esse comando permite que você consuma mensagens de um tópico específico. Para isso, basta executar o seguinte comando:

```sh
./bin/kafka-console-consumer.sh \
--topic SHOP_TOPIC \
--from-beginning \
--bootstrap-server localhost:9092 \
```

A resposta será a seguinte:

```json
{"identifier":"d351ea15-345d-4068-8f8d-e48812c8169b","dateShop":[
10,7,4,848385000],"items":[{"productIdentifier":"123456789","amou
nt":100,"price":1000.0},{"productIdentifier":"123456789","amount"
:100,"price":1000.0}]}
```

As mensagens são salvas na ordem em que foram enviadas. O Kafka garante a entrega das mensagens na ordem em que foram enviadas.

### Conclusão

Neste capítulo, aprendemos como produzir mensagens para o Kafka usando o Spring Boot. Vimos como criar um produtor e enviar mensagens para um tópico específico. Também aprendemos como verificar as mensagens enviadas para o tópico usando o comando kafka-console-consumer.sh. No próximo capítulo, aprenderemos como consumir mensagens do Kafka.

