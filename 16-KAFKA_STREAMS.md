# Kafka streams

O kafka streams é uma biblioteca do Apache Kafka que permite o processamento de fluxos de dados em tempo real. Ele fornece uma API de alto nível para criar aplicativos de streaming, permitindo que os desenvolvedores processem e analisem dados em movimento.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.santana</groupId>
	<artifactId>kafka-stream</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<dependencies>
		<dependency>
			<groupId>org.apache.kafka</groupId>
			<artifactId>kafka-streams</artifactId>
			<version>2.8.0</version>
		</dependency>
		<dependency>
			<groupId>org.apache.kafka</groupId>
			<artifactId>kafka-clients</artifactId>
			<version>2.8.0</version>
		</dependency>
		<dependency>
		    <groupId>com.google.code.gson</groupId>
		    <artifactId>gson</artifactId>
		    <version>2.8.9</version>
		</dependency>
		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			<version>1.18.34</version>
			<scope>provided</scope>
		</dependency>
	</dependencies>
</project>
```
