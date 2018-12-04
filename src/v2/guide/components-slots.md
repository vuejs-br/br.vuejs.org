---
title: Slots
type: guide
order: 104
---

## Slot Content

Vue implementa uma API de distribuição de conteúdo que é modelada após a atual [Detalhamento da especificação dos componentes da Web](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), usando o elemento `<slot>` para servir como saídas de distribuição de conteúdos

Isso permite que vc componha componentes como este:

``` html
<navigation-link url="/profile">
  Seu Perfil
</navigation-link>
```

Então no template para `<navigation-link>`, você pode ter:

``` html
<a
  v-bind:href="url"
  class="nav-link"
>
  <slot></slot>
</a>
```

Quando o componente renderiza, o elemento `<slot>` vai ser substituído pelo "Seu Perfil". Slots podem conter qualquer template de código, incluindo HTML:

``` html
<navigation-link url="/profile">
  <!-- Add a Font Awesome icon -->
  <span class="fa fa-user"></span>
  Seu Perfil
</navigation-link>
```

Ou até mesmo outros componentes:

``` html
<navigation-link url="/profile">
  <!-- Use a component to add an icon -->
  <font-awesome-icon name="user"></font-awesome-icon>
  Seu Perfil
</navigation-link>
```

Se um `<navigation-link>` **não** conter um elemento `<slot>`, qualquer conteudo informado sera simplesmente descartado.

## Named Slots

Existem momentos quando é útil ter multiplos slots. Por exemplo, em um componente hipotético `base-layout` com o seguinte template:

``` html
<div class="container">
  <header>
    <!-- Nós queremos o cabeçalho aqui -->
  </header>
  <main>
    <!-- Nós queremos o contúdo principal aqui -->
  </main>
  <footer>
    <!-- Nós queremos o rodapé aqui -->
  </footer>
</div>
```
Para esses casos, o elemento `<slot>` tem um atributo especial, `name`, que pode ser utilizado para definir slits adicionais: 

``` html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

Para prover conteúdo para os slots nomeados, nos podemos utilizar o atribuo do `slot` em um elemento `<template>` no pai:

```html
<base-layout>
  <template slot="header">
    <h1>Aqui pode ser o título da página</h1>
  </template>

  <p>Um parágrafo para o conteúdo principal.</p>
  <p>e mais um.</p>

  <template slot="footer">
    <p>Aqui algumas informações de contato</p>
  </template>
</base-layout>
```

Ou, o atributo `slot` também pode ser utilizado diretamente em um elemento normal:

``` html
<base-layout>
  <h1 slot="header">Aqui pode ser o título da página</h1>

  <p>Um parágrafo para o conteúdo principal.</p>
  <p>e mais um.</p>

  <p slot="footer">Aqui algumas informações de contato</p>
</base-layout>
```
Ainda pode haver um slot sem nome, que é o **slot padrão** que serve como saída abrangente para qualquer conteúdo sem correspondência. Em ambos os exemplos abaixo, os HTML renderizados devem ser:

``` html
<div class="container">
  <header>
    <h1>Aqui pode ser o título da página</h1>
  </header>
  <main>
  <p>Um parágrafo para o conteúdo principal.</p>
  <p>e mais um.</p>
  </main>
  <footer>
    <p>Aqui algumas informações de contato</p>
  </footer>
</div>
```

## Conteúdo Padrão de Slot

Ainda existem casos quando é útil prover um slot com contaúdo padão. Por exemplo, um componente `<submit-button>` pode querer que o conteúdo do botão seja "Enviar" por padrão, mas também permitir que usuários substituam com "Salvar","Carregar", ou qualquer outra coisa.

Para conseguir isso, especifique o conteúdo padrão entre as tags `<slot>`.

```html
<button type="submit">
  <slot>Enviar</slot>
</button>
```

se o slot for fornecido pelo pai, ele substituirá o conteúdo padrão.

## Escopo de Compilação

Quando você quer usar dados dentro de um slot, como em:

``` html
<navigation-link url="/profile">
  Logado como {{ user.name }}
</navigation-link>
```

Este slot tem acesso as mesmas propriedades da instancia(i.e. o mesmo "escopo") como o resto do template. O slot **não** deve ter acesso ao escodo do `<navigation-link>`. Por exemplo, tentar acessar a `url` não deve funcionar. Como uma regra, lembre-se disso:

> Tudo no template do pai é compilado no escopo do pai; tudo no template filho é compilado no escopo filho.

## Slots Escopo

> Novo na 2.1.0+

As vezes você ira querer prover um componente com um slot reusável que pode sacessar dados de um componente filho. Por Exemplo, um simples componente `<todo-list>` pode conter o seguinte em seu template:

```html
<ul>
  <li
    v-for="todo in todos"
    v-bind:key="todo.id"
  >
    {{ todo.text }}
  </li>
</ul>
```

Mas em algumas partes do nosso aplicativo, queremos que os itens individuais de to-do renderizem algo diferente de apenas o `todo.txt`. É aqui que entram os slots com escopo.

Para fazer o recurso possível, tudo que precisamos fazer é encapsular o conteúdo do item de to-do em um eleento `<slot>`, então passar ao slot qualuqer dado relevante a esse contexto: nesse caso, o objeto `todo`:

```html
<ul>
  <li
    v-for="todo in todos"
    v-bind:key="todo.id"
  >
    <!-- Nós temos um slot para cada todo, passando um -->
    <!-- objeto `todo` como propriedade do slot.                -->
    <slot v-bind:todo="todo">
      <!-- Fallback content -->
      {{ todo.text }}
    </slot>
  </li>
</ul>
```

Agora quando formos utiliza o componente `<todo-list>`, nós podemos opcionalmente definir um `<template>`alternativo para os itens to-do, mas com acesso para dados de um filho através do atributo `slot-scope`:

```html
<todo-list v-bind:todos="todos">
  <!-- Define `slotProps` como o nome do nosso escopo do slot -->
  <template slot-scope="slotProps">
    <!-- Define um template personalizado para os itens to-do , utilizando -->
    <!-- `slotProps` ṕara personalizar cada to-do.            -->
    <span v-if="slotProps.todo.isComplete">✓</span>
    {{ slotProps.todo.text }}
  </template>
</todo-list>
```

> Na 2.5.0+, `slot-scope` não é mais limitado ao elemento `<template>`, mas pode ser usado em qualquer elemento ou componente no slot.

### Destruindo `slot-scope`

O valor do `slot-scope`pode na verdade aceitar qualquer expressão JavaScript válida que pode aparecer na posição do argumento de uma definição de função. Isso significa ambientes suportados ([single-file components](single-file-components.html) ou [modern browsers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Browser_compatibility)) você pode também utilizar [ES2015 destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring) na expressão, assim:

```html
<todo-list v-bind:todos="todos">
  <template slot-scope="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```

Esse é um excelente modo de criar slotes de escopo um pouco mais limpos.
