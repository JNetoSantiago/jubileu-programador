# RAbbitMQ (Traduzido do original)

#### Terminologia

O termo "publisher" (publicador) pode ter significados diferentes dependendo do contexto. No geral, em mensageria, um publisher (também chamado de producer, ou produtor) é uma aplicação (ou instância de uma aplicação) que publica (ou produz) mensagens. A mesma aplicação pode também consumir mensagens, tornando-se assim um consumer (consumidor) ao mesmo tempo.

Os protocolos de mensageria também possuem o conceito de assinatura duradoura para entrega de mensagens. O termo subscription (assinatura) é comumente usado para descrever essa entidade. Consumer (consumidor) é outro termo utilizado. Os protocolos de mensageria suportados pelo RabbitMQ utilizam ambos os termos, mas a documentação do RabbitMQ tende a preferir o segundo.
Conceitos Básicos

#### O básico

O RabbitMQ é um broker de mensagens. Ele recebe mensagens de publicadores, as encaminha e, caso existam filas para roteá-las, as armazena para consumo ou as entrega imediatamente aos consumidores, se houver algum disponível.

Os publicadores publicam para um destino, que varia de acordo com o protocolo:

* No AMQP 0-9-1, os publicadores enviam mensagens para exchanges.
* No AMQP 1.0, a publicação acontece em um link.
* No MQTT, os publicadores enviam mensagens para tópicos.
* No STOMP, há suporte para diferentes tipos de destino: tópicos, filas e exchanges AMQP 0-9-1.

Essas diferenças entre os protocolos são abordadas em mais detalhes na seção específica sobre protocolos.

Uma mensagem publicada precisa ser roteada para uma fila (ou um tópico, etc.). A fila (ou tópico) pode ter consumidores online. Se a mensagem for roteada com sucesso para uma fila e houver um consumidor disponível que possa recebê-la, a mensagem será enviada para esse consumidor.

Se houver uma tentativa de publicar em uma fila (ou tópico) inexistente, ocorrerá uma exceção no nível do canal com o código 404 Not Found, e o canal no qual a tentativa foi feita será fechado.