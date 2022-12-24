# <center>Rails - Jubileu quer criar um novo projeto rails</center>

### Jubileu está animado, e quer conhecer melhor o rails, para isso, ele decidiu criar um novo projeto para poder vender os ossos que não quer mais

```shell
bundle exec rails new desapega_osso -a propshaft -j esbuild --database postgresql --skip-test --css tailwind
```

Aqui, Jubileu está criando um novo projeto rails chamado `desapega_osso`, está usando algumas peculiaridades que vamos já comentar:

Antes, entenda um conceito importante, afinal, o que é asset pipeline?
Segundo a doc do rails: "O asset pipeline fornece uma estrutura para concatenar e reduzir ou compactar assets JavaScript e CSS. Ele também adiciona a capacidade de gravar esses assets em outras linguagens e pré-processadores, como CoffeeScript, Sass e ERB. Ele permite que assets em seu aplicativo sejam combinados automaticamente com assets de outras gems.". 
No rails o Asset Pipeline que vem por padrão é o Sprockets. Pronto!

### Propshaft (-a propshaft)

Estamos usando o propshaft em vez do padrão Sprockets. Propshaft é um asset pipeline para Rails, sendo mais rápido e mais simples que Sprockets, isto porque nos dias de hoje, Javascripts e CSS são compilados por empacotadores Node.js, e com o aumento de largura de banda, não temos uma grande pressão para minificar assets.
Com o Propshaft precisamos de transpiladores baseados em node como aqueles em: jsbundling-rails e cssbundling-rails.

### Esbuild (-j esbuild)

Em vez de usar-mos o importmap-rails padrão, estamos usando jsbundling-rails com esbuild para construir Javascript.

### Postgresql (--database postgresql)

Em vez de usar o sqlite por padrão, usaremos o postgre.

### Tailwind (--css tailwind)

Aqui, informamos que queremos utilizar nosso projeto com tailwindcss.

Pronto, temos um novo projeto rails criado, e nele, precisamos fazer mais algumas alterações, mas antes veja que no Gemfile, foram adicionadas as seguintes gems:

```ruby
gem "propshaft"
gem "turbo-rails"
gem "stimulus-rails" 
gem "jsbundling-rails" 
gem "cssbundling-rails"
```

Veja também que o conteúdo do nosso package.json:


```json
{
    "name": "app", 
    "private": "true", 
    "dependencies": {
        "@hotwired/stimulus": "^3.0.1", 
        "@hotwired/turbo-rails": "^7.1.1",
        "autoprefixer": "^10.4.4", 
        "esbuild": "^0.14.36", 
        "postcss": "^8.4.12", 
        "tailwindcss": "^3.0.24"
    }, 
    "scripts": {
        "build": "esbuild app/javascript/*.* --bundle --sourcemap --outdir=app/assets/builds",
        "build:css": "tailwindcss -i ./app/assets/stylesheets/application.tailwind.css -o ./app/assets/builds/application.css"
    }
}
```
Sim, ainda precisaremos voltar aqui, para adicionar mais alguns pacotes e configurações do Typescript.

Foi criado tambem, o tailwind.config.js

```json
module.exports = {
    content: [
        "./app/views/**/*.html.erb", 
        "./app/helpers/**/*.rb", 
        "./app/javascript/**/*.js",   
        "./config/initializers/simple_form_tailwind.rb",
    ], 
}
```
Nele, temos um campo chamado content, onde informamos, que o tailwind vasculhe e mapeie possiveis classes para incluir no nosso pacote CSS final.

Veja `application.tailwind.css`, é onde inicializamos as classes básicas do Tailwind.

```css
@tailwind base; 
@tailwind components; 
@tailwind utilities;
```

Antes de rodar a aplicação, veja os arquivos `Procfile.dev` e `bin/dev`:

Repare que aqui, no `Procfile.dev`, três processos são criados:
* web: sobe o servidor da nossa aplicação na porta 3000
* js: executa o yarn para buildar nosso javascript
* css: executa o yarn para buildar nosso CSS
```
web: bin/rails server -p 3000
js: yarn build --watch
css: yarn build:css --watch
```

O `Procfile.dev` é executado pelo `foreman`, no nosso inicializador `bin/dev`, verificamos se a `gem` do `foreman` está instalada, se não instalamos a mesma. Veja que em seguida mandamos o `foreman` executar o `Procfile.dev`.
```
#!/usr/bin/env bash
if ! command -v foreman &> /dev/null
then
  echo "Installing foreman..."
  gem install foreman
fi
foreman start -f Procfile.dev
```

Podemos subir o servidor com: `bin/dev`

Você disse tipos?

Jubileu ainda não está satisfeito, ele quer tipar seus códigos Javascript, para isso, ele decidiu incluir no seu setup o Typescript:

```shell
$ yarn add --dev typescript tsc-watch
$ yarn add --dev @typescript-eslint/parser
$ yarn add --dev @typescript-eslint/eslint-plugin
```

Veja que adicionamos o pacote `tsc-watch`, isto foi necessário, porque o esbuild não executa o compilador Typescript, para ver se nosso código é type-safe.

Jubileu então criou e configurou na raiz de seu projeto um arquivo chamado `tsconfig.json`, com as configurações do nosso Typescript:

```json
{
    "compilerOptions": {
        "declaration": false,
        "emitDecoratorMetadata": true,
        "experimentalDecorators": true,
        "lib": ["es2019", "dom"],
        "jsx": "react",
        "module": "es6",
        "moduleResolution": "node",
        "baseUrl": ".",
        "paths": {
            "*": ["node_modules/*", "app/packs/*"] 
        },
        "sourceMap": true,
        "target": "es2019",
        "noEmit": true
    },
    "exclude": ["**/*.spec.ts", "node_modules", "vendor", "public"],
    "compileOnSave": false
}
```

Assim como fez alterações na chave scripts do nosso package.json:

```json
"build:js": "esbuild app/javascript/*.* --bundle --sourcemap --outdir=app/assets/builds",
"build:css": "tailwindcss -i ./app/assets/stylesheets/application.tailwind.css -o ./app/assets/builds/application.css",
"failure:js": "rm ./app/assets/builds/application.js && rm ./app/assets/builds/application.js.map", 
"dev": "tsc-watch --noClear -p tsconfig.json --onSuccess \"yarn build:js\" --onFailure \"yarn failure:js\""
```

Veja que:

* noClear, impede que o tsc-watch limpe a janela do console.
* -p tsconfig.json, aponta para o arquivo de configuração TypeScript que controla a compilação que desejo fazer.
* --onSuccess \"yarn build:js\", controla o que você deseja que aconteça se a compilação do TypeScript for bem-sucedida. Em nosso caso, queremos que o build:js aconteça, pois agora sabemos que o código é seguro para o tipo.
* --onFailure \"yarn failed:js\", controla o que você deseja que aconteça na falha de compilação do TypeScript. Acho que tinha opções aqui, mas o que escolhi fazer foi o script failed.js, ou seja, rm ./app/assets/builds/application.js && rm ./app/assets/builds/application.js.map, removendo os arquivos esbuild existentes do diretório de construção para que a página do navegador de desenvolvimento apresente erros em vez de retornar as compilações bem-sucedidas mais recentes.

Foi necessário alterar tambem o Procfile.dev, veja que agora, o processo `js` roda um `yarn dev`

```
web: unset PORT && bin/rails server
js: yarn dev
css: yarn build:css --watch
```
