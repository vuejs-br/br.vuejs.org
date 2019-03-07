---
title: Slots
type: guide
order: 104
---

<p class="tip">**Nota da Equipe de Tradução**
Este arquivo ainda não foi traduzido! Leia a versão original em inglês a seguir e, se puder, colabore com sua tradução: acesse [nosso projeto no GitHub](https://github.com/vuejs-br/br.vuejs.org/issues), avise que irá contribuir e inicie a tradução. Sua participação é muito importante!</p>

> Esta página assume que você já leu o [Básico sobre Componentes](components.html). Leia lá primeiro se você for novo com componentes.

>Na verão 2.6, Nós introduzimos uma nova syntax unificada (A diretiva `v-slot`) para os slots nomeados e com escopo. Ele substitui os atributos `slot` e `slot-scope`, o qual estão agora obsoletos, mas não foram removidos e ainda estão documentados [aqui](#Deprecated-Syntax). Os fundamentos ara nova sintax é descrita neste [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md).

## Conteúdos Slot

Vue implementa uma API de distribuição de conteúdo que é modelada após a atual [Detalhamento da especificação dos componentes da Web](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), usando o elemento `<slot>` para servir como saídas de distribuição de conteúdos

Isso permite que você componha componentes como esse:

``` html
<navigation-link url="/profile">
  Seu Perfil
</navigation-link>
```

Então no _template_ para `<navigation-link>`, você poderá ter:

``` html
<a
  v-bind:href="url"
  class="nav-link"
>
  <slot></slot>
</a>
```

Quando o componente renderizar, o elemento `<slot>`  será substituido por "Seu Perfil". Slots podem conter qualquer tipo de codigo template, incluindo HTML:

``` html
<navigation-link url="/profile">
  <!-- Adicionando icones Font Awesome -->
  <span class="fa fa-user"></span>
  Seu Perfil
</navigation-link>
```

Ou até mesmo outros componentes:

``` html
<navigation-link url="/profile">
  <!-- Usado um componente para adcionar um icone -->
  <font-awesome-icon name="user"></font-awesome-icon>
  Seu Perfil
</navigation-link>
```

Se no `<navigation-link>`  **NÃO** contém um elemento `<slot>`, qualquer conteúdo passado para ele simplesmente será discartado.

## Escopo de compilação

Quando você quer usar um dado dentro de um slot, como em:

``` html
<navigation-link url="/profile">
  Logado como {{ user.name }}
</navigation-link>
```

Esse `slot` tem acesso as mesmas propriedades da instância (i.e. ao mesmo "escopo") como o resto do template. O slot **NÃO** tem acesso ao escopo do `<navigation-link>`. Por exemplo, tentando acessar a `url` não funcionaria:

``` html
<navigation-link url="/profile">
  Clicando aqui te enviará para: {{ url }}
  <!--
  O `url` será indefinido, porque esse conteúdo é passado
  _para_ <navigation-link>, em vez de definido _dentro_ do componente <navigation-link>.
  -->
</navigation-link>
```

Como regra, lembre-se de que:

> Tudo no _template_ pai é compilado no escopo do pai; Tudo no _template_ filho é compilado no escopo do filho.

## Conteúdo de _Fallback_

Há casos em que é útil especificar o conteúdo de falllback (i.e. padrão) para um slot, a ser renderizado somente quando nenhum conteúdo é fornecido. Por exemplo, no componente `<submit-button>`:

```html
<button type="submit">
  <slot></slot>
</button>
```

Pordemos querer que o texto "Enviar" seja renderizado dentro do `<button>` na maioria das vezes. para "Enviar" o conteúdo de fallback, podemos colocá-lo entre as tags `<slot>`:

```html
<button type="submit">
  <slot>Enviar</slot>
</button>
```

Agora, quando usamos `<submit-button>` no componente pai, sem fornecer conteúdo para o slot:

```html
<submit-button></submit-button>
```

Irá renderizar o conteúdo de fallback, "Enviar":

```html
<button type="submit">
  Enviar
</button>
```

Mas se nós fornecermos conteúdo:

```html
<submit-button>
  **Salvar**
</submit-button>
```

Então, o conteúdo fornecido será renderizado:

```html
<button type="submit">
  Salvar
</button>
```
## Slots Nomeados

> Atualizado em 2.6.0+. [Veja aqui](#Deprecated-Syntax) para a sintax obsoleta usando o atributo `slot`.

Há momentos que é útil ter multiplos elementos slots. Por exemplo, em um componente `<base-layout>` com o seguinte template:

``` html
<div class="container">
  <header>
    <!-- Nós queremos o cabeçalho aqui -->
  </header>
  <main>
    <!-- Nós queremos o conteúdo principal aqui -->
  </main>
  <footer>
    <!-- ós queremos o rodapé aqui -->
  </footer>
</div>
```

Para estes casos, o elemento `<slot>` em um atributo especial chamado, `name`, que pode ser usado para definir slots adicionais:

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

Um `<slot>` sem `name` implica implicitamente como "default".

Para fornecer conteúdo para slots nomeados, nos podemos usar a diretiva `v-slot` em um `<template>`, fornecendo o nome do slot como arumento do `v-slot`:

```html
<base-layout>
  <template v-slot:header>
    <h1>Aqui pode estar um título da página</h1>
  </template>

  <p>Um parágrafo para o conteúdo principal.</p>
  <p>E um outro parágrafo.</p>

  <template v-slot:footer>
    <p>Aqui estão algumas informações de contato</p>
  </template>
</base-layout>
```

Agora tudo dentro dos elementos `<template>` serão passado aos slots correspondentes. Qualquer conteúdo não envolvido por um `<template>` usando `v-slot` é assumido como sendo o slot padrão.

No entanto, você ainda pode incluir o conteúdo do slot padrão em um `<template>` se você desejar deve ficar explícito:

```html
<base-layout>
  <template v-slot:header>
    <h1>Aqui pode estar um título da página</h1>
  </template>

  <template v-slot:default>
    <p>Um parágrafo para o conteúdo principal.</p>
    <p>E um outro parágrafo.</p>
  </template>

  <template v-slot:footer>
    <p>Aqui estão algumas informações de contato</p>
  </template>
</base-layout>
```

De qualquer forma, O HTML renderizado será:

``` html
<div class="container">
  <header>
    <h1>Aqui pode estar um título da página</h1>
  </header>
  <main>
    <p>Um parágrafo para o conteúdo principal.</p>
    <p>E um outro parágrafo.</p>
  </main>
  <footer>
    <p>Aqui estão algumas informações de contato</p>
  </footer>
</div>
```

Observe que **`v-slot` só pode ser adicionado a um `<template>`** (com [uníca exeção](#Abbreviated-Syntax-for-Lone-Default-Slots)), ao contrário dos  [atributos `slot`](#Deprecated-Syntax).

## Scoped Slots

> Atualizado em 2.6.0+. [Veja aqui](#Deprecated-Syntax) para a sintaxe obsoleta usando o atributo `slot-scope`.

As vezes, é útil que o conteúdo do slot tenha acesso aos dados disponíveis apenas no componente filho. Por exemplo, imagine um componente `<current-user>` com o seguinte template:

```html
<span>
  <slot>{{ user.lastName }}</slot>
</span>
```
Podemos querer substituir esse conteúdo de fallback para exibir o primeiro nome do usuário, em vez do último, assim:
We might want to replace this fallback content to display the user's first name, instead of last, like this:

``` html
<current-user>
  {{ user.firstName }}
</current-user>
```

Isso não funcionará, no entando, porque somente o componente `<current-user>` tem acesso ao `user` e ao conteúdo que estamos fornecendo é renderizado no pai.

Para tornar `user` disponível para o conteúdo do slot no pai, podemos ligar `user` como um atributo do elemento `<slot>`:

``` html
<span>
  <slot v-bind:user="user">
    {{ user.lastName }}
  </slot>
</span>
```

Atributos ligados a um elemento `<slot>`são chamados de  **slot props**. Agora, no escopo pai, podemos usar o `v-slot` com um valor para definir um nome para o slot que nos foi fornecido:

``` html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

Neste exemplo, escolhemos nomear o objeto que contém todos os nossos slots props `slotProps`, mas você pode usar qualquer nome que desejar.

### Sintaxe abreviada para slots default

Em casos como acima, quando _apenas_ o _slot default_ é fornecido, as tags do componente podem ser usadas como o template. Isso nos permite usar o `v-slot` diretamente no componente:

``` html
<current-user v-slot:default="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

Isso pode ser encurtado ainda mais. Assim como o conteúdo não especificado é considerado como sendo o slot padrão, assume-se que o `v-slot` sem um argumento se refira ao slot padrão:

``` html
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

Observe que a sintax abreviada para o  slot padrão **NÂO PODE** ser misturado com slots nomeados, pois iso levaria a ambiguidade do escopo:

``` html
<!-- INVALID, will result in warning -->
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
  <template v-slot:other="otherSlotProps">
    slotProps NÂO está disponível aqui!
  </template>
</current-user>
```

Sempre que houver vários slots, use a sintaxe completa com base em `<template>` para _todos_ os slots:

``` html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>

  <template v-slot:other="otherSlotProps">
    ...
  </template>
</current-user>
```

### Destructuring Slot Props

Internamente, os `scoped slots` trabalham envolvendo seu conteúdo de slot em uma função passada em um único argumento:

```js
function (slotProps) {
  // ... conteúdo do slot ...
}
```
Isso significa que o valor de `v-slot` pode realmente aceitar qualquer expressão javascript válida que possa aparecer na posição de argumento de uma definição de função. Então, em ambientes suportados ([single-file components](single-file-components.html) ou [browsers modernos](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Browser_compatibility)), você também pode usar [ES2015 destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring) to pull out specific slot props, assim:

``` html
<current-user v-slot="{ user }">
  {{ user.firstName }}
</current-user>
```

Isso pode tornar o _template_ bem mais limpo, especialmente quando o slot fornece muitas _props_. Também abre outras possibilidades, tais como renomear _props_, e.g. `user` para `person`:

``` html
<current-user v-slot="{ user: person }">
  {{ person.firstName }}
</current-user>
```

Você pode até mesmo definir fallbacks, para serem usados no caso de um _slot prop_ ser indefinido:

``` html
<current-user v-slot="{ user = { firstName: 'Guest' } }">
  {{ user.firstName }}
</current-user>
```

## Slots de nomes dinâmicos

> Novo na versão 2.6.0+

[Diretiva de argumentos dinâmicos](syntax.html#Dynamic-Arguments) também funciona `v-slot`, permitindo a definição de slots nomeados dinâmicos:

``` html
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

## Atalho para Slots Nomeados

> Novo na versão 2.6.0+

Semelhante ao `v-on` e `v-bind` o `v-slot` também possui um atalho, substituindo tudo antes do argumento (`v-slot:`) pelo símbolo especial `#`. Por exemplo, `v-slot:header` pode ser reescrito como `#header`:

```html
<base-layout>
  <template #header>
    <h1>Aqui pode estar um título da página</h1>
  </template>

  <p>Um parágrafo para o conteúdo principal.</p>
  <p>E um outro parágrafo.</p>

  <template #footer>
    <p>Aqui estão algumas informações de contato</p>
  </template>
</base-layout>
```

No entanto, assim como com outras diretivas, a abreviação só está disponível quando um argumento é fornecdo. Isso significa que a seguinte sintaxe é inválida:

``` html
<!-- Isso acionará um aviso -->
<current-user #="{ user }">
  {{ user.firstName }}
</current-user>
```

Em vez disso, você sempre deve especificar um nome para o slot se desejar usar o atalho:

``` html
<current-user #default="{ user }">
  {{ user.firstName }}
</current-user>
```

## Outros exemplos

**Slot props nos permite transformar slots em templates reutilizáveis que podem renderizar diferentes conteúdos com base em adições de entrada.** Isso é mais útil quando você está projetando um componente reutilizável que encapsula a lógica de dados, enquanto permite que o componente pai consumidor personalize parte de seu layout.

Por exemplo, estamos implementando um componente`<todo-list>` que contém o layout e a lógica de filtragem para uma lista:

```html
<ul>
  <li
    v-for="todo in filteredTodos"
    v-bind:key="todo.id"
  >
    {{ todo.text }}
  </li>
</ul>
```

Em vez de codificar o conteúdo para cada _todo_, podemos deixar que o componente pai assuma o controle fazendo de todos _todo_ um slot, então ligando `todo` como uma slot prop:

```html
<ul>
  <li
    v-for="todo in filteredTodos"
    v-bind:key="todo.id"
  >
    <!--
    Nós temos um slot para cada _todo_ passando o objeto
    `todo` como uma slot prop.
    -->
    <slot name="todo" v-bind:todo="todo">
      <!-- Fallback content -->
      {{ todo.text }}
    </slot>
  </li>
</ul>
```

Agora, quando usamos o componente `<todo-list>`, podemos opcionalmente definir uma alternativa `<template>` para itens do _todo_, mas com acesso aos dados do filho:

```html
<todo-list v-bind:todos="todos">
  <template v-slot:todo="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```

Contudo, mesmo que tratado superficialmente dos quais _scoped slots_ são capazes de fazer. Para exemplos completos e reais de uso, recomendamos buscar mais sobre bibliotecas como [Vue Virtual Scroller](https://github.com/Akryum/vue-virtual-scroller), [Vue Promised](https://github.com/posva/vue-promised), e [Portal Vue](https://github.com/LinusBorg/portal-vue).

## Deprecated Syntax

> The `v-slot` directive was introduced in Vue 2.6.0, offering an improved, alternative API to the still-supported `slot` and `slot-scope` attributes. The full rationale for introducing `v-slot` is described in this [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md). The `slot` and `slot-scope` attributes will continue to be supported in all future 2.x releases, but are officially deprecated and will eventually be removed in Vue 3.

### Named Slots with the `slot` Attribute

> <abbr title="Still supported in all 2.x versions of Vue, but no longer recommended.">Deprecated</abbr> in 2.6.0+. See [here](#slots-nomeados) for the new, recommended syntax.

To pass content to named slots from the parent, use the special `slot` attribute on `<template>` (using the `<base-layout>` component described [here](#slots-nomeados) as example):

```html
<base-layout>
  <template slot="header">
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template slot="footer">
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

Or, the `slot` attribute can also be used directly on a normal element:

``` html
<base-layout>
  <h1 slot="header">Here might be a page title</h1>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <p slot="footer">Here's some contact info</p>
</base-layout>
```

There can still be one unnamed slot, which is the **default slot** that serves as a catch-all for any unmatched content. In both examples above, the rendered HTML would be:

``` html
<div class="container">
  <header>
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
  </footer>
</div>
```

### Scoped Slots with the `slot-scope` Attribute

> <abbr title="Still supported in all 2.x versions of Vue, but no longer recommended.">Deprecated</abbr> in 2.6.0+. See [here](#Scoped-Slots) for the new, recommended syntax.

To receive props passed to a slot, the parent component can use `<template>` with the `slot-scope` attribute (using the `<slot-example>` described [here](#Scoped-Slots) as example):

``` html
<slot-example>
  <template slot="default" slot-scope="slotProps">
    {{ slotProps.msg }}
  </template>
</slot-example>
```

Here, `slot-scope` declares the received props object as the `slotProps` variable, and makes it available inside the `<template>` scope. You can name `slotProps` anything you like similar to naming function arguments in JavaScript.

Here `slot="default"` can be omitted as it is implied:

``` html
<slot-example>
  <template slot-scope="slotProps">
    {{ slotProps.msg }}
  </template>
</slot-example>
```

The `slot-scope` attribute can also be used directly on a non-`<template>` element (including components):

``` html
<slot-example>
  <span slot-scope="slotProps">
    {{ slotProps.msg }}
  </span>
</slot-example>
```

The value of `slot-scope` can accept any valid JavaScript expression that can appear in the argument position of a function definition. This means in supported environments ([single-file components](single-file-components.html) or [modern browsers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Browser_compatibility)) you can also use [ES2015 destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring) in the expression, like so:

``` html
<slot-example>
  <span slot-scope="{ msg }">
    {{ msg }}
  </span>
</slot-example>
```

Using the `<todo-list>` described [here](#Other-Examples) as an example, here's the equivalent usage using `slot-scope`:

``` html
<todo-list v-bind:todos="todos">
  <template slot="todo" slot-scope="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```