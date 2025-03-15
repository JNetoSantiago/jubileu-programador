# Elastic Search

O Elasticsearch é um mecanismo de busca e análise distribuído, baseado no Lucene, usado para armazenar, buscar e analisar grandes volumes de dados rapidamente. Ele é comumente utilizado para pesquisa full-text, análise de logs, monitoramento e aplicações que exigem respostas rápidas para consultas complexas.

### Principais Características

* Pesquisa full-text poderosa: suporta buscas avançadas, como fuzzy search, autocompletar, faceted search, etc.
* Alta performance: foi projetado para lidar com grandes volumes de dados e consultas rápidas.
* Distribuído e escalável: pode ser distribuído entre vários nós para melhor performance e redundância.
* API RESTful: interage via JSON e HTTP, facilitando integração com outras aplicações.
* Agregações: permite análises avançadas dos dados armazenados.


### Casos de Uso

* Motores de busca (como o da Wikipédia e e-commerces).
* Análise e monitoramento de logs (muitas vezes junto ao Kibana e Logstash, formando a stack ELK).
* Indexação e busca de grandes volumes de documentos.
* Sistemas de recomendação e análise de dados em tempo real.

### Cluster, Indices e Documentos

No Elasticsearch, os conceitos de cluster, índice e documento são fundamentais para entender como ele armazena e organiza dados.

##### Cluster

Um cluster é um conjunto de nós (servidores) que trabalham juntos para armazenar e processar dados no Elasticsearch. Ele é responsável por distribuir a carga e garantir alta disponibilidade.

Exemplo:

* Um cluster pode ter vários nós (servidores), e os dados são distribuídos entre eles.
* Se um nó falhar, outro pode assumir, garantindo resiliência.
* Cada cluster tem um nome único (por padrão, "elasticsearch").

##### Índice (Index)

Um índice é como um banco de dados no Elasticsearch. Ele agrupa documentos relacionados e define como eles serão armazenados e buscados.

Principais Características:

* Um cluster pode conter vários índices.
* Cada índice é composto por shards (partições de dados).
* Índices são definidos por mappings, que especificam os tipos de dados de cada campo.

Exemplo de Índices:

* products: Armazena informações sobre produtos.
* users: Contém dados dos usuários.
* logs-2025-03: Índice de logs para um determinado período.

##### Documento (Document)

Um documento é a menor unidade de dados no Elasticsearch, armazenado em formato JSON dentro de um índice.

Características:

* Cada documento tem um ID único dentro do índice.
* Documentos são organizados em índices, semelhantes a tabelas em bancos relacionais.
* NoSQL: Os documentos podem ter estruturas flexíveis, sem esquema fixo.

### Indice invertido

O índice invertido é a estrutura de dados principal usada pelo Elasticsearch (e pelo Lucene) para tornar buscas de texto extremamente rápidas e eficientes.

* Em vez de armazenar documentos de forma sequencial como um banco relacional, ele mapeia palavras para os documentos onde aparecem.
* Isso acelera consultas, pois permite localizar rapidamente os documentos que contêm um termo específico.

Como Funciona?

1) O Elasticsearch analisa os textos dos documentos e os divide em tokens (palavras, números, etc.).
2) Ele cria um mapeamento de palavras → documentos.
3) Na hora da busca, ele verifica quais documentos contêm os termos desejados, sem precisar percorrer tudo.