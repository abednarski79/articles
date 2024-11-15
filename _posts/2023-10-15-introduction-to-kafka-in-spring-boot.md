---
layout: post
title: Introduction to Kafka in Spring Boot
date: 2023-10-15
---

Apache Kafka is a distributed event-streaming platform used for building real-time data pipelines and streaming applications. Spring Boot provides seamless integration with Kafka through the **spring-kafka** library, simplifying its usage in Java-based applications.

This article introduces how to use Kafka in a Spring Boot application, including examples of creating Kafka listeners and consumers using annotations.

---

## Setting Up Kafka in Spring Boot

### 1. Add Dependencies

To use Kafka with Spring Boot, include the following dependency in your `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

### 2. Kafka Configuration

Configure Kafka properties in `application.properties` or `application.yml`:

```properties
spring.kafka.bootstrap-servers=localhost:9092
spring.kafka.consumer.group-id=my-group
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer
```

---

## Creating Kafka Producer

A Kafka producer is responsible for sending messages to a Kafka topic.

### Example

#### Producer Configuration

You can configure the producer in the `@Configuration` class:

```java
import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;

import java.util.HashMap;
import java.util.Map;

@Configuration
public class KafkaProducerConfig {

    @Bean
    public ProducerFactory<String, String> producerFactory() {
        Map<String, Object> configProps = new HashMap<>();
        configProps.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        configProps.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        configProps.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        return new DefaultKafkaProducerFactory<>(configProps);
    }

    @Bean
    public KafkaTemplate<String, String> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
}
```

#### Sending Messages

Use `KafkaTemplate` to send messages to a topic:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Service;

@Service
public class KafkaProducerService {

    @Autowired
    private KafkaTemplate<String, String> kafkaTemplate;

    private static final String TOPIC = "my_topic";

    public void sendMessage(String message) {
        kafkaTemplate.send(TOPIC, message);
    }
}
```

---

## Creating Kafka Consumer

A Kafka consumer reads messages from a Kafka topic.

### Example

#### Consumer Configuration

You can configure the consumer in the `@Configuration` class:

```java
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.common.serialization.StringDeserializer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.annotation.EnableKafka;
import org.springframework.kafka.core.DefaultKafkaConsumerFactory;
import org.springframework.kafka.core.ConsumerFactory;
import org.springframework.kafka.listener.ConcurrentMessageListenerContainer;
import org.springframework.kafka.listener.ContainerProperties;

import java.util.HashMap;
import java.util.Map;

@EnableKafka
@Configuration
public class KafkaConsumerConfig {

    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        Map<String, Object> configProps = new HashMap<>();
        configProps.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        configProps.put(ConsumerConfig.GROUP_ID_CONFIG, "my-group");
        configProps.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        configProps.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        return new DefaultKafkaConsumerFactory<>(configProps);
    }
}
```

#### Creating a Listener

Use the `@KafkaListener` annotation to define a consumer:

```java
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Service;

@Service
public class KafkaConsumerService {

    @KafkaListener(topics = "my_topic", groupId = "my-group")
    public void listen(String message) {
        System.out.println("Received Message: " + message);
    }
}
```

---

## Testing the Kafka Integration

### Producing Messages

You can trigger the Kafka producer using a REST controller:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class KafkaController {

    @Autowired
    private KafkaProducerService kafkaProducerService;

    @GetMapping("/send")
    public String sendMessage(@RequestParam String message) {
        kafkaProducerService.sendMessage(message);
        return "Message sent: " + message;
    }
}
```

### Observing Consumer Output

Run your application and start a Kafka server. Send a message using the REST endpoint (e.g., `http://localhost:8080/send?message=HelloKafka`) and observe the output of the consumer in the console.

---

## Best Practices

1. **Use separate topics** for different kinds of data to avoid mixing unrelated messages.
2. **Set appropriate offset management**: Use `earliest` to process all messages or `latest` for new ones.
3. **Handle errors gracefully**: Implement error handlers for retries and logging.
4. **Monitor Kafka health**: Use tools like Prometheus and Grafana for monitoring Kafka brokers and consumer lag.

---

## Conclusion

Spring Boot's integration with Kafka simplifies both producing and consuming messages. With minimal configuration, you can set up a robust messaging system for real-time data processing. Start with this example to integrate Kafka into your applications and explore its full potential for event-driven architectures.
