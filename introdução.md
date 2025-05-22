# Introdução

## Normalmente interações entre sistema são executas de maneiras sincronas, ou seja, o cliente envia uma requisição e espera por uma resposta do servidor. No entanto nem toda operação pode ser executada dessa forma, em especiais em algum casos:

1. processamentos de longa duração
2. Integração com terceiros
3. processamento de eventos internos


## problemas de processamento assíncrono

1. dificuldade para depurar
2. aumento de complexidade

## O que é o Apache Kafka?

O apache kafka é uma feramento para processamentos de fluxos de evento que são representados de forma de mensagens. que é basicamente um fluxo de dados que pode ser lido e escrito por aplicações. tmeos 3 componentes principais:

1. **Produtores**: são as aplicações que produzem mensagens e as enviam para o Kafka.
2. **Topicos**: são os canais de comunicação onde as mensagens são publicadas e consumidas. Os tópicos são particionados, o que permite que as mensagens sejam distribuídas entre diferentes nós do cluster Kafka.
3. **Consumidores**: são as aplicações que consomem mensagens dos tópicos. Os consumidores podem ser agrupados em grupos de consumidores, o que permite que as mensagens sejam distribuídas entre diferentes instâncias de uma aplicação.


Essas são as três caracteristicas são comns a praticamnete todos os sismtea de fila, então quala vantagem do kafka sobre os outro sistemas de fila?, existem varias vantagem alguma das quais são comparticlhadas com outros sistemas de fila, como por exemplo:

1. **Durabilidade**: as mensagens são armazenadas em disco, o que garante que elas não serão perdidas em caso de falha do sistema.
2. **Paralelismo**: as mensagens podem ser processadas em paralelo, o que aumenta a eficiência do sistema.
3. **Balanceamento de carga**: o Kafka permite que as mensagens sejam distribuídas entre diferentes nós do cluster, o que garante que o sistema esteja sempre disponível e escalável.
4. **Streams**: o Kafka permite o processamento de fluxos de dados em tempo real, o que é útil para aplicações que precisam processar grandes volumes de dados em tempo real.

## O que é uma mensagem?

Toda mensage no kafka é formmada por 3 valores:
1. **Chave**: é um identificador único da mensagem, que pode ser usado para particionar as mensagens entre os nós do cluster.
2. **Valor**: é o conteúdo da mensagem, que pode ser qualquer tipo de dado.
3. **Timestamp**: é a hora em que a mensagem foi salva no tópico, que pode ser usado para ordenar as mensagens e garantir a entrega em tempo real.

```shell

# Exemplo de mensagem
{
  "key": "123",
  "value": "Hello, World!",
  "timestamp": 1633036800000
}
```
