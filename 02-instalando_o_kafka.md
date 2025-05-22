# INSTALANDO O KAFKA


## [instalação do kafka ](https://kafka.apache.org/)

##  Tópicos

Um dos principais conceitos do Kafka é o tópico, que é um canal de comunicação onde as mensagens são publicadas e consumidas.segue abaixo um exemplo de como criar um tópico no Kafka:

```sh
./bin/kafka-topics.sh	\
				--create	\
				--topic	topico-teste	\
				--bootstrap-server	localhost:9092
```

nesse primeiro momentos criamos um topicos usando o kafka-topics que é uma ferramenta para gerenciar os tópicos do Kafka. O comando acima cria um tópico chamado "topico-teste" no servidor Kafka em execução na porta 9092.

1. **--create**: indica que estamos criando um novo tópico.
2. **--topic**: especifica o nome do tópico que estamos criando.
3. **--bootstrap-server**: especifica o endereço do servidor Kafka onde o tópico será criado. O endereço é composto pelo hostname e pela porta do servidor Kafka.


## Lista de comandos do kafka-topics

```sh
./bin/kafka-topics.sh	\
				--list	\
				--bootstrap-server	localhost:9092
```

Outro comando interessante é o que retorna detalhes de um tópico específico, que é o describe. O comando abaixo retorna os detalhes do tópico "topico-teste":

```sh
./bin/kafka-topics.sh	\
				--describe	\
				--topic	topico-teste	\
				--bootstrap-server	localhost:9092
```
O comando acima retorna informações sobre o tópico, como o número de partições, o número de réplicas e o líder do tópico.

```sh

Topic:	topico-teste				TopicId:	om2YlC2yQrSKraJZ1zk_gA				Partiti
onCount:	1
ReplicationFactor:	1				Configs:	segment.bytes=1073741824
				Topic:	topico-teste				Partition:	0				Leader:	0				Replicas:
	0				Isr:	0

```


### Enviando e recebendo mensagens do Kafka

Já vimos como criar um tópico no kafa que é  um canal de comunicação onde as mensagens são publicadas e consumidas. Agora vamos ver como enviar e receber mensagens do Kafka, vamos criar produces que são as mensagens que são criadas e enviada para um tópico em específico.
```sh
/bin/kafka-console-producer.sh \
--topic topico-teste \
--bootstrap-server localhost:9092
```

Esse comando iniciar um produtor cada mensagem que for digitada no terminal será enviada para o tópico "topico-teste". Para enviar uma mensagem, basta digitar o texto e pressionar Enter. O Kafka irá armazenar a mensagem no tópico e ela estará disponível para consumo.


### Consumindo mensagens do Kafka

Agora que temos um produtor enviando mensagens para o tópico "topico-teste", vamos criar um consumidor que irá ler essas mensagens. Para isso, vamos usar o comando kafka-console-consumer.sh:

```sh

./bin/kafka-console-consumer.sh \
--topic topico-teste \
--bootstrap-server localhost:9092

```

se enviarmos uma mensagem no produtor, ela será exibida no consumidor. O consumidor irá ler as mensagens do tópico e exibi-las no terminal.

```sh
~/dev/kafka_2.13-2.8.0$ bin/kafka-console-consume
r.sh --topic topico-teste --bootstrap-server localhost:9092
mensagem1
mensagem2
mensagem3
```

## Consumer groups

é possivel criar grupos de consumidores que consomen as mensagem de um mesmo tópico, masi com grupos diferente, isso é epsecilmenmte útils quando para balancear a carga e processamento paralelo, para criar um grupo de consumidores, basta usar a opção --group no comando kafka-console-consumer.sh:

```sh
./bin/kafka-console-consumer.sh \
--topic topico-teste \
--from-beginning \
--bootstrap-server localhost:9092 \
--consumer-property group.id=grupo-1
```

1. **--from-beginning**: indica que o consumidor deve ler as mensagens desde o início do tópico.
2. **--consumer-property group.id=grupo-1**: especifica o ID do grupo de consumidores. Isso permite que vários consumidores leiam mensagens do mesmo tópico, mas cada um em seu próprio grupo.

### Listando os grupos de consumidores

Para listar os grupos de consumidores, podemos usar o comando kafka-consumer-groups.sh:

```sh
./bin/kafka-consumer-groups.sh \
--bootstrap-server localhost:9092 \
--list
```

## Parando o Kafka

As informações publicadas no Kafka são armazenadas em disco, o que garante que elas não serão perdidas em caso de falha do sistema. Para parar o Kafka, basta usar o comando kafka-server-stop.sh e apagar o diretório de logs:

```sh

./bin/kafka-server-stop.sh

rm -rf /tmp/kraft-combined-logs
```


