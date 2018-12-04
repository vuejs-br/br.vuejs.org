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

That slot has access to the same instance properties (i.e. the same "scope") as the rest of the template. The slot does **not** have access to `<navigation-link>`'s scope. For example, trying to access `url` would not work. As a rule, remember that:

> Everything in the parent template is compiled in parent scope; everything in the child template is compiled in the child scope.

## Scoped Slots

> New in 2.1.0+

Sometimes you'll want to provide a component with a reusable slot that can access data from the child component. For example, a simple `<todo-list>` component may contain the following in its template:

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

But in some parts of our app, we want the individual todo items to render something different than just the `todo.text`. This is where scoped slots come in.

To make the feature possible, all we have to do is wrap the todo item content in a `<slot>` element, then pass the slot any data relevant to its context: in this case, the `todo` object:

```html
<ul>
  <li
    v-for="todo in todos"
    v-bind:key="todo.id"
  >
    <!-- We have a slot for each todo, passing it the -->
    <!-- `todo` object as a slot prop.                -->
    <slot v-bind:todo="todo">
      <!-- Fallback content -->
      {{ todo.text }}
    </slot>
  </li>
</ul>
```

Now when we use the `<todo-list>` component, we can optionally define an alternative `<template>` for todo items, but with access to data from the child via the `slot-scope` attribute:

```html
<todo-list v-bind:todos="todos">
  <!-- Define `slotProps` as the name of our slot scope -->
  <template slot-scope="slotProps">
    <!-- Define a custom template for todo items, using -->
    <!-- `slotProps` to customize each todo.            -->
    <span v-if="slotProps.todo.isComplete">✓</span>
    {{ slotProps.todo.text }}
  </template>
</todo-list>
```

> In 2.5.0+, `slot-scope` is no longer limited to the `<template>` element, but can instead be used on any element or component in the slot.

### Destructuring `slot-scope`

The value of `slot-scope` can actually accept any valid JavaScript expression that can appear in the argument position of a function definition. This means in supported environments ([single-file components](single-file-components.html) or [modern browsers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Browser_compatibility)) you can also use [ES2015 destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring) in the expression, like so:

```html
<todo-list v-bind:todos="todos">
  <template slot-scope="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```

This is a great way to make scoped slots a little cleaner.
