---
title: Slots
type: guide
order: 104
---

> Esta página assume que você já leu o [Básico sobre Componentes](components.html). Leia lá primeiro se você for novo com componentes.

## Conteúdo do Slot

O Vue implementa uma API de distribuição de conteúdo que é modelada conforme a [Especificação dos Componentes Web](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), usando o elemento `<slot>` servindo como pontos de dsitribuição para o conteúdo.

Isso permite que você componha componentes da seguinte maneira:

``` html
<navigation-link url="/perfil">
  Seu Perfil
</navigation-link>
```

E no template do componente `<navigation-link>`, você deverá ter:

``` html
<a
  v-bind:href="url"
  class="nav-link"
>
  <slot></slot>
</a>
```

Quando esse componente for renderizado, o elemento `<slot>` será substituído por "Seu Perfil". Slots podem conter qualquer código de template, incluindo HTML:

``` html
<navigation-link url="/perfil">
  <!-- Adiciona um ícone Font Awesome -->
  <span class="fa fa-user"></span>
  Seu Perfil
</navigation-link>
```

Ou até mesmo outros componentes:

``` html
<navigation-link url="/perfil">
  <!-- Usa um componente para adicionar um ícone -->
  <font-awesome-icon name="user"></font-awesome-icon>
  Seu Perfil
</navigation-link>
```

Seo componente `<navigation-link>` **não** conter um elemento `<slot>`, qualquer conteúdo passado para ele seria simplesmente descartado.

## Slots Nomeados

Existem casos em que é útil ter multiplos slots. Por exemplo, em um componente `base-layout` hipotético com o seguinte template:

``` html
<div class="container">
  <header>
    <!-- Nós queremos o conteúdo do cabeçalho aqui -->
  </header>
  <main>
    <!-- Nós queremos o conteúdo principal aqui -->
  </main>
  <footer>
    <!-- Nós queremos o conteúdo do rodapé aqui -->
  </footer>
</div>
```

Nesses casos, o elemento `<slot>` tem um atributo especial, `name`, que pode ser usado para definir slots adicionais:

``` html
<div class="container">
  <header>
    <slot name="cabecalho"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="rodape"></slot>
  </footer>
</div>
```

Para atribuir conteúdo aos slots nomeados, nós podemos usar o atributo `slot` em um elemento `<template>`:

```html
<base-layout>
  <template slot="cabecalho">
    <h1>Aqui pode ser o título da página</h1>
  </template>

  <p>Um paragrafo para o conteúdo principal.</p>
  <p>E mais um paragráfo.</p>

  <template slot="rodape">
    <p>Aqui estão algumas informações de contato</p>
  </template>
</base-layout>
```

Ou, o atributo `slot` pode támbem ser usado diretamente em um elemento normal:

``` html
<base-layout>
  <h1 slot="cabecalho">Aqui pode ser o título da página</h1>

  <p>Um parágrafo para o conteúdo principal.</p>
  <p>E mais um paragráfo.</p>

  <p slot="rodape">Aqui estão algumas informações de contato</p>
</base-layout>
```

Ainda pode haver um slot sem nome, conhecido como **slot padrão** que serve como um ponto pega-tudo para qualquer conteúdo não correspondido. Em ambos os exemplos acima, o HTML renderizado seria:

``` html
<div class="container">
  <header>
    <h1>Aqui pode ser o título da página</h1>
  </header>
  <main>
    <p>Um parágrafo para o conteúdo principal.</p>
    <p>E mais um parágrafo.</p>
  </main>
  <footer>
    <p>Aqui estão algumas informações de contato</p>
  </footer>
</div>
```

## Conteúdo Padrão do Slot

Existem casos em que é útil prover um slot com um conteúdo padrão. Por exemplo, um componente `<submit-button>` pode querer que, por padrão, o conteúdo do botão seja "Enviar", mas também permite os usuários a sobrescrevê-lo com "Salvar", "Upload", ou algo diferente.

Para fazer isso, especifique o conteúdo padrão entre as tags `<slot>`.

```html
<button type="submit">
  <slot>Enviar</slot>
</button>
```

Se algum conteúdo for atribuído pelo elemento pai, isso irá substituir o conteúdo padrão.

## Escopo de Compilação

Quando você quiser usar dados dentro de um slot, assim como em:

``` html
<navigation-link url="/perfil">
  Logado como {{ usuario.nome }}
</navigation-link>
```

Esse slot tem acesso as mesmas propriedades da instância (isto é, o mesmo "escopo") que o resto do template. O slot **não** tem acesso ao escopo do `<navigation-link>`. Por exemplo, não seria possível acessar a `url`. Como regra, lembre-se que:

> Tudo no elemento pai é compilado no escopo pai; tudo no template filho é compilado no escopo filho.

## Slots com Escopo

> Novo na 2.1.0+

As vezes você irá querer atribuir um componente com um slot reutilizável que pode acessar dados do componente filho. Por exemplo, um simples componente `<todo-list>` pode conter o seguinte conteúdo em seu template:

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

Mas em algumas partes do nosso app, nos queremos que cada item da lista de afazeres (todo) renderize algo diferente ao ínves de apenas `todo.text`. É aí que os slots com escopo entram em ação.

Para fazer com que essa funcionalidade seja possível, tudo que temos que fazer é inserir o conteúdo do item todo em um elemento `<slot>`, e então passar ao slot qualquer dado relevate ao seu contexto: nesse caso, o objeto `todo`:

```html
<ul>
  <li
    v-for="todo in todos"
    v-bind:key="todo.id"
  >
    <!-- Nós temos um slot para cada afazer, atribuindo-o -->
    <!-- o objeto `todo` como um prop do slot.            -->
    <slot v-bind:todo="todo">
      <!-- Conteúdo de reserva -->
      {{ todo.text }}
    </slot>
  </li>
</ul>
```

Agora quando nós usarmos o componente `<todo-list>`, nós podemos opcionalmente definir um `<template>` alternativo para os afazeres (todo), mas com acesso aos dados do elemento filho via o atributo `slot-scope`:

```html
<todo-list v-bind:todos="todos">
  <!-- Define `slotProps` como o nome do escopo de nosso slot -->
  <template slot-scope="slotProps">
    <!-- Define um template customizado para os afazeres (todo), usando -->
    <!-- `slotProps` para customizar cada afazer.                       -->
    <span v-if="slotProps.todo.isComplete">✓</span>
    {{ slotProps.todo.text }}
  </template>
</todo-list>
```

> Na 2.5.0+, `slot-scope` não é mais limitado ao elemento `<template>`, mas pode ser usado em qualquer elemento ou componente no slot.

### Desestruturando `slot-scope`

O valor do `slot-scope` pode aceitar qualquer expressão JavaScript válida que pode aparecer na posição de um argumento em uma definição de uma função. Isso significa que em ambientes suportados ([componentes single-file](single-file-components.html) ou [navegadores modernos](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Browser_compatibility)) você também pode usar [desestruturação ES2015](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring) na expressão, assim como:

```html
<todo-list v-bind:todos="todos">
  <template slot-scope="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```

Essa é uma ótima maneira defazer com que elementos com escopo tenham uma sintaxe mais limpa.