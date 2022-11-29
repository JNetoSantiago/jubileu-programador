# <center>Git - Jubileu quer versionar bem seus códigos</center>

<p>&nbsp;</p>
<p align='center'>
  <img src="https://raw.githubusercontent.com/JNetoSantiago/jubileu-tutoriais/main/git_handbook/flow.png" width="500" />
</p>
<p align='center'><strong>Na figura acima nós temos um exemplo de uso do git flow.</strong></p>
<p>&nbsp;</p>

Com o git flow, nós não damos um push diretamente no master. Mas criamos a branch develop que irá nos ajudar no gerenciamento do nosso workflow:
```shell
git checkout -b develop
```


Para cada feature nova em que o programador está trabalhando, ele cria uma branch, como por exemplo:
```shell
git checkout -b feature/register
```

Perceba, que criamos a feature obedecendo um padrão, com o sufixo feature: `feature/register`.

Em seguida, assim que concluímos esta tarefa, podemos dar um merge com a develop e depois de mudar para a develop, apagar esta branch:
```shell
git merge develop
git checkout develop

git branch --delete feature/register
```


Antes de mandar o código da branch develop para a branch master, precisamos mandar para release, por exemplo, imagine que estamos terminando um sprint, concluindo uma entrega, podemos criar uma branch release com aquelas funcionalidades referente aquele sprint, desta forma, eu consigo trabalhar ainda na develop, porém já com a release separada. Veja que precisamos criar uma branch nova, com o sufixo release/ seguido da versão daquela release, considere que estamos criando esta branch a partir da branch develop:
```shell
git checkout -b release/1.0.1
```


Assim que nossa release estiver pronta para ir pra produção, podemos dar um merge na develop e na main, assim como criar uma tag para a main.
```shell
git checkout develop
git merge release/1.0.1

git checkout master
git merge release/1.0.1
git tag -a 0.1.0 -m "Tagging version 0.1.0"

git branch --delete release/1.0.1
```


Entretanto, caso tenhamos um bug na master, devemos criar uma branch com o prefixo hotfix:
```shell
git checkout hotfix/register
```

Depois de fazermos as devidas correções, devemos dar um merge para atualizar tanto a branch master, quanto a develop:
```shell
git checkout develop
git merge hotfix/register

git checkout master
git merge hotfix/register
git tag -a 0.1.1 -m "Tagging version 0.1.1"

git branch --delete hotfix/register
```
