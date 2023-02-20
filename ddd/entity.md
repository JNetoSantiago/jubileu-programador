### Entity

Entities (ou Entidades) são objetos que representam conceitos ou coisas do mundo real em um sistema de software. Em Domain-Driven Design (DDD), as entidades são uma das principais construções do modelo de domínio e são usadas para representar as coisas que possuem uma identidade e um ciclo de vida.

As entidades são caracterizadas por sua identidade única e inalterável, que é geralmente representada por um identificador, como um número de identificação único (ID) ou um código alfanumérico. Além disso, as entidades têm um conjunto de atributos ou propriedades que descrevem suas características e um conjunto de comportamentos que elas podem executar.

As entidades são diferentes de outros conceitos do modelo de domínio, como os value objects (objetos de valor), porque elas têm uma identidade única e persistente que não é baseada apenas em seus atributos. Por exemplo, um carro pode ter diferentes valores de cor, marca, modelo e ano, mas cada carro tem uma identidade única e persistente que o diferencia de todos os outros carros. Por outro lado, um objeto de valor, como um preço, é identificado apenas por seus atributos e não tem uma identidade única.

Em resumo, as entidades são um importante conceito em DDD e são usadas para modelar objetos do mundo real em um sistema de software, levando em consideração suas características e comportamentos específicos.

Aqui está um exemplo simples de uma classe Ruby que representa uma entidade "Usuário" com uma identidade única e alguns atributos básicos:

```ruby
class User
  attr_reader :id, :name, :email

  def initialize(id, name, email)
    @id = id
    @name = name
    @email = email
  end

  # Outros métodos e comportamentos do usuário podem ser adicionados aqui
end
```

Neste exemplo, a classe `User` tem três atributos: `id`, `name` e `email`. O atributo `id` representa a identidade única do usuário, enquanto `name` e `email` representam outras informações básicas do usuário.

O método `initialize` é usado para criar uma nova instância da classe `User`, definindo os valores iniciais para cada um dos atributos. A classe também inclui um método `attr_reader` para tornar os atributos somente leitura (read-only), o que significa que eles não podem ser modificados após a criação do objeto.

Obviamente, este é apenas um exemplo muito básico, e as entidades no mundo real geralmente são muito mais complexas e têm muitos outros atributos e comportamentos. Mas espero que isso dê uma ideia de como uma entidade pode ser representada em Ruby.

#### Entities e validação
No que diz respeito à validação das entidades, a abordagem mais comum é delegar a responsabilidade de validação para objetos externos, como Services (Serviços) ou Factories (Fábricas), em vez de fazer com que as entidades se auto validem.

Essa abordagem permite que as regras de negócio sejam centralizadas em um único lugar, o que facilita a manutenção e a evolução do sistema. Além disso, essa abordagem permite que as entidades se concentrem em sua responsabilidade principal, que é representar o conceito do mundo real, sem se preocupar com as regras de negócio ou validação.

No entanto, as entidades podem ter métodos que verificam se elas estão em um estado válido ou não. Esses métodos geralmente são usados internamente pela entidade para garantir a consistência de seu estado e podem ser úteis durante o desenvolvimento e testes.

Em resumo, embora as entidades possam ter métodos de validação, a responsabilidade final pela validação geralmente é delegada a outros objetos, para que as entidades possam se concentrar em sua responsabilidade principal de representar conceitos do mundo real em um sistema de software.

#### Classes anêmicas
No contexto do Domain-Driven Design (DDD), não há um conceito específico de "classe anêmica". No entanto, o termo "anemia" às vezes é usado para descrever uma classe que contém apenas dados sem comportamento correspondente. Em outras palavras, uma classe anêmica é uma classe que não tem métodos significativos além dos getters e setters.

No DDD, o objetivo é criar um modelo rico em comportamento que reflita o domínio da aplicação e as regras de negócios relevantes. Uma classe anêmica pode ser um indicador de que o modelo de domínio não está sendo expresso adequadamente ou que a lógica de negócios está sendo colocada em outros lugares, como em serviços ou controladores.

Para combater a anemia de uma classe, é preciso avaliar a semântica dos dados e identificar comportamentos que possam ser movidos para a classe. É importante lembrar que o DDD não se trata apenas de criar classes ricas em comportamento, mas sim de criar um modelo que reflita as regras de negócios e que seja fácil de entender e manter.

#### Exemplos de classes anêmicas e não anêmicas

Classe anêmica em Ruby:
```ruby
class Pessoa
  attr_accessor :nome, :idade, :endereco, :telefone
end
```

Esta classe tem apenas atributos e getters/setters, mas não tem comportamento significativo.

Exemplo de classe não anêmica em Ruby:

```ruby
class ContaBancaria
  def initialize(saldo_inicial)
    @saldo = saldo_inicial
  end

  def depositar(valor)
    @saldo += valor
  end

  def sacar(valor)
    if @saldo >= valor
      @saldo -= valor
    else
      raise "Saldo insuficiente."
    end
  end

  def saldo
    @saldo
  end
end
```

Neste exemplo, a classe ContaBancaria tem comportamentos significativos, como depositar, sacar e verificar o saldo da conta. Os atributos são usados internamente para armazenar o estado da conta e não são expostos publicamente por meio de getters/setters.