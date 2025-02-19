# Kubernetes - jubileu e seus pods

Kubernetes é um orquestrador de containers. Ele também é chamado de k8s, sendo open source, ele automatiza a implantação, o gerenciamento e a escalabilidade de aplicações conteinerizadas.

Principais funcionalidades: 
* Gerenciamento automático de containers: Ele inicia, para e reinicia containers automaticamente conforme necessário.
* Escalabilidade: Aumenta ou reduz automaticamente a quantidade de containers de acordo com a demanda.
* Balanceamento de carga: Distribui o tráfego de rede entre os containers.
* Auto-recuperação: Reinicia containers que falham, substitui e redistribui cargas.
* Deploys e rollbacks: Permite realizar deploys sem downtime e reverter versões em caso de problemas.
* Armazenamento e volumes: Suporta armazenamento local, em nuvem ou distribuído.

Kubernets nos provê um conjunto de APIs, e podemos fazer chamadas a elas através do kuberctl.

Conceitos básicos do Kubernetes:
* Pod: A menor unidade do Kubernetes, pode conter um ou mais containers. 
* Node: Máquina física ou virtual onde os pods são executados.
* Cluster: Conjunto de nodes gerenciados pelo Kubernetes.
* Deployment: Define como os pods devem ser criados e atualizados.
* Service: Expõe aplicações para acesso dentro e fora do cluster.
* ConfigMap e Secret: Gerenciam configurações e credenciais de forma segura.

Para prosseguir, precisamos instalar o kubectl, segue o [link](https://kubernetes.io/pt-br/docs/tasks/tools/install-kubectl-linux/).

Em seguida iremos usar o Kind:

O Kind ou Kubernets in Docker, nos permite rodar o kubernetes localmente dentro de containers docker.
Veja como instalar o kind [aqui](https://kind.sigs.k8s.io/docs/user/quick-start#installation).

## ~/.kube/config
Antes de prosseguir precisamos entender sobre o arquivo ~/.kube/config.
Este arquivo, ~/.kube/config, é o arquivo de configuração do kubectl. Nele nós temos as informações como: credenciais, contextos e detalhes de acesso, que precisamos para nos conectarmos a um ou mais cluster kubernetes.

O ~/.kube/config, está em formato YAML e geralmente contem:
* Lista de clusters disponiveis e seus endpoints de API.
* Credenciais de usuário, como tokens, certificados ou chaves privadas.
* Contextos
* Contexto atual

Agora vamos ver alguns comandos úteis para gerenciar este arquivo:

Verificar o contexto atual:
```sh
kubectl config current-context
```

Listar os contextos disponíveis:
```sh
kubectl config get-contexts
```

Mudar para outro contexto
```sh
kubectl config use-context nome-do-contexto
```

Adicionar um novo cluster ao arquivo de configuração
```sh
kubectl config set-cluster meu-cluster --server=https://meu-cluster-endpoint:6443
```

Adicionar um usuário
```sh
kubectl config set-credentials meu-usuario --token=meu-token
```

Criar um novo contexto
```sh
kubectl config set-context meu-novo-contexto --cluster=meu-cluster --user=meu-usuario
```

Remover um contexto
```sh
kubectl config delete-context nome-do-contexto
```

## Criando um cluster no kind

Se quisessemos criar um cluster simples usando kind, poderiamos criar com o comando abaixo:
```sh
kind create cluster --name meu-cluster
```

Para criar um cluster com múltiplos nós podemos criar um arquivo `kind.yaml`:
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

Com o yaml acima criamos um cluster com:
* 1 nó de control-plane: Esse nó é o "cérebro" do Kubernetes. Ele gerencia o cluster e mantém o estado desejado dos aplicativos
* 2 nós worker: Os workers são responsáveis por executar os containers das aplicações.

Agora podemos criar nosso cluster rodando:
```sh
kind create cluster --name meu-cluster --config kind-config.yaml
```

Para verificar se o cluster foi criado podemos rodar:
```sh
kubectl get nodes
```

Se quisermos remover o cluster:
```sh
kind delete cluster --name meu-cluster
```

Usando o cluster:
O kind já adiciona o contexto do cluster no seu `~/.kube/config`, então você pode interagir com o cluster normalmente com `kubectl`. 
```sh
kubectl config current-context
```

E se precisar mudar para ou context:
```sh
kubectl config use-context kind-meu-cluster
```

## Criando uma aplicação rails
Vamos criar uma aplicação rails 7.1.5 para rodarmos dentro do kubernets:
```sh
rails new rails_kubernets
```

Agora vamos gerar o build da aplicação:
```sh
docker build -t joaoneto123/rails-kubernets .
```

Podemos testar a aplicação com:
```sh
docker run --rm -p 3000:3000 -e SECRET_KEY_BASE=$(bin/rails secret) joaoneto123/rails-kubernets
```

Vamos subir nosso build para o docker hub:


Vamos criar tambem um diretório chamado `k8s`, e nele iremos colocar as configurações necessárias para rodar a aplicação no kubernetes.

## Pods
Os pods são a menor unidade do kubernetes. Eles representam um ou mais containers que compartilham a mesma rede, armazenamento e configuração dentro do cluster.

Estrutura de um Pod: 
* Containers: normalmente teremos um único container por pod, mas podemos ter vários.
* Rede compartilhada: Todos os containers dentro do pod compartilham o mesmo IP e podem se comunicar entre si
* Armazenamento compartilhado: Pod pode montar volumes que são acessíveis em todos os containers que estão contidos nele.

Vamos colocar um Pod na nossa aplicação rails:
* OBS: Esta chave SECRET_KEY_BASE está sendo colocada aqui no momento apenas de exemplo.
k8s/pod.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: "kubernetes-rails"
  labels:
    app: "kubernetes-rails"
spec:
  containers:
    - name: "kubernetes-rails"
      image: "joaoneto123/rails-kubernets"
      env:
        - name: SECRET_KEY_BASE
          value: "1b5566dab59a73a61c60637b180dfb4f08bdd30f4d2d32f1459cec3465a89934dd44c86d42c6027496a9a99e332e96f87c4f77c6f2db6f093454eb938b0416d6"
```

Aplicamos as mudanças com:
```sh
kubectl apply -f k8s/pod.yaml
```

Ver detalhes do Pod:
```sh
kubectl describe pod kubernetes-rails
```

Ver logs do container no Pod:
```sh
kubectl logs kubernetes-rails
```

Acessar Shell de um pod:
```sh
kubectl exec -it kubernetes-rails -- /bin/sh
```

Deletar um pod:
```sh
kubectl delete pod kubernetes-rails
```

## ReplicaSet
O ReplicaSet nos permite configurar quantos pods devem estar sempre em execução, assim se um pod cair, ele automaticamente levanta outro no lugar automaticamente, sem precisar que façamos isso manualmente.

Com o yaml abaixo ele irá criar 5 pods rodando nossa aplicação: 
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: "kubernetes-rails"
  labels:
    app: "kubernetes-rails"
spec:
  selector:
    matchLabels:
      app: "kubernetes-rails"
  replicas: 5
  template:
    metadata:
      name: "kubernetes-rails"
      labels:
        app: "kubernetes-rails"
    spec:
      containers:
        - name: "kubernetes-rails"
          image: "joaoneto123/rails-kubernets"
          env:
            - name: SECRET_KEY_BASE
              value: "1b5566dab59a73a61c60637b180dfb4f08bdd30f4d2d32f1459cec3465a89934dd44c86d42c6027496a9a99e332e96f87c4f77c6f2db6f093454eb938b0416d6"
```

Explicando o YAML:

* replicas: 5 – O ReplicaSet irá garantir que 5 Pods com o container da nossa aplicação estejam sempre em execução.
* selector – Define como o ReplicaSet vai selecionar os Pods. Nesse caso, o matchLabels indica que o ReplicaSet deve gerenciar todos os Pods que possuem o label app: kubernetes-rails.
* template – Define o template para os Pods gerenciados. Dentro do template, temos o container da nossa aplicação.

Aplicamos as mudanças com:
```sh
kubectl apply -f k8s/replicaset.yaml
```

Podemos ver nosso replica set em:
```sh
kubectl get replicasets
```

E nossos pods rodando em:
```sh
kubectl get pods
```

## Deployment
O exemplo de replicaset anterior, foi apenas a título de conhecimento, na prática criaremos deployments para criar e gerenciar replicasets.

Como um Deployment usa ReplicaSets:

* O Deployment cria um ReplicaSet automaticamente.
* O ReplicaSet gerencia os Pods conforme especificado no template.
* O Deployment facilita a atualização de Pods de maneira controlada (como atualizar a versão do container).

Com o Deployment, sempre que nossa imagem for alterada, ele se encarregará de destruir os pods que tinhamos e cria-los novamente com a imagem atualizada.

A única coisa que vamos fazer é criar um arquivo deployment.yaml com o mesmo conteúdo do replicaset anterior, mas com uma alteração: o kind deverá ser Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: "kubernetes-rails"
  labels:
    app: "kubernetes-rails"
spec:
  selector:
    matchLabels:
      app: "kubernetes-rails"
  replicas: 5
  template:
    metadata:
      name: "kubernetes-rails"
      labels:
        app: "kubernetes-rails"
    spec:
      containers:
        - name: "kubernetes-rails"
          image: "joaoneto123/rails-kubernets"
          env:
            - name: SECRET_KEY_BASE
              value: "1b5566dab59a73a61c60637b180dfb4f08bdd30f4d2d32f1459cec3465a89934dd44c86d42c6027496a9a99e332e96f87c4f77c6f2db6f093454eb938b0416d6"
```