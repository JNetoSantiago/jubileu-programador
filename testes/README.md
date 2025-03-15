# Testes

Testes automatizados são scripts ou programas que verificam se uma aplicação funciona corretamente sem a necessidade de intervenção manual. Eles são escritos para testar diferentes partes do sistema, desde funções específicas até fluxos completos de uso.

Testar é essencial porque garante que o código funciona como esperado, evita regressões e melhora a qualidade da aplicação.

#### Benefícios dos testes automatizados

Evita Erros em Produção

* Testes identificam bugs antes que o código chegue aos usuários.
* Diminuem a chance de erros críticos derrubarem o sistema.

Facilita Manutenção e Refatoração 

* Se você mudar um código, os testes ajudam a garantir que nada quebrou.
* Refatorações podem ser feitas com mais segurança.

Acelera o Desenvolvimento

* Apesar de parecer que escrever testes leva tempo, eles economizam esforço no longo prazo.
* Encontrar e corrigir bugs manualmente é muito mais trabalhoso.

Documenta o Comportamento do Código

* Bons testes funcionam como documentação: mostram o que cada parte do código deve fazer.

Apoia o Desenvolvimento Colaborativo

* Novos devs podem entender melhor o sistema ao rodar os testes.
* Facilita revisão de código e integração de novas funcionalidades.


#### Principais tipos de testes automatizados:

* Testes Unitários – Testam partes pequenas do código (como métodos ou funções) isoladamente.
* Testes de Integração – Verificam se diferentes módulos da aplicação funcionam bem juntos.
* Testes Funcionais – Validam se a aplicação atende aos requisitos esperados pelo usuário.
* Testes de Aceitação – Simulam cenários reais para garantir que o software está pronto para produção.
* Testes de Regressão – Garantem que mudanças no código não quebraram funcionalidades existentes.
* Testes de Performance – Avaliam velocidade e escalabilidade do sistema.

###### O que são Testes Unitários?

Testes unitários são um tipo de teste automatizado que verifica partes pequenas e isoladas do código, geralmente funções ou métodos individuais, para garantir que elas funcionam corretamente.

Em Rails, isso significa testar métodos de models, helpers, services e até algumas partes de controllers de forma isolada, sem dependências externas como banco de dados ou APIs.

Principais características dos testes unitários:

* Rápidos – Executam em milissegundos, pois testam apenas um pequeno bloco de código.
* Isolados – Não dependem de banco de dados, arquivos ou APIs externas.
* Fáceis de depurar – Como testam uma unidade específica, fica fácil encontrar a origem do erro.

###### O que são Testes de Integração?

Testes de integração verificam se diferentes partes do sistema funcionam bem juntas. Eles garantem que múltiplos componentes – como models, controllers, e serviços – interagem corretamente, testando fluxos mais amplos da aplicação.

No Rails, isso significa testar requisições HTTP, comunicação com o banco de dados, autenticação, e até interações entre models.

Por que usar Testes de Integração?

* Garante que os componentes se comunicam corretamente
* Previne problemas entre models, controllers e serviços
* Ajuda a identificar falhas que testes unitários não cobrem

###### O que são Testes End-to-End (E2E)?

Testes End-to-End (E2E) verificam todo o fluxo de uma aplicação como um usuário real faria, garantindo que todos os componentes – frontend, backend, banco de dados e APIs – funcionem corretamente juntos.

Eles são mais abrangentes que testes de integração, pois simulam ações reais no sistema, como um usuário fazendo login, navegando em páginas e interagindo com botões e formulários.

Por que usar Testes E2E?

* Garante que todo o sistema funciona do início ao fim
* Simula interações reais, testando UI, API e banco de dados juntos
* Ajuda a prevenir bugs que só aparecem no ambiente real

Desvantagens

* Mais lentos – Precisam simular um navegador real.
* Mais complexos – Dependem de infraestrutura como Selenium ou Cypress.

#### Pirâmides de teste

Pirâmides de teste são um conceito em engenharia de software que ajuda a estruturar a estratégia de testes para garantir qualidade e eficiência. A ideia foi popularizada por Mike Cohn no livro Succeeding with Agile e sugere um modelo hierárquico onde testes mais rápidos e baratos são mais numerosos, enquanto testes mais lentos e caros são menos frequentes.

Estrutura da Pirâmide de Testes:

Testes Unitários (Base da Pirâmide)

* São os mais numerosos e rápidos.
* Testam partes isoladas do código (métodos ou funções).
* Exemplo: Testar um método que calcula descontos em um pedido.

Testes de Integração (Meio da Pirâmide)

* Verificam a comunicação entre diferentes partes do sistema.
* Exemplo: Testar se um repositório do banco de dados retorna corretamente um objeto esperado.

Testes de Interface Gráfica (Topo da Pirâmide)

* São mais lentos e custosos.
* Simulam interações reais de um usuário com o sistema.
* Exemplo: Testar o fluxo de um usuário adicionando um item ao carrinho e finalizando a compra.

Variações e Evoluções da Pirâmide:

* Pirâmide invertida (anti-padrão 🛑): Quando há muitos testes de interface gráfica e poucos testes unitários, tornando a suíte de testes lenta e frágil.
* Teste de Contrato: Em arquiteturas de microserviços, testes de contrato garantem que serviços se comuniquem corretamente.
* Pirâmide de Testes Moderna (Honeycomb 🐝): Considera testes exploratórios e monitoramento em produção.