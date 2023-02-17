### Domain Event

"Use um evento de domínio para capturar uma ocorrência de algo que aconteceu no domínio." - Vaughn Vernon

De acordo com Martin Fowler, um evento de domínio é um tipo de evento que é gerado quando algo significativo acontece no seu sistema de negócios. Eles são usados para transmitir informações sobre mudanças no estado do domínio para outros componentes do sistema, como serviços, aggregate roots e outros eventos de domínio. Eventos de domínio são diferentes de eventos simplesmente técnicos, como logs de auditoria ou notificações, pois eles têm um impacto real e significativo no estado do negócio. Além disso, os eventos de domínio são um elemento-chave da arquitetura de sistemas baseados em eventos, que buscam lidar com mudanças dinâmicas e incertas no sistema de negócios.

É preciso entender que todo evento deve ser representado em uma ação realizada no passado:

* UserCreated
* OrderPlaced
* EmailSent

#### Quando devemos utilizar Domain Events?

Você deve usar eventos de domínio quando deseja transmitir informações sobre mudanças significativas no estado do seu sistema de negócios para outros componentes. Aqui estão alguns exemplos de quando usar eventos de domínio:

1.  Quando você deseja modelar a interação entre componentes do seu sistema: eventos de domínio permitem que você modelar a interação entre componentes de maneira clara e concisa.
2.  Quando você deseja garantir a consistência de dados: eventos de domínio podem ser usados para garantir a consistência de dados ao longo do sistema. Por exemplo, se um componente cria um novo registro, você pode gerar um evento de domínio para notificar outros componentes sobre a criação desse registro. 
3.  Quando você deseja adicionar novas funcionalidades sem afetar outros componentes: eventos de domínio permitem que você adicione novas funcionalidades sem afetar outros componentes do sistema, pois você pode adicionar um novo componente que ouça o evento de domínio e execute a ação desejada sem alterar o componente original.
4.  Quando você deseja aproveitar o poder da arquitetura baseada em eventos: eventos de domínio são um elemento-chave da arquitetura baseada em eventos e permitem que você aproveite seus benefícios, como a capacidade de lidar com mudanças dinâmicas e incertas.

Em resumo, você deve usar eventos de domínio quando deseja transmitir informações sobre mudanças significativas no estado do seu sistema de negócios para outros componentes e quando deseja modelar a interação entre componentes de maneira clara e concisa.

#### Quais são os components de um Domain Event?

Os componentes de um evento de domínio incluem:

1.  Event (O evento em si): o evento é a descrição da mudança significativa no estado do sistema de negócios. Ele pode incluir informações como a data e hora em que o evento ocorreu, o tipo de evento e os dados associados a ele.
3.  Event Dispatcher (O emissor do evento): o emissor do evento é o componente que gera o evento. É responsável por criar e publicar o evento para que outros componentes possam ouvir e reagir a ele.
4.  Handler (O destinatário do evento): o destinatário do evento é o componente que recebe e reage ao evento. É responsável por processar o evento e tomar as ações necessárias em resposta a ele. Podemos ter vários handlers para cada evento.
5.  O barramento de eventos: o barramento de eventos é o componente que gerencia a publicação e a entrega de eventos. Ele é responsável por transmitir o evento do emissor para o destinatário e garantir que o evento chegue a todos os destinatários interessados.

Em resumo, os componentes de um evento de domínio incluem o evento em si, o emissor do evento, o destinatário do evento e o barramento de eventos. Juntos, eles formam a infraestrutura que permite a comunicação e a colaboração entre componentes em um sistema baseado em eventos.

#### Passos a seguir para implementar Domain Event

Aqui estão os passos gerais para implementar um evento de domínio:

1.  Identifique a mudança significativa no estado do sistema: Antes de começar a implementar um evento de domínio, é importante identificar a mudança significativa no estado do sistema que você deseja representar como um evento.
2.  Defina o evento: Depois de identificar a mudança no estado do sistema, você precisa definir o evento. Isso inclui especificar o tipo de evento, a data e hora em que o evento ocorreu e os dados associados a ele.
3.  Implemente o emissor do evento: Depois de definir o evento, você precisa implementar o emissor do evento. O emissor do evento é o componente responsável por criar e publicar o evento.
4.  Implemente o manipulador do evento: Em seguida, você precisa implementar o manipulador do evento. O manipulador do evento é o componente responsável por processar o evento e tomar as ações necessárias em resposta a ele.
5.  Implemente o barramento de eventos: O barramento de eventos é o componente responsável por transmitir o evento do emissor para o manipulador. Você precisa implementar o barramento de eventos para garantir que o evento chegue a todos os manipuladores interessados.
6.  Publique o evento: Finalmente, você precisa publicar o evento. Isso envolve chamar o método de publicação no emissor do evento e passar o evento como um argumento.
7.  Teste o evento: Depois de implementar e publicar o evento, você precisa testá-lo para garantir que ele esteja funcionando corretamente.

Em resumo, esses são os passos gerais para implementar um evento de domínio. É importante notar que a implementação exata pode variar dependendo do framework ou biblioteca que você está usando. No entanto, esses passos gerais devem ajudá-lo a entender o processo geral de implementação de eventos de domínio.

#### Implementando Domain Event na prática (Em Ruby)

Aqui está uma implementação de exemplo de um evento de domínio em Ruby seguindo os passos que mencionei, perceba que existem várias maneiras de implementar eventos de domínio e que o código pode variar dependendo das suas necessidades:

1.  Identificar a mudança significativa no estado do sistema: Vamos supor que você deseje representar a criação de uma nova ordem como um evento de domínio.
2.  Defina o evento:

```rb
class OrderCreatedEvent
  attr_reader :order_id, :customer_name, :amount

  def initialize(order_id, customer_name, amount)
    @order_id = order_id
    @customer_name = customer_name
    @amount = amount
  end
end
```

3.  Implemente o emissor do evento:

```rb
class Order
  def initialize(order_id, customer_name, amount)
    @order_id = order_id
    @customer_name = customer_name
    @amount = amount
  end

  def create
    # Aqui você pode implementar a lógica de negócios de criar uma nova ordem

    # Em seguida, publique o evento OrderCreatedEvent
    event_bus.publish(OrderCreatedEvent.new(@order_id, @customer_name, @amount))
  end

  private

  def event_bus
    # Aqui você pode implementar o barramento de eventos
  end
end
```

4.  Implemente o manipulador do evento:

```rb
class OrderCreatedEventHandler
  def handle(event)
    # Aqui você pode implementar a lógica de negócios para lidar com o evento de criação de ordem
  end
end
```

5.  Implemente o barramento de eventos:

```rb
class EventBus
  def initialize
    @event_handlers = []
  end

  def subscribe(event_handler)
    @event_handlers << event_handler
  end

  def publish(event)
    @event_handlers.each do |handler|
      handler.handle(event) if handler.respond_to?(:handle)
    end
  end
end
```

6.  Publique o evento:

```rb
order = Order.new(1, "John Doe", 100)
order.create
```

7.  Teste o evento:

```rb
event_bus = EventBus.new
event_bus.subscribe(OrderCreatedEventHandler.new)

order = Order.new(1, "John Doe", 100)
order.create

# Aqui você pode implementar os testes para garantir que o evento OrderCreatedEvent tenha sido publicado corretamente e processado pelo manipulador OrderCreatedEventHandler
```

#### Considerações sobre o Event Bus

Event Bus é uma abstração que permite que componentes diferentes em um sistema publiquem e ouçam eventos. Ele é usado para implementar o padrão de arquitetura "Publish-Subscribe", que permite que vários componentes sejam notificados de eventos relevantes sem que eles precisem estar diretamente ligados.

Em outras palavras, o Event Bus é como uma linha de ônibus que permite que vários componentes possam viajar para o mesmo destino, mas não precisem se preocupar com os detalhes da rota ou como chegar lá. Em vez disso, eles apenas precisam se inscrever no Event Bus e esperar que o evento relevante chegue.

Na implementação de um Event Bus, os componentes interessados ​​no evento se registram com o bus e, quando o evento ocorre, ele é publicado no bus e os componentes inscritos são notificados. Isso permite que o acoplamento entre os componentes seja reduzido e que a flexibilidade e a escalabilidade do sistema sejam aumentadas.

#### Domain Events vs Messageria

Domain Events e sistemas de mensageria compartilham uma semelhança em que ambos permitem que componentes diferentes em um sistema publiquem e ouçam eventos. No entanto, existem diferenças importantes entre os dois.

Domain Events são eventos que ocorrem dentro de um sistema de aplicativos e são específicos do domínio da aplicação. Eles são usados ​​para comunicar mudanças no estado de entidades no sistema, como a criação, atualização ou exclusão de um registro. Eles são normalmente implementados como parte de uma arquitetura de microserviços e são geralmente processados localmente, sem a necessidade de envio para outros sistemas.

Sistemas de mensageria, por outro lado, são sistemas que permitem que componentes em diferentes aplicativos ou sistemas publiquem e recebam mensagens. Eles geralmente são implementados como sistemas de fila ou sistemas de mensagem em tempo real, e são usados ​​para integrar diferentes sistemas e aplicativos.

Em resumo, Domain Events são específicos para um sistema de aplicações, enquanto sistemas de mensageria são usados ​​para integrar diferentes sistemas. No entanto, Domain Events podem ser implementados com sistemas de mensageria, se necessário, para permitir a comunicação entre componentes em diferentes aplicativos ou sistemas.

#### Domain Events vs CQRS

No arquitetura CQRS (Command Query Responsibility Segregation), o Domain Event é um tipo especial de evento que representa uma mudança significativa no estado de um objeto do domínio. Eles são gerados pelo modelo de domínio em resposta a uma mudança de estado, por exemplo, ao salvar ou atualizar um objeto, e são usados para notificar outros componentes do sistema sobre essa mudança.

O papel do Domain Event é garantir a consistência do sistema, permitindo que outros componentes possam reagir e atualizar seus estados de acordo. Além disso, eles também são úteis para implementar funcionalidades que dependem de múltiplos componentes, como a sincronização entre diferentes partes do sistema, a persistência de eventos para auditoria e análise, entre outros.

Em resumo, o Domain Event é uma parte fundamental do CQRS, pois permite que os componentes do sistema comuniquem entre si de forma segura e confiável, garantindo a consistência e integridade dos dados.