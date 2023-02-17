### Domain Service

Um Domain Service é um conceito de design de software que representa um serviço especializado que atua dentro do contexto de um domínio específico. O domínio em questão é a área de conhecimento ou expertise em que o software está sendo desenvolvido e, nesse sentido, um Domain Service é uma parte integrante da lógica de negócios que faz parte do domínio em questão.

Os Domain Services são responsáveis por executar operações complexas e/ou coordenar outras partes do sistema, geralmente interagindo com outras entidades do domínio para cumprir uma tarefa específica. Eles podem ser vistos como uma extensão das regras de negócios, encapsulando comportamentos complexos que não pertencem a nenhuma entidade específica do domínio.

Um Domain Service é geralmente implementado como uma classe ou um conjunto de classes que fornecem uma interface para outros componentes do sistema, permitindo que eles solicitem a execução de tarefas específicas que fazem parte do contexto do domínio. Em geral, os Domain Services são independentes de qualquer tecnologia específica, como banco de dados ou interface do usuário, tornando-os altamente reutilizáveis e flexíveis.

Eles também servem para coordenar a atividade dos agregados e repositórios com o propósito de implementar ação de negócio.

#### Aplicação prática
Aqui está um exemplo de como utilizar Domain Services em Ruby utilizando `dry-monads`, `Entities` e `Repositories`:

Suponha que temos um sistema de gerenciamento de usuários que permite criar novos usuários. Para criar um usuário, precisamos de algumas informações como nome, email e senha. No entanto, antes de criar um novo usuário, precisamos validar as informações para garantir que o usuário seja válido. Vamos criar um Domain Service que encapsula essa lógica:

1.  Primeiro, criamos nossa entidade `User`:
```ruby
# app/entities/user.rb
class User < Dry::Struct
  attribute :name, Types::Strict::String
  attribute :email, Types::Strict::String
  attribute :password, Types::Strict::String
end
```

2.  Em seguida, criamos nosso Repository `UserRepository` que é responsável por persistir e buscar usuários no banco de dados:

```ruby
# app/repositories/user_repository.rb
class UserRepository
  def create(user)
    # código para salvar o usuário no banco de dados
  end

  def find_by_email(email)
    # código para buscar um usuário no banco de dados por e-mail
  end
end
```

3.  Criamos nosso Domain Service `CreateUser` que é responsável por validar as informações do usuário e, se válido, criar um novo usuário no banco de dados:

```ruby
# app/services/create_user.rb
class CreateUser
  include Dry::Monads[:result]

  def initialize(params, user_repository: UserRepository.new)
    @params = params
    @user_repository = user_repository
  end

  def call
    user = User.new(@params)
    if user.valid?
      email_already_taken?(@params[:email]) ? Failure('Email already taken') : Success(@user_repository.create(user))
    else
      Failure(user.errors.full_messages.join(', '))
    end
  end

  private

  def email_already_taken?(email)
    @user_repository.find_by_email(email).present?
  end
end
```

Nesse exemplo, `CreateUser` é uma classe que define um método `call` que recebe um hash de parâmetros como argumento e retorna um monad `Success` ou `Failure`.

O método `call` começa criando uma nova instância da entidade `User` com os parâmetros fornecidos e verificando se a instância é válida usando o método `valid?` da entidade.

Se a instância for válida, o método verifica se o email já está sendo usado por outro usuário, utilizando o método `email_already_taken?` que busca por um usuário com o email fornecido no repositório de usuários.

Se o email já estiver sendo usado, o método retorna um monad `Failure` com a mensagem `'Email already taken'`. Caso contrário, o método chama o método `create` do repositório de usuários para salvar o novo usuário e retorna um monad `Success` com o usuário criado.

Se a instância não for válida, o método retorna um monad `Failure` com a mensagem de erro gerada pelo `ActiveModel::Errors` da entidade.

Dessa forma, utilizando `dry-monads`, `Entities` e `Repositories`, podemos separar claramente as responsabilidades de cada componente da nossa aplicação, facilitando a manutenção e evolução do código.