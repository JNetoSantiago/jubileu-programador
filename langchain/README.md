### Model I/O

O elemento central de qualquer aplicação de modelo de linguagem é... o modelo. O LangChain fornece a você os blocos de construção para interagir com qualquer modelo de linguagem.

* Recursos: Transformar em templates, selecionar dinamicamente e gerenciar entradas de modelo
* Modelos de linguagem: Fazer chamadas para modelos de linguagem através de interfaces comuns
* Analisadores de saída: Extrair informações a partir das saídas do modelo

<p>&nbsp;</p>
<p align='center'>
  <img src="https://raw.githubusercontent.com/JNetoSantiago/langchain/model_01.png" width="250" />
</p>

## Prompts

A nova forma de programar modelos é através de "prompts". Um prompt refere-se à entrada para o modelo. Essa entrada é frequentemente construída a partir de vários componentes. O LangChain fornece diversas classes e funções para tornar a construção e o trabalho com "prompts" mais fácil.

* Modelos de prompt: Parametrizam as entradas do modelo
* Seletores de exemplos: Selecionam dinamicamente exemplos para incluir nos "prompts"

# Prompt templates

Os modelos de linguagem recebem texto como entrada - esse texto é comumente referido como "prompt". Geralmente, isso não é simplesmente uma sequência de texto rígida, mas sim uma combinação de um modelo, alguns exemplos e a entrada do usuário. O LangChain fornece várias classes e funções para facilitar a construção e o trabalho com "prompts".

O que é um modelo de prompt?
Um modelo de prompt refere-se a uma forma reproduzível de gerar um "prompt". Ele contém uma sequência de texto ("o modelo"), que pode receber um conjunto de parâmetros do usuário final e gerar um "prompt".

Um modelo de prompt pode conter:

instruções para o modelo de linguagem,
um conjunto de exemplos de poucas palavras para ajudar o modelo de linguagem a gerar uma resposta melhor,
uma pergunta para o modelo de linguagem.

Aqui está um exemplo simples:

```js
import { PromptTemplate } from "langchain/prompts";

const prompt = PromptTemplate.fromTemplate(`You are a naming consultant for new companies.
What is a good name for a company that makes {product}?`
);

const formattedPrompt = await prompt.format({
  product: "colorful socks",
});

/*
  You are a naming consultant for new companies.
  What is a good name for a company that makes colorful socks?
*/
```

Criar a prompt template
Você pode criar "prompts" simples codificados diretamente usando a classe PromptTemplate. Os modelos de "prompt" podem aceitar qualquer número de variáveis de entrada e podem ser formatados para gerar um "prompt".

```js
import { PromptTemplate } from "langchain/prompts";

// An example prompt with no input variables
const noInputPrompt = new PromptTemplate({
  inputVariables: [],
  template: "Tell me a joke.",
});
const formattedNoInputPrompt = await noInputPrompt.format();

console.log(formattedNoInputPrompt);
// "Tell me a joke."

// An example prompt with one input variable
const oneInputPrompt = new PromptTemplate({
  inputVariables: ["adjective"],
  template: "Tell me a {adjective} joke."
})
const formattedOneInputPrompt = await oneInputPrompt.format({
  adjective: "funny",
});

console.log(formattedOneInputPrompt);
// "Tell me a funny joke."

// An example prompt with multiple input variables
const multipleInputPrompt = new PromptTemplate({
  inputVariables: ["adjective", "content"],
  template: "Tell me a {adjective} joke about {content}.",
});
const formattedMultipleInputPrompt = await multipleInputPrompt.format({
  adjective: "funny",
  content: "chickens",
});

console.log(formattedMultipleInputPrompt);
// "Tell me a funny joke about chickens."
```

Se você não deseja especificar manualmente as inputVariables, também pode criar um PromptTemplate usando o método de classe fromTemplate. O LangChain irá inferir automaticamente as inputVariables com base no modelo fornecido.

```js
import { PromptTemplate } from "langchain/prompts";

const template = "Tell me a {adjective} joke about {content}.";

const promptTemplate = PromptTemplate.fromTemplate(template);
console.log(promptTemplate.inputVariables);
// ['adjective', 'content']
const formattedPromptTemplate = await promptTemplate.format({
  adjective: "funny",
  content: "chickens",
});
console.log(formattedPromptTemplate);
// "Tell me a funny joke about chickens."
```

Você pode criar modelos de prompt personalizados que formatam o prompt da maneira que desejar. Para obter mais informações, consulte "Custom Prompt Templates".

Modelo de prompt para chat:
Modelos de chat recebem uma lista de mensagens de chat como entrada - essa lista é comumente referida como "prompt". Essas mensagens de chat diferem de sequências de texto simples (que você passaria para um modelo de linguagem) porque cada mensagem está associada a um papel.

Por exemplo, na API de Completamento de Chat da OpenAI, uma mensagem de chat pode ser associada a um papel de AI, humano ou sistema. O modelo deve seguir mais de perto as instruções provenientes das mensagens do sistema.

O LangChain fornece vários modelos de prompt para facilitar a construção e o trabalho com "prompts" de chat. É recomendado que você use esses modelos de prompt relacionados a chat em vez do PromptTemplate ao consultar modelos de chat para explorar totalmente o potencial do modelo subjacente.

Aqui está um exemplo de importação e uso dos modelos de prompt:

```js
import {
  ChatPromptTemplate,
  PromptTemplate,
  SystemMessagePromptTemplate,
  AIMessagePromptTemplate,
  HumanMessagePromptTemplate,
} from "langchain/prompts";
import {
  AIMessage,
  HumanMessage,
  SystemMessage,
} from "langchain/schema";
```

Criando um modelo de mensagem associado a um papel usando o correspondente <ROLE>MessagePromptTemplate.

```js
const template = "Você é um assistente prestativo que traduz {input_language} para {output_language}.";
const systemMessagePrompt = SystemMessagePromptTemplate.fromTemplate(template);

const humanTemplate = "{text}";
const humanMessagePrompt = HumanMessagePromptTemplate.fromTemplate(humanTemplate);
```

Construindo o modelo de mensagem diretamente usando o PromptTemplate.

```js
const prompt = new PromptTemplate({
  template: "Você é um assistente prestativo que traduz {input_language} para {output_language}.",
  inputVariables: ["input_language", "output_language"],
});
const systemMessagePrompt2 = new SystemMessagePromptTemplate({
  prompt,
});
```

Construindo um modelo de chat a partir de um ou mais MessagePromptTemplates.

```js
const chatPrompt = ChatPromptTemplate.fromPromptMessages([systemMessagePrompt, humanMessagePrompt]);

// Formate as mensagens
const formattedChatPrompt = await chatPrompt.formatMessages({
  input_language: "Inglês",
  output_language: "Francês",
  text: "Eu amo programar.",
});

console.log(formattedChatPrompt);

/*
  [
    SystemMessage {
      content: 'Você é um assistente prestativo que traduz Inglês para Francês.'
    },
    HumanMessage {
      content: 'Eu amo programar.'
    }
  ]
*/
```

Observe que o exemplo acima utiliza a biblioteca "langchain" e usa alguns modelos de prompt, como SystemMessagePromptTemplate e HumanMessagePromptTemplate, para construir um modelo de chat. O objeto resultante "formattedChatPrompt" contém uma lista de mensagens formatadas que podem ser usadas como entrada para um modelo de linguagem ou de chat.

# Partial prompt templates

Os "partial prompt templates" são uma forma de criar modelos de prompt que aceitam apenas um subconjunto das variáveis necessárias, permitindo que você insira valores parciais antes de ter todas as variáveis disponíveis. O LangChain suporta duas formas de fazer isso:

Partial com strings:
Este método é útil quando você tem algumas variáveis disponíveis antes de outras. Por exemplo, se você tiver um modelo de prompt que requer duas variáveis, "foo" e "baz", e obtiver o valor de "foo" primeiro, pode ser incômodo esperar até ter ambas as variáveis disponíveis para passá-las ao modelo de prompt. Em vez disso, você pode criar um prompt parcial com o valor de "foo" e, em seguida, passar o modelo de prompt parcial adiante. Abaixo está um exemplo disso:

```js
import { PromptTemplate } from "langchain/prompts";

const prompt = new PromptTemplate({
  template: "{foo}{bar}",
  inputVariables: ["foo", "bar"]
});

const partialPrompt = await prompt.partial({
  foo: "foo",
});

const formattedPrompt = await partialPrompt.format({
  bar: "baz",
});

console.log(formattedPrompt);

// Resultado: foobaz
```

Também é possível inicializar o modelo de prompt com as variáveis parciais.

```js
const prompt = new PromptTemplate({
  template: "{foo}{bar}",
  inputVariables: ["bar"],
  partialVariables: {
    foo: "foo",
  },
});

const formattedPrompt = await prompt.format({
  bar: "baz",
});

console.log(formattedPrompt);

// Resultado: foobaz
```

Partial com funções:
Este método é útil quando você possui uma variável que deseja sempre obter de uma maneira comum. Por exemplo, se você quiser que o prompt tenha sempre a data atual, mas não pode codificá-la diretamente no prompt e passá-la junto com outras variáveis pode ser tedioso. Nesse caso, é muito útil usar uma função que retorne a data atual para criar um prompt parcial.

```js
const getCurrentDate = () => {
  return new Date().toISOString();
};

const prompt = new PromptTemplate({
  template: "Me conte uma piada {adjetivo} sobre o dia {data}",
  inputVariables: ["adjetivo", "data"],
});

const partialPrompt = await prompt.partial({
  data: getCurrentDate,
});

const formattedPrompt = await partialPrompt.format({
  adjetivo: "engraçada",
});

console.log(formattedPrompt);

// Resultado: Me conte uma piada engraçada sobre o dia 2023-07-13T00:54:59.287Z
```

Também é possível inicializar o modelo de prompt com as variáveis parciais:

```js
const prompt = new PromptTemplate({
  template: "Me conte uma piada {adjetivo} sobre o dia {data}",
  inputVariables: ["adjetivo"],
  partialVariables: {
    data: getCurrentDate,
  }
});

const formattedPrompt = await prompt.format({
  adjetivo: "engraçada",
});

console.log(formattedPrompt);

// Resultado: Me conte uma piada engraçada sobre o dia 2023-07-13T00:54:59.287Z
```

Dessa forma, você pode usar a abordagem que melhor se adapte ao seu caso específico para criar "partial prompt templates" no LangChain.

# Composition
Essa anotação explica como compor vários prompts juntos. Isso pode ser útil quando você deseja reutilizar partes dos prompts. Isso pode ser feito com um "PipelinePrompt". Um "PipelinePrompt" consiste em duas partes principais:

Prompt final: Este é o prompt final que é retornado.
Prompts do pipeline: Esta é uma lista de tuplas, consistindo em um nome de string e um modelo de prompt. Cada modelo de prompt será formatado e, em seguida, passado para futuros modelos de prompt como uma variável com o mesmo nome.

```js
import { PromptTemplate, PipelinePromptTemplate } from "langchain/prompts";

const fullPrompt = PromptTemplate.fromTemplate(`{introduction}

{example}

{start}`);

const introductionPrompt = PromptTemplate.fromTemplate(
  `You are impersonating {person}.`
);

const examplePrompt =
  PromptTemplate.fromTemplate(`Here's an example of an interaction:
Q: {example_q}
A: {example_a}`);

const startPrompt = PromptTemplate.fromTemplate(`Now, do this for real!
Q: {input}
A:`);

const composedPrompt = new PipelinePromptTemplate({
  pipelinePrompts: [
    {
      name: "introduction",
      prompt: introductionPrompt,
    },
    {
      name: "example",
      prompt: examplePrompt,
    },
    {
      name: "start",
      prompt: startPrompt,
    },
  ],
  finalPrompt: fullPrompt,
});

const formattedPrompt = await composedPrompt.format({
  person: "Elon Musk",
  example_q: `What's your favorite car?`,
  example_a: "Telsa",
  input: `What's your favorite social media site?`,
});

console.log(formattedPrompt);

/*
  You are impersonating Elon Musk.

  Here's an example of an interaction:
  Q: What's your favorite car?
  A: Telsa

  Now, do this for real!
  Q: What's your favorite social media site?
  A:
*/
```