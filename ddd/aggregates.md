### Aggregates

No contexto do Domain-Driven Design (DDD), um Aggregate (ou agregado) é um padrão de design que permite agrupar um conjunto de objetos relacionados em uma única unidade coesa de tratamento.

Um Aggregate é composto por uma Entidade Raiz (Root Entity) e um ou mais objetos relacionados, chamados de Entidades Filhas (Child Entities) e Objetos de Valor (Value Objects). A Entidade Raiz é a única entidade no Aggregate que pode ser referenciada por outras entidades fora do Aggregate, e é responsável por garantir a consistência e integridade dos objetos relacionados dentro do Aggregate.

A ideia por trás dos Aggregates é encapsular a complexidade da lógica de negócios em unidades mais simples e gerenciáveis, reduzindo a complexidade da aplicação como um todo. Além disso, os Aggregates permitem definir limites claros de transações, evitando que duas operações em Aggregates diferentes afetem um ao outro.

Um exemplo de Aggregate pode ser um sistema de vendas, onde uma Venda é a Entidade Raiz do Aggregate, que contém Entidades Filhas como Itens de Venda e Objetos de Valor como endereço de entrega e informações do cliente. Todos os objetos relacionados a uma venda são agrupados em um único Aggregate, garantindo a integridade e consistência dos dados da venda.

O conceito de Aggregates é um dos pilares do DDD, e seu uso é recomendado para desenvolvimento de sistemas complexos com regras de negócio bem definidas. Ao definir os limites de transações em Aggregates bem projetados, podemos obter sistemas mais eficientes, robustos e escaláveis.

#### Fatores de uma agregação

Existem alguns fatores que são importantes para definir uma agregação no contexto do Domain-Driven Design (DDD):

1.  Consistência: a agregação deve garantir que os objetos que a compõem estejam sempre em um estado consistente entre si. Por exemplo, em uma aplicação de pedidos online, a agregação de um pedido pode conter os itens do pedido, o cliente que fez o pedido e o endereço de entrega. Se um dos itens do pedido estiver em um estado inválido, a agregação como um todo deve ser considerada inválida.
2.  Limites de transação: a agregação deve ser uma unidade atômica em uma transação de banco de dados. Em outras palavras, todas as operações que afetam os objetos dentro da agregação devem ser executadas em uma única transação, de forma a garantir a consistência dos dados.
3.  Identidade: a agregação deve ter uma identidade clara e única. Isso significa que ela deve ter um identificador que a diferencie de outras agregações no sistema. Em muitos casos, esse identificador é um número ou uma cadeia de caracteres única.
4.  Coesão: a agregação deve representar uma unidade de negócio coesa. Isso significa que os objetos dentro da agregação devem estar relacionados de forma lógica e fazer sentido juntos. Por exemplo, em uma aplicação de compras online, pode ser natural agrupar os objetos relacionados a um pedido em uma agregação, já que eles são todos parte de uma transação única.
5.  Acoplamento: a agregação deve ser desacoplada de outras agregações e objetos do sistema. Isso significa que ela deve depender apenas de objetos internos à agregação e não depender de outros objetos do sistema. Isso ajuda a simplificar a lógica de negócio e tornar o sistema mais fácil de manter.

Ao considerar esses fatores, é possível definir Aggregates que são coesos, bem definidos e fáceis de manter e evoluir ao longo do tempo.

OBS: UTILIZE APENAS UM ÚNICO REPOSITÓRIO POR AGREGAÇÃO

#### Aplicação prática

```ruby
class Pedido
  attr_reader :id, :itens, :cliente, :endereco
  
  def initialize(id, cliente, endereco)
    @id = id
    @itens = []
    @cliente = cliente
    @endereco = endereco
  end
  
  def adicionar_item(produto, quantidade)
    item = ItemPedido.new(produto, quantidade)
    @itens << item
  end
  
  def total
    @itens.reduce(0) { |total, item| total + item.total }
  end
  
  def confirmar
    # Lógica para confirmar o pedido
  end
end

class ItemPedido
  attr_reader :produto, :quantidade
  
  def initialize(produto, quantidade)
    @produto = produto
    @quantidade = quantidade
  end
  
  def total
    @produto.preco * @quantidade
  end
end

class Cliente
  attr_reader :nome, :email
  
  def initialize(nome, email)
    @nome = nome
    @email = email
  end
end

class Endereco
  attr_reader :rua, :numero, :cidade, :estado, :cep
  
  def initialize(rua, numero, cidade, estado, cep)
    @rua = rua
    @numero = numero
    @cidade = cidade
    @estado = estado
    @cep = cep
  end
end
```

Neste exemplo, a classe `Pedido` representa a Entidade Raiz do Aggregate, que é composto por itens de pedido (objetos da classe `ItemPedido`), um cliente (objeto da classe `Cliente`) e um endereço de entrega (objeto da classe `Endereco`). A classe `ItemPedido` é uma Entidade Filha do Aggregate e a classe `Cliente` e `Endereco` são Objetos de Valor.

Note que a classe `Pedido` é responsável por manter a integridade dos objetos relacionados dentro do Aggregate. Por exemplo, a adição de um novo item de pedido é feita pelo método `adicionar_item`, que cria um objeto da classe `ItemPedido` e o adiciona à lista de itens do pedido (`@itens`). O cálculo do valor total do pedido é feito pelo método `total`, que itera sobre a lista de itens e soma os valores de cada item.

```ruby
cliente = Cliente.new("João da Silva", "joao@example.com")
endereco = Endereco.new("Rua A", 123, "São Paulo", "SP", "01234-567")
pedido = Pedido.new(1, cliente, endereco)
pedido.adicionar_item("Produto 1", 2)
pedido.adicionar_item("Produto 2", 3)
puts "Total do pedido: R$#{pedido.total}"
pedido.confirmar
```
Neste exemplo, criamos um novo cliente e endereço de entrega, em seguida, criamos um novo pedido com o ID `1`, o cliente e endereço criados anteriormente. Depois, adicionamos dois itens ao pedido, um com o produto "Produto 1" e quantidade 2, e outro com o produto "Produto 2" e quantidade 3. Em seguida, chamamos o método `total` para calcular o valor total do pedido e o método `confirmar` para confirmar o pedido.

É importante notar que, dependendo da complexidade do seu sistema, você pode precisar de estratégias mais avançadas para gerenciar os Aggregates. Em um sistema mais complexo, é comum ter vários Aggregates interagindo uns com os outros, e é necessário garantir a consistência dos dados entre eles. Nesses casos, pode ser útil utilizar um Framework de DDD, como o Event Sourcing ou CQRS, que fornecem mecanismos avançados de gerenciamento de Aggregates e Eventos.

Com um Aggregate bem projetado, é possível garantir a consistência e integridade dos dados e reduzir a complexidade da aplicação. Além disso, o uso de Aggregates pode ajudar a separar as responsabilidades de cada classe e tornar o código mais modular e fácil de manter.