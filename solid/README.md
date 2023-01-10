# <center>Solid - Jubileu quer aprender mais sobre SOLID</center>

Em Engenharia de Software, SOLID é um acrônimo, que referencia 5 princípios voltados para a orientação a objetos, com o intuito de tornar seus códigos mais fáceis de entender, manter, reaproveitar, etc. Solid foi criado em um livro de Robert C. Martin (Uncle Bob), que se chama "Agile Software Development - Principle, Patterns and Pratices".

* **S. Single Responsability Principle** (Princípio da Resposabilidade única)
* **O. Open-closed Principle** (Princípio Aberto-Fechado)
* **L. Liskov Substituition Principle** (Princípio da substituição de Liskov)
* **I. Interface Segregation Principle** (Princípio da Segregação da Interface)
* **D. Dependency Inversion Principle** (Princípio da inversão da dependência)

Segure na pata do Jubileu, e vamos destrinchar todos eles:

1 - **S. Single Responsability Principle** (Princípio da Resposabilidade única): Uma classe deve ter um, e somente um, motivo para mudar

Cada classe deve possuir apenas uma única responsabilidade dentro de um software. Quando temos uma classe que executa várias ações, temos o que chamamos de God Class, ou classe Deus, esta classe, sabe demais, ela faz demais, desta maneira, se torna uma classe difícil de manter, pois teremos dificuldades ao fazer alterações na mesma, e se não tivermos testes então...

Vamos a um exemplo prático. Imagine a classe *Order* abaixo:

```rb
class Order
	def calculate_total_sum 
		...
	end

	def print_order
		...
	end
	
	def save
		...
	end
end
```

Esta classe está violando o Princípio da Responsabilidade Unica, uma vez que ela realiza três tipos distintos de tarefas, esta classe calcula o valor total do pedido, imprime o pedido e persiste o pedido no banco. 

Isto gera alguns problemas para nós, e são eles:
* Falta de coesão, uma vez que a classe *Order* no caso está assumindo responsabilidades que não deveriam ser suas.
* Alto acoplamento, porque aqui nós temos uma classe com muitas responsabilidades o que gera um número maior de dependências, veja como é dificil alterar o código de uma classe como esta, pois, será se alterando um trecho deste código, não irá "quebrar" outras partes da aplicação?
* Problema ao implementar testes, ora, aqui temos uma classe difícil de *mockar*, repare que quanto mais responsabilidades, mais complexo deverá ser o nosso teste.

Agora que estamos cientes do problema, vamos refatorar este código de modo que ele passe a respeitar o SRP?

```rb
class Order
	def calculate_total_sum 
		...
	end 
end

class OrderRepository
	def save 
		...
	end 
end

class OrderViewer
	def print_order 
		...
	end 
end
```

Repare que:
* Em vez de ter um único arquivo extenso de testes, agora eu posso ter três arquivos menores.
* Se eu precisar fazer alguma manutenção na forma como se calcula o valor total do pedido, não preciso mexer em códigos que não dizem respeito a esta funcionalidade.
* Meu código foi divido em partes menores, de mais fácil manutenção, é mais fácil encontrar um problema em um arquivo com 90 linhas de código do que com um de 2 mil linhas, não é verdade?

Então você se pergunta, e quando aos métodos? Eu deveria me preocupar com eles? Sim, de igual maneira também devemos aplicar o Principio da Responsabilidade Única aos métodos. Vamos ao código:

Imagine que você tem o método abaixo:

```rb
def send_notification(article_id)
	article = Article.find(article_id)
	
	article.authors.each do |author|
		if author.is_trusted
			author.notify
			author.update_article_counter
		end
	end
end
```

Precisamos refatorar isto, pois este código está complexo de manusear, há muita coisa acontecendo neste único método, veja que recebemos um *article_id*, depois recuperamos no banco este artigo, iteramos sobre os autores deste artigo e enviamos uma notificação caso o autor for confiável, para em seguida atualizar a contagem de artigos daquele usuário. Vamos refatorar?

```rb
def send_notification(article_id)
	article = find_article(article_id)

	notify_authors(article)
end

def find_article(article_id)
	article = Article.find(article_id)
end

def trusted_authors(authors)
	authors.map(&:is_trusted)
end

def notify_authors(article)
	authors = trusted_authors(article.authors)
	authors.each(&:notify)
end
```

Desta forma, imagine, se mudar meu ORM, basta alterar no método `find_article`, se mudar o nome da função que retorna se meu autor é confiável, basta mudar no método `trusted_authors`, e o mesmo com `notify_authors`. Repare que no fim, cada método tem um único propósito.