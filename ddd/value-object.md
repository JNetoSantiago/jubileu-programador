### Value Object

Um Value Object (ou objeto de valor) é um conceito de programação orientada a objetos que descreve um tipo de objeto que representa um valor ou um conjunto de valores que é tratado como um todo e imutável. Em outras palavras, um Value Object é um objeto que não tem identidade própria, mas é definido pelo seu valor.

Um exemplo comum de Value Object é uma data, que é definida pelos valores dos seus campos (dia, mês e ano) e é imutável. Outros exemplos de Value Objects podem ser um endereço, um número de telefone, um CPF, entre outros.

Ao contrário de um Entity (entidade), que tem uma identidade única e é mutável, um Value Object não pode ser alterado depois de criado. Além disso, Value Objects geralmente não possuem métodos que alteram seu estado interno, mas podem ter métodos que retornam informações ou realizam cálculos baseados em seu valor.

Value Objects são frequentemente usados em design de software para melhorar a clareza, a manutenibilidade e a flexibilidade do código, já que ajudam a isolar as dependências entre objetos e tornam o código mais expressivo e semântico.

#### Exemplo prático

```ruby
class Endereco
  attr_reader :rua, :numero, :cidade, :estado, :cep
  
  def initialize(rua, numero, cidade, estado, cep)
    @rua = rua
    @numero = numero
    @cidade = cidade
    @estado = estado
    @cep = cep
  end
  
  # Método que retorna uma string formatada com o endereço completo
  def to_s
    "#{@rua}, #{@numero}, #{@cidade}, #{@estado} - #{@cep}"
  end
end
```

Neste exemplo, a classe `Endereco` representa um Value Object que encapsula os valores de um endereço. Os atributos da classe (`@rua`, `@numero`, `@cidade`, `@estado` e `@cep`) são definidos no construtor e são somente leitura (read-only), o que significa que o valor de cada atributo só pode ser definido uma vez, na criação do objeto.

O método `to_s` é um exemplo de um método que não altera o estado interno do objeto, mas retorna uma informação baseada no seu valor. Nesse caso, o método retorna uma string formatada com o endereço completo.

Com um objeto `Endereco`, podemos fazer o seguinte:
```ruby
endereco = Endereco.new("Rua A", 123, "São Paulo", "SP", "01234-567")
puts endereco.to_s
# Saída: "Rua A, 123, São Paulo, SP - 01234-567"
```

Note que, como um Value Object, o objeto `Endereco` não tem identidade própria e é definido somente pelos seus valores. Se criarmos outro objeto `Endereco` com os mesmos valores, teremos dois objetos distintos, mas iguais em termos de valor.
