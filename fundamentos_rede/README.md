# Fudamentos de Redes de computadores

## Modelo TCP/IP

O modelo TCP/IP (Transmission Control Protocol / Internet Protocol) é um conjunto de protocolos que define como dispositivos se comunicam em redes, incluindo a Internet. Ele é composto por quatro camadas, cada uma responsável por funções específicas na comunicação de dados.

**Camada de Aplicação (L7)**
  - Responsável por: Interação com os aplicativos e usuários finais.
  - Funções principais:
    * Define como os dados são representados para os programas e usuários.
    * Interpreta e processa os dados recebidos pela rede.
  - Protocolos comuns:
    * HTTP/HTTPS (Navegação na web)
    * FTP (Transferência de arquivos)
    * SMTP/POP3/IMAP (E-mails)
    * SSH (Acesso remoto seguro)
    * DNS (Resolução de nomes)

**Camada de Transporte (L4)**
  - Responsável por: Garantir que os dados cheguem corretamente e na ordem certa.
  - Funções principais:
    * Estabelece conexões entre dispositivos (handshake no TCP).
    * Divide os dados em segmentos e os reagrupa no destino.
    * Garante a entrega confiável dos pacotes (caso do TCP) ou entrega rápida sem garantia de ordem (caso do UDP).
  - Protocolos comuns:
    * TCP (Transmissão confiável, com controle de fluxo e correção de erros).
    * UDP (Menos confiável, mas mais rápido, usado para streaming e VoIP).

**Camada de Acesso à Rede (L3)**
  - Responsável por: Comunicação direta entre dispositivos físicos na mesma rede.
  - Funções principais:
    * Define como os dados são transmitidos fisicamente (via cabo, Wi-Fi, fibra óptica etc.).
    * Gerencia endereços MAC e controle de acesso à mídia (como CSMA/CD no Ethernet).
    * Realiza a formatação dos quadros de dados para transmissão.
  - Protocolos comuns: 
    * Ethernet
    * Wi-Fi (802.11)
    * ARP
    * PPP.



## Ciclo de vida de um pacote

1) Geração dos dados na camada de aplicação.
2) Segmentação na camada de transporte (TCP/UDP).
3) Encapsulamento em pacotes na camada de Internet (IP).
4) Encapsulamento em quadros na camada de acesso à rede (MAC, Ethernet, Wi-Fi).
5) Transmissão através do meio físico.
6) Roteamento entre redes até o destino final.
7) Desencapsulamento e entrega dos dados à aplicação correta.

#### Camada de Aplicação

##### Http
O HTTP (Hypertext Transfer Protocol) é um protocolo de comunicação usado na web para transferir dados entre um cliente (navegador, por exemplo) e um servidor. Ele é a base para a navegação na internet, permitindo a troca de páginas da web, imagens, vídeos e outros conteúdos.

Os métodos HTTP definem a ação da requisição. Os mais comuns são:

* GET: Solicita um recurso (exemplo: página HTML).
* POST: Envia dados ao servidor (exemplo: formulário).
* PUT: Atualiza um recurso existente.
* DELETE: Remove um recurso.
* PATCH: Atualiza parcialmente um recurso.
* HEAD: Igual ao GET, mas sem corpo na resposta (só cabeçalhos).
* OPTIONS: Retorna métodos suportados pelo servidor.

Códigos de Resposta HTTP

* Respostas de Sucesso (2xx)
* Redirecionamentos (3xx)
* Erros do Cliente (4xx)
* Erros do Servidor (5xx)

Requisições HTTP (HTTP Request) contêm método, URL, cabeçalhos e, opcionalmente, um corpo. Exemplo de uma requisição HTTP:

```
GET /index.html HTTP/1.1
Host: www.exemplo.com
User-Agent: Mozilla/5.0
Accept: text/html
```

Respostas HTTP (HTTP Response) contêm um código de status, cabeçalhos e, opcionalmente, um corpo com os dados.Exemplo de uma resposta Http

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1024

<html>
  <body><h1>Bem-vindo!</h1></body>
</html>
```

#### RTT
RTT (Round Trip Time) é o tempo que um pacote de dados leva para ir de um ponto A (cliente) até um ponto B (servidor) e voltar ao ponto A. Em outras palavras, é o tempo total de ida e volta de uma requisição na rede.

Como o RTT funciona?

* Um dispositivo (cliente) envia um pacote para outro dispositivo (servidor).
* O servidor recebe o pacote e responde.
* O cliente recebe a resposta.
* O tempo total desse ciclo é o RTT.

Fatores que afetam o RTT

* Distância física entre o cliente e o servidor.
* Congestionamento da rede, que pode causar atrasos.
* Latência do servidor, que pode demorar para processar a resposta.
* Roteadores e switches intermediários, que podem adicionar atrasos na transmissão.

Medição do RTT

* Uma maneira comum de medir o RTT é usando o comando ping, que envia pacotes ICMP para um servidor e mede o tempo de resposta.

Exemplo:

```sh
ping google.com

64 bytes from 142.250.217.78: icmp_seq=1 ttl=117 time=10.4 ms
``` 

O valor "time=10.4 ms" indica o RTT.

#### HTTP Connections: HTTP Persistent vs Non-persistent

Conexão persistente e não persistente são conceitos usados principalmente em redes e bancos de dados para descrever a maneira como as conexões são mantidas entre um cliente e um servidor.

**Conexão Não Persistente**
Uma conexão não persistente é aquela que fecha automaticamente após a transação ser concluída. Cada nova requisição precisa estabelecer uma nova conexão com o servidor.

* Libera recursos mais rapidamente, pois a conexão não fica ocupada o tempo todo.
* Pode ser mais segura em alguns cenários, pois conexões fechadas não podem ser reutilizadas por atacantes.
* Maior latência devido à necessidade de abrir e fechar conexões repetidamente.
* Pode sobrecarregar o servidor se muitas conexões forem criadas em um curto período de tempo.

**Conexão Persistente**
Uma conexão persistente é aquela que permanece aberta após uma transação ser concluída, permitindo que múltiplas requisições sejam feitas sem a necessidade de reestabelecer a conexão a cada vez.

* Reduz a sobrecarga de tempo e recursos, pois evita a necessidade de criar e fechar conexões repetidamente.
* Melhora o desempenho, especialmente em aplicações que fazem várias requisições consecutivas.
* Pode usar técnicas como keep-alive para manter a conexão ativa.

