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

Aplicamos as mudanças com:
```sh
kubectl apply -f k8s/deployment.yaml
```

Podemos ver nosso deployment set em:
```sh
kubectl get deployments
```

Podemos detalhar nosso deployment set em:
```sh
kubectl describe deployments kubernet-rails
```

Se quiser acessar a sua aplicação rode o comando:
```sh
kubectl get pods
```

```sh
kubectl port-forward pod/kubernetes-rails-fbb45d985-mc6c8 3000:3000
```

e abra em http://localhost:3000

# Rollout e revisões

Com o rollout, ao alterarmos a imagem ou outra configuração do deployment, ele reinicia os pods gradualmente, bem como permite, em conjunto com as revisions fazer rollbacks.
Uma nova revision é criada quando um deployment é alterado, assim podemos navegar entre versões.
Ex: Se subirmos algo defeituoso, podemos dar um rollback voltando para a versão anterior.

O comando para visualizar o status de um rollout é:
```sh
kubectl rollout status deployment kubernetes-rails
```

Para ver o histórico de mudanças de um Deployment:
```sh
kubectl rollout history deployment kubernetes-rails
```

Para fazer um rollback (voltar à versão anterior):
```sh
kubectl rollout undo deployment kubernetes-rails
```
Para ver as revisões disponíveis
```sh
kubectl rollout history deployment kubernetes-rails
```

Voltar para uma versão específica
```sh
kubectl rollout undo deployment kubernetes-rails --to-revision=2
```

# Service
O service expõe nossos pods atribuindo a eles um IP. Por meio desse IP ele distribui o tráfego para os pods disponíveis.
Os Services atuam como uma camada de abstração sobre os Pods, permitindo que outros serviços ou usuários acessem os Pods.

Tipos de Services no Kubernetes:

* ClusterIP: Nosso service fica disponível para comunicação interna. Ele cria um IP interno, o qu eé útil por exemplo para conectar um backend com um banco de dados. Mas o pod não fica acessível externamente.
* NodePort: Expõe o service para o mundo externo em uma porta específica.
* LoadBalancer: Cria um balanceador de carga que distribui os acessos entre os pods.
* ExternalName: redireciona o tráfego para um dominio externo.

### ClusterIP:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: "kubernetes-rails-service"
spec:
  selector:
    app: "kubernetes-rails"
  type: ClusterIP
  ports:
  - name: "kubernetes-rails-service"
    port: 3000
    targetPort: 3000
    protocol: TCP
```

Obs: o `port` é a porta do service que deve ser acessado. Já o `targetPort` é a porta do container. OU seja, acessamos nosso service pela `port` e ele redireciona para a `targetPort` do service.

Aplicamos as mudanças com:
```sh
kubectl apply -f k8s/service.yaml
```

Listar Services rodando no cluster
```sh
kubectl get services
```

Ver detalhes do Service
```sh
kubectl describe service kubernetes-rails-service
```

Deletar um Service
```sh
kubectl delete service kubernetes-rails-service
```

### NodePort

```yaml
apiVersion: v1
kind: Service
metadata:
  name: "kubernetes-rails-service"
spec:
  selector:
    app: "kubernetes-rails"
  type: NodePort
  ports:
  - name: "kubernetes-rails-service"
    port: 3000
    targetPort: 3000
    protocol: TCP
    NodePort: 30001
```

### LoadBalancer

Cria um IP externo para que os pods sejam acessados externamente. Sempre que quisermos liberar nossa aplicação para internet usamos o Load Balancer.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: "kubernetes-rails-service"
spec:
  selector:
    app: "kubernetes-rails"
  type: LoadBalancer
  ports:
  - name: "kubernetes-rails-service"
    port: 3000
    targetPort: 3000
    protocol: TCP
```

# Variáveis de ambiente

Nós já vimos como lidar com variáveis de ambiente por alto:

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

Veja que é passado um env, com um par de chave e valor, podendo passar quantos quiser.

### ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: "kubernetes-rails-env"
data:
  SECRET_KEY_BASE: "1b5566dab59a73a61c60637b180dfb4f08bdd30f4d2d32f1459cec3465a89934dd44c86d42c6027496a9a99e332e96f87c4f77c6f2db6f093454eb938b0416d6"
  DATABASE_NAME: "kubernets_development"
```

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
  replicas: 1
  template:
    metadata:
      name: "kubernetes-rails"
      labels:
        app: "kubernetes-rails"
    spec:
      containers:
        - name: "kubernetes-rails"
          image: "joaoneto123/rails-kubernets:v2"
          env:
            - name: SECRET_KEY_BASE
              valueFrom:
                configMapKeyRef:
                  name: "kubernetes-rails-env"
                  key: SECRET_KEY_BASE
            - name: DATABASE_NAME
              valueFrom:
                configMapKeyRef:
                  name: "kubernetes-rails-env"
                  key: DATABASE_NAME
```

ou simplesmente:

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
  replicas: 1
  template:
    metadata:
      name: "kubernetes-rails"
      labels:
        app: "kubernetes-rails"
    spec:
      containers:
        - name: "kubernetes-rails"
          image: "joaoneto123/rails-kubernets:v2"
          envFrom:
            - configMapRef:
              name: "kubernetes-rails-env"
```

# Health Check

Usamos o Health Check, ou verificação de saúde, para garantir que os pods e containers estejam funcionando corretamente.

Com isso nós conseguimos:
* Detectar e reiniciar containers travados ou com erro.
* Remover Pods com falha do balanceador de carga.
* Garantir que apenas containers prontos recebam tráfego.

# Probes
Os Probes, nos permite, automaticamente monitorar a saude dos containers dentro dos Pods. Assim nosso cluster sabe o momento de receber tráfego e quando reiniciar o container.

* Liveness Probe: Verifica se o nosso container está "vivo", se não estiver, reinicia.
* Readiness Probe: Verifica se o container está pronto para receber tráfego. (se não tiver pronto, ele será removido do service)

### Liveness Probe
O Liveness Probe, verifica se o container travou e não está respondendo. Se a verificação falhar, o kubernetes reinicia o container.

Aqui abaixo está um exemplo de implementação do Liveness Probe:

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
  replicas: 1
  template:
    metadata:
      name: "kubernetes-rails"
      labels:
        app: "kubernetes-rails"
    spec:
      containers:
        - name: "kubernetes-rails"
          image: "joaoneto123/rails-kubernets:v2"
          livenessProbe:
            httpGet:
              path: /healthz # endpoint da sua aplicação para o health check
              port: 3000
            periodSeconds: 5 # a cada cinco segundos verifica a saude da aplicação
            failureThreshold: 1 # quando der problema, passado 1 segundo ele tenta reiniciar 
            timeoutSeconds: 1 # se passar de 1 segundo para acessar a rota, ele da timeout
            successThreshold: 1 # quantas vezes ele tem que testar pra ter certeza da saude da app
          envFrom:
            - configMapRef:
              name: "kubernetes-rails-env"
```

Veja que o trecho de código é:

```yaml
livenessProbe:
  httpGet:
    path: /healthz # endpoint da sua aplicação para o health check
    port: 3000
  periodSeconds: 5 # a cada cinco segundos verifica a saude da aplicação
  failureThreshold: 1 # quando der problema, passado 1 segundo ele tenta reiniciar 
  timeoutSeconds: 1 # se passar de 1 segundo para acessar a rota, ele da timeout
  successThreshold: 1 # quantas vezes ele tem que testar pra ter certeza da saude da app
```

### Readiness Probe
Com o Readiness Probe, evitamos que um container que não está pronto, não receba tráfego. Se ele falhar, o Kubernetes vai remover ele do service, até que esteja pronto.
```yaml
readinessProbe:
  httpGet:
    path: /healthz # endpoint da sua aplicação para o health check
    port: 3000
  periodSeconds: 5 # a cada cinco segundos verifica a saude da aplicação
  failureThreshold: 1 # quando der problema, passado 1 segundo ele tenta reiniciar 
  timeoutSeconds: 1 # se passar de 1 segundo para acessar a rota, ele da timeout
  successThreshold: 1 # quantas vezes ele tem que testar pra ter certeza da saude da app
  initialDelaySeconds: 10 # ele vai esperar 10 segundos para o readiness começar a funcionar
```

### Readiness Probe + Liveness Probe
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
  replicas: 1
  template:
    metadata:
      name: "kubernetes-rails"
      labels:
        app: "kubernetes-rails"
    spec:
      containers:
        - name: "kubernetes-rails"
          image: "joaoneto123/rails-kubernets:v2"
          readinessProbe:
            httpGet:
              path: /healthz
              port: 3000
            periodSeconds: 3
            failureThreshold: 1
            initialDelaySeconds: 10
          livenessProbe:
            httpGet:
              path: /healthz
              port: 3000
            periodSeconds: 5
            failureThreshold: 1
            timeoutSeconds: 1
            successThreshold: 1
          envFrom:
            - configMapRef:
              name: "kubernetes-rails-env"
```

### Startup Probe

O Startup Probe é um tipo de probe no Kubernetes que verifica se um container já finalizou sua inicialização. Ele garante que o readiness e o liveness só irão iniciar quando a aplicação estiver pronta.
Abaixo podemos ver que em 90 minutos ele vai esperar a aplicação ficar pronta, depois disso o readiness e o liveness passam a valer.

```yaml
startupProbe:
  httpGet:
    path: /healthz # endpoint da sua aplicação para o health check
    port: 3000
  periodSeconds: 3 # a cada 3 segundos verifica a saude da aplicação
  failureThreshold: 30 # quando der problema, passado 1 segundo ele tenta reiniciar 
```

# Metrics Server

Quando usamos o kind, ele não vem com o metrics-server. O Metrics Server é um componente essencial para monitoramento no Kubernetes. Ele coleta e disponibiliza métricas de uso de CPU e memória dos Pods e Nodes.

 Principais funções:
* Permite o funcionamento do HPA (Horizontal Pod Autoscaler).
* Suporta comandos como kubectl top pod e kubectl top node.
* Fornece métricas para ferramentas de monitoramento (ex: Prometheus, Grafana).

Como Funciona o Metrics Server?

* O Metrics Server coleta métricas diretamente do Kubelet de cada Node.
* Os dados são armazenados apenas em memória (não são persistidos).
* Ele não fornece métricas históricas – para isso, use o Prometheus.

Com ele temos as métricas para fazer o autoscaling.

### Instalação do metrics-server para o kind
Primeiro vamos baixar o metrics server dentro da nossa pasta k8s:

```sh
wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Em seguida vamos renomear para metrics-server.yaml

Abaixo da linha 139, vamos adicionar um novo argumento para o container:
```yaml
- --kubelet-insecure-tls
```

Que ficará assim:

```yaml
containers:
  - args:
    - --cert-dir=/tmp
    - --secure-port=10250
    - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
    - --kubelet-use-node-status-port
    - --metric-resolution=15s
    - --kubelet-insecure-tls
```

Em seguida aplicar o yaml

```sh
kubectl apply -f k8s/metrics-server.yaml
```

Vamos verificar se já está instalado com:

```sh
kubectl get apiservices
```

Veja como deve ser o retorno:
```
kubectl get apiservices
NAME                              SERVICE                      AVAILABLE                  AGE
v1.                               Local                        True                       53s
v1.admissionregistration.k8s.io   Local                        True                       53s
v1.apiextensions.k8s.io           Local                        True                       53s
v1.apps                           Local                        True                       53s
v1.authentication.k8s.io          Local                        True                       53s
v1.authorization.k8s.io           Local                        True                       53s
v1.autoscaling                    Local                        True                       53s
v1.batch                          Local                        True                       53s
v1.certificates.k8s.io            Local                        True                       53s
v1.coordination.k8s.io            Local                        True                       53s
v1.discovery.k8s.io               Local                        True                       53s
v1.events.k8s.io                  Local                        True                       53s
v1.flowcontrol.apiserver.k8s.io   Local                        True                       53s
v1.networking.k8s.io              Local                        True                       53s
v1.node.k8s.io                    Local                        True                       53s
v1.policy                         Local                        True                       53s
v1.rbac.authorization.k8s.io      Local                        True                       53s
v1.scheduling.k8s.io              Local                        True                       53s
v1.storage.k8s.io                 Local                        True                       53s
v1beta1.metrics.k8s.io            kube-system/metrics-server   False (MissingEndpoints)   10s
v2.autoscaling                    Local                        True                       53s
```

Repare que ele está instalado como: `v1beta1.metrics.k8s.io`.

### Resources

No Kubernetes, resources são configurações que definem como os pods e containers utilizam os recursos do cluster, como CPU e memória. Isso é essencial para garantir que as aplicações rodem de maneira eficiente e estável, evitando o consumo excessivo de recursos por um único pod.

Tipos principais:

* CPU: Mede-se em millicores (m). Exemplo: 100m (0.1 core), 500m (0.5 core), 1 (1 core).
* Memória: Mede-se em bytes, mas normalmente usa-se MiB (Mebibytes) ou GiB (Gibibytes). Exemplo: 256Mi, 1Gi.

#### Requests e Limits
##### Requests

Define o valor mínimo de recursos que o container precisa para rodar. O Kubernetes usa esse valor para agendar o pod em um nó que tenha capacidade suficiente.

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "128Mi"
```

O container receberá pelo menos 250 millicores de CPU e 128Mi de memória.

##### Limits

Define o valor máximo de recursos que um container pode usar. Se ultrapassar esse valor, o Kubernetes pode limitar o consumo (no caso de CPU) ou matar o pod (no caso de memória).

```yaml
resources:
  limits:
    cpu: "500m"
    memory: "256Mi"
```

O container não pode ultrapassar 500 millicores de CPU e 256Mi de memória.

Obs:

* Se requests não for definido, o Kubernetes assume que o container não precisa de nada específico, podendo ser alocado de forma aleatória.
* Se limits não for definido, o container pode consumir o máximo de recursos disponíveis no nó, o que pode afetar outras aplicações.

Para monitorar os recursos podemos usar:

```sh
kubectl top pods
kubectl top nodes
```

Ou usar ferramentas como Prometheus + Grafana para uma visualização mais detalhada.

Veja como fica nosso deployment completo:

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
  replicas: 1
  template:
    metadata:
      name: "kubernetes-rails"
      labels:
        app: "kubernetes-rails"
    spec:
      containers:
        - name: "kubernetes-rails"
          image: "joaoneto123/rails-kubernets:v2"
          resources:
            requests:
              cpu: 100m
              memory: 20Mi
            limits:
              cpu: 500m
              memory: 25Mi
          startupProbe:
            httpGet:
              path: /healthz # endpoint da sua aplicação para o health check
              port: 3000
            periodSeconds: 3 # a cada 3 segundos verifica a saude da aplicação
            failureThreshold: 10 # quando der problema, passado 1 segundo ele tenta reiniciar 
          readinessProbe:
            httpGet:
              path: /healthz
              port: 3000
            periodSeconds: 3
            failureThreshold: 1
            initialDelaySeconds: 10
          livenessProbe:
            httpGet:
              path: /healthz
              port: 3000
            periodSeconds: 5
            failureThreshold: 1
            timeoutSeconds: 1
            successThreshold: 1
          envFrom:
            - configMapRef:
              name: "kubernetes-rails-env"
```

# HPA

O HPA (Horizontal Pod Autoscaler) é um recurso do Kubernetes que ajusta automaticamente o número de réplicas de Pods com base na demanda de CPU, memória ou métricas personalizadas.
Assim garantimos a escalabilidade automática e manteremos a aplicação responsiva sem desperdício de recursos.

O HPA monitora métricas dos Pods, como: 
* Uso de CPU
* Uso de memória
* Métricas personalizadas (exemplo: requisições HTTP por segundo)

Se a demanda aumenta → O Kubernetes cria mais réplicas do Pod.
Se a demanda diminui → O Kubernetes remove réplicas desnecessárias.

Vamos criar um arquivo de configuração do HPA

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: kubernetes-rails-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    name: kubernetes-rails # nome do deployment
    kind: Deployment
  minReplicas: 1 # em prod iremos usar no minimo 2
  maxReplicas: 5
  targetCPUUtilizationPercentage: 75
```
