# Introdução ao Apache Kafka

Este repositório de uso pessoal tem como objetivo salvar minhas anotações sobre os conceitos e fundamentos do Apache Kafka, uma plataforma de streaming distribuída que permite a publicação, assinatura, armazenamento e processamento de fluxos de registros em tempo real.

## Referências

Como referência, utilizei o livro _Apache Kafka e Spring Boot: Comunicação Assíncrona entre Microsserviços_ - Eduardo Felipe Zambom Santana.

## Problemática

O protocolo mais utilizado hoje para comunicação web é o HTTP, que é baseado em requisições e respostas. Isso significa que o cliente envia uma requisição e espera por uma resposta do servidor. Existem alguns cenários em que isso não é o ideal, como quando, por algum motivo, o servidor não pode responder à requisição imediatamente. Isso pode ser agravado em cenários com retry ou timeout, em que o lado do cliente bloqueia recursos enquanto espera a resposta do servidor. Isso pode levar a problemas de desempenho e escalabilidade, especialmente em sistemas distribuídos.

O Apache Kafka é atualmente um dos principais sistemas para processamento assíncrono disponível, pois além de disponibilizar os tópicos para a produção e consumo de mensagens, também possui diversas funcionalidades que facilitam bastante a implementação de nossas aplicações, como a persistência de mensagens, o particionamento de dados e a replicação de tópicos. Além disso, o Kafka é altamente escalável e pode lidar com grandes volumes de dados em tempo real, o que o torna uma escolha popular para sistemas de streaming e processamento de eventos.

## Objetivo do livro

O objetivo principal deste livro é mostrar como implementar aplicações utilizando o Kafka para fazer comunicação assíncrona entre microsserviços, utilizando o Spring Boot como framework de desenvolvimento. O livro aborda desde os conceitos básicos do Kafka até a implementação de aplicações reais, com exemplos práticos e dicas para otimizar o desempenho e a escalabilidade das aplicações. Neste livro, aplicaremos Spring Boot com Kafka, implementando uma aplicação completa que será incrementada capítulo a capítulo, começando pelos conceitos mais básicos como criação dos tópicos e a produção e consumo de mensagens. Depois será mostrada a utilização das partes mais complexas do Kafka, como a utilização de consumidores para distribuição e balanceamento de carga das mensagens, o mecanismo de retentativa quando houver falhas no processamento de mensagens e processamento de fluxos de dados. Também serão mostradas algumas implementações interessantes em Python e testes utilizando Kafka.
