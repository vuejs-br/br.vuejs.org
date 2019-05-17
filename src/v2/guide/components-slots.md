---
title: Slots
type: guide
order: 104
---

> Esta página assume que você já leu o [Básico sobre Componentes](components.html). Leia lá primeiro se você for novo com componentes.

<<<<<<< HEAD
> Na versão 2.6, Nós introduzimos uma nova sintaxe unificada (A diretiva `v-slot`) para os slots nomeados e com escopo. Ele substitui os atributos `slot` e `slot-scope`, os quais estão agora obsoletos, mas não foram removidos e ainda estão documentados [aqui](#Deprecated-Syntax). Os fundamentos para a nova sintaxe estão descritos neste [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md).

## Conteúdo do _Slot_
=======
> In 2.6.0, we introduced a new unified syntax (the `v-slot` directive) for named and scoped slots. It replaces the `slot` and `slot-scope` attributes, which are now deprecated, but have _not_ been removed and are still documented [here](#Deprecated-Syntax). The rationale for introducing the new syntax is described in this [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md).

## Slot Content

Vue implements a content distribution API inspired by the [Web Components spec draft](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), using the `<slot>` element to serve as distribution outlets for content.
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

_Vue_ implementa uma _API_ de distribuição de conteúdo que é modelada após o atual [detalhamento da especificação dos componentes da Web](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), usando o elemento `<slot>` para servir como saída de distribuição de conteúdos.

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

<<<<<<< HEAD
Quando o componente renderizar, o elemento `<slot>`  será substituido por "Seu Perfil". Slots podem conter qualquer tipo de código template, incluindo _HTML_:
=======
When the component renders, `<slot></slot>` will be replaced by "Your Profile". Slots can contain any template code, including HTML:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

``` html
<navigation-link url="/profile">
  <!-- Adicionando ícones Font Awesome -->
  <span class="fa fa-user"></span>
  Seu Perfil
</navigation-link>
```

Ou até mesmo outros componentes:

``` html
<navigation-link url="/profile">
  <!-- Usando um componente para adicionar um ícone -->
  <font-awesome-icon name="user"></font-awesome-icon>
  Seu Perfil
</navigation-link>
```

<<<<<<< HEAD
Se no `<navigation-link>`  **não** contém um elemento `<slot>`, qualquer conteúdo passado para ele simplesmente será descartado.

## Escopo de compilação

Quando você quer usar um dado dentro de um _slot_, como em:

``` html
<navigation-link url="/profile">
  Logado como {{ user.name }}
</navigation-link>
```

Esse `slot` tem acesso as mesmas propriedades da instância (i.e. ao mesmo "escopo") como o resto do _template_. O _slot_ **não** tem acesso ao escopo do `<navigation-link>`. Por exemplo, tentando acessar a `url` não funcionaria:

``` html
<navigation-link url="/profile">
  Clicando aqui te enviará para: {{ url }}
  <!--
  O `url` será undefined, porque esse conteúdo é passado
  para <navigation-link>, em vez de definido dentro do componente <navigation-link>.
=======
If `<navigation-link>`'s template did **not** contain a `<slot>` element, any content provided between its opening and closing tag would be discarded.

## Compilation Scope

When you want to use data inside a slot, such as in:

``` html
<navigation-link url="/profile">
  Logged in as {{ user.name }}
</navigation-link>
```

That slot has access to the same instance properties (i.e. the same "scope") as the rest of the template. The slot does **not** have access to `<navigation-link>`'s scope. For example, trying to access `url` would not work:

``` html
<navigation-link url="/profile">
  Clicking here will send you to: {{ url }}
  <!--
  The `url` will be undefined, because this content is passed
  _to_ <navigation-link>, rather than defined _inside_ the
  <navigation-link> component.
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
  -->
</navigation-link>
```

<<<<<<< HEAD
Como regra, lembre-se de que:

> Tudo no _template_ pai é compilado no escopo do pai; Tudo no _template_ filho é compilado no escopo do filho.

## Conteúdo de _Fallback_

Há casos em que é útil especificar o conteúdo de _fallback_ (i.e. padrão) para um _slot_, a ser renderizado somente quando nenhum conteúdo é fornecido. Por exemplo, no componente `<submit-button>`:
=======
As a rule, remember that:

> Everything in the parent template is compiled in parent scope; everything in the child template is compiled in the child scope.

## Fallback Content

There are cases when it's useful to specify fallback (i.e. default) content for a slot, to be rendered only when no content is provided. For example, in a `<submit-button>` component:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

```html
<button type="submit">
  <slot></slot>
</button>
```

<<<<<<< HEAD
Podemos querer que o texto "Enviar" seja renderizado dentro do `<button>` na maioria das vezes. para "Enviar" o conteúdo de _fallback_, podemos colocá-lo entre as _tags_ `<slot>`:

```html
<button type="submit">
  <slot>Enviar</slot>
</button>
```

Agora, quando usamos `<submit-button>` no componente pai, sem fornecer conteúdo para o _slot_:
=======
We might want the text "Submit" to be rendered inside the `<button>` most of the time. To make "Submit" the fallback content, we can place it in between the `<slot>` tags:

```html
<button type="submit">
  <slot>Submit</slot>
</button>
```

Now when we use `<submit-button>` in a parent component, providing no content for the slot:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

```html
<submit-button></submit-button>
```

<<<<<<< HEAD
Irá renderizar o conteúdo de _fallback_, "Enviar":

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
=======
will render the fallback content, "Submit":

```html
<button type="submit">
  Submit
</button>
```

But if we provide content:

```html
<submit-button>
  Save
</submit-button>
```

Then the provided content will be rendered instead:

```html
<button type="submit">
  Save
</button>
```
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

> Atualizado em 2.6.0+. [Veja aqui](#Sintaxe-Obsoleta) para a sintaxe obsoleta usando o atributo `slot`.

<<<<<<< HEAD
Há momentos em que é útil ter múltiplos elementos slots. Por exemplo, em um componente `<base-layout>` com o seguinte template:
=======
> Updated in 2.6.0+. [See here](#Deprecated-Syntax) for the deprecated syntax using the `slot` attribute.

There are times when it's useful to have multiple slots. For example, in a `<base-layout>` component with the following template:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

``` html
<div class="container">
  <header>
    <!-- Nós queremos o cabeçalho aqui -->
  </header>
  <main>
    <!-- Nós queremos o conteúdo principal aqui -->
  </main>
  <footer>
    <!-- Nós queremos o rodapé aqui -->
  </footer>
</div>
```

Para estes casos, o elemento `<slot>` tem um atributo especial chamado, `name`, que pode ser usado para definir _slots_ adicionais:

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

<<<<<<< HEAD
Uma saída `<slot>` sem `name` tem implicitamente o nome _"default"_.

Para fornecer conteúdo para _slots_ nomeados, nós podemos usar a diretiva `v-slot` em um `<template>`, fornecendo o nome do _slot_ como argumento do `v-slot`:
=======
A `<slot>` outlet without `name` implicitly has the name "default".

To provide content to named slots, we can use the `v-slot` directive on a `<template>`, providing the name of the slot as `v-slot`'s argument:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

```html
<base-layout>
  <template v-slot:header>
<<<<<<< HEAD
    <h1>Aqui pode estar um título da página</h1>
=======
    <h1>Here might be a page title</h1>
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
  </template>

  <p>Um parágrafo para o conteúdo principal.</p>
  <p>E um outro parágrafo.</p>

  <template v-slot:footer>
<<<<<<< HEAD
    <p>Aqui estão algumas informações de contato</p>
=======
    <p>Here's some contact info</p>
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
  </template>
</base-layout>
```

<<<<<<< HEAD
Agora tudo dentro dos elementos `<template>` serão passado aos _slots_ correspondentes. Qualquer conteúdo não envolvido por um `<template>` usando `v-slot` é assumido como sendo o _slot default_.

No entanto, você ainda pode incluir o conteúdo do _slot default_ em um `<template>` se você deseja ser explícito:
=======
Now everything inside the `<template>` elements will be passed to the corresponding slots. Any content not wrapped in a `<template>` using `v-slot` is assumed to be for the default slot.

However, you can still wrap default slot content in a `<template>` if you wish to be explicit:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

```html
<base-layout>
  <template v-slot:header>
<<<<<<< HEAD
    <h1>Aqui pode estar um título da página</h1>
  </template>

  <template v-slot:default>
    <p>Um parágrafo para o conteúdo principal.</p>
    <p>E um outro parágrafo.</p>
  </template>

  <template v-slot:footer>
    <p>Aqui estão algumas informações de contato</p>
=======
    <h1>Here might be a page title</h1>
  </template>

  <template v-slot:default>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </template>

  <template v-slot:footer>
    <p>Here's some contact info</p>
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
  </template>
</base-layout>
```

<<<<<<< HEAD
De qualquer forma, O _HTML_ renderizado será:
=======
Either way, the rendered HTML will be:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

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

<<<<<<< HEAD
Observe que **`v-slot` só pode ser adicionado a um `<template>`** (com [única exceção](#sintaxe-abreviada-para-slots-default)), ao contrário dos  [atributos `slot`](#Deprecated-Syntax).

## Slots com Escopo Definido

> Atualizado em 2.6.0+. [Veja aqui](#Sintaxe-abreviada-para-slots-default) para a sintaxe obsoleta usando o atributo `slot-scope`.

As vezes, é útil que o conteúdo do _slot_ tenha acesso aos dados disponíveis apenas no componente filho. Por exemplo, imagine um componente `<current-user>` com o seguinte _template_:
=======
Note that **`v-slot` can only be added to a `<template>`** (with [one exception](#Abbreviated-Syntax-for-Lone-Default-Slots)), unlike the deprecated [`slot` attribute](#Deprecated-Syntax).

## Scoped Slots

> Updated in 2.6.0+. [See here](#Deprecated-Syntax) for the deprecated syntax using the `slot-scope` attribute.

Sometimes, it's useful for slot content to have access to data only available in the child component. For example, imagine a `<current-user>` component with the following template:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

```html
<span>
  <slot>{{ user.lastName }}</slot>
</span>
```
Podemos querer substituir esse conteúdo de _fallback_ para exibir o primeiro nome do usuário, em vez do último, assim:

<<<<<<< HEAD
``` html
<current-user>
  {{ user.firstName }}
</current-user>
```

Isso não funcionará, no entanto, porque somente o componente `<current-user>` tem acesso ao `user` e o conteúdo que estamos fornecendo é renderizado no pai.

Para tornar `user` disponível para o conteúdo do _slot_ no pai, podemos vincular `user` como um atributo do elemento `<slot>`:
=======
We might want to replace this fallback content to display the user's first name, instead of last, like this:

``` html
<current-user>
  {{ user.firstName }}
</current-user>
```

That won't work, however, because only the `<current-user>` component has access to the `user` and the content we're providing is rendered in the parent.

To make `user` available to the slot content in the parent, we can bind `user` as an attribute to the `<slot>` element:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

``` html
<span>
  <slot v-bind:user="user">
    {{ user.lastName }}
  </slot>
</span>
<<<<<<< HEAD
```

Atributos vinculados a um elemento `<slot>` são chamados de  **props do _slot_**. Agora, no escopo pai, podemos usar o `v-slot` com um valor para definir um nome para o _slot_ que nos foi fornecido:

``` html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

Neste exemplo, escolhemos nomear o objeto que contém todos os nossos props do _slot_ `slotProps`, mas você pode usar qualquer nome que desejar.

### Sintaxe abreviada para slots default

Em casos como acima, quando apenas o _slot default_ é fornecido, as _tags_ do componente podem ser usadas como o _template_. Isso nos permite usar o `v-slot` diretamente no componente:

``` html
<current-user v-slot:default="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

Isso pode ser encurtado ainda mais. Assim como o conteúdo não especificado é considerado como sendo o _slot default_, assume-se que o `v-slot` sem um argumento se refira ao _slot default_:

``` html
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

Observe que a sintaxe abreviada para o  _slot default_ **não pode** ser misturada com slots nomeados, pois isso levaria a ambiguidade do escopo:

``` html
<!-- inválido, resultará em aviso -->
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
  <template v-slot:other="otherSlotProps">
    slotProps não está disponível aqui!
  </template>
</current-user>
```

Sempre que houver vários _slots_, use a sintaxe completa com base em `<template>` para **todos** os _slots_:

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

### Desestruturando Props do _Slot_

Internamente, os `slots` com escopo trabalham envolvendo seu conteúdo em uma função passando um único argumento:

```js
function (slotProps) {
  // ... conteúdo do slot ...
}
```
Isso significa que o valor de `v-slot` pode realmente aceitar qualquer expressão _JavaScript_ válida que possa aparecer na posição de argumento em uma definição de função. Então, em ambientes suportados ([componentes single-file](single-file-components.html) ou [browsers modernos](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Operators/Atribuicao_via_desestruturacao#Compatibilidade_do_navegador)), você também pode usar [desestruturação de objetos do ES2015](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Operators/Atribuicao_via_desestruturacao#Desestrutura%C3%A7%C3%A3o_de_objeto) para usar props específicos de slots, assim:

``` html
<current-user v-slot="{ user }">
  {{ user.firstName }}
</current-user>
```

Isso pode tornar o _template_ bem mais limpo, especialmente quando o slot fornece muitas props. Também abre outras possibilidades, tais como renomear props, e.g. `user` para `person`:

``` html
<current-user v-slot="{ user: person }">
  {{ person.firstName }}
</current-user>
```

Você pode até mesmo definir _fallbacks_, para serem usados no caso de um prop do _slot_ ser undefined:

``` html
<current-user v-slot="{ user = { firstName: 'Guest' } }">
  {{ user.firstName }}
</current-user>
```

## Slots de nomes dinâmicos

> Novo na versão 2.6.0+

[Argumentos de diretiva dinâmicos](syntax.html#Dynamic-Arguments) também funciona no `v-slot`, permitindo a definição de nomes de slots dinâmicos:

``` html
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

## Forma Abreviada para Slots Nomeados

> Novo na versão 2.6.0+

Semelhante ao `v-on` e `v-bind`, `v-slot` também possui uma forma abreviada, substituindo tudo antes do argumento (`v-slot:`) pelo símbolo especial `#`. Por exemplo, `v-slot:header` pode ser reescrito como `#header`:

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

No entanto, assim como com outras diretivas, a abreviação só está disponível quando um argumento é fornecido. Isso significa que a seguinte sintaxe é inválida:

``` html
<!-- Isso acionará um aviso -->
<current-user #="{ user }">
  {{ user.firstName }}
</current-user>
```

Em vez disso, você sempre deve especificar um nome para o _slot_ se desejar usar o atalho:

``` html
<current-user #default="{ user }">
  {{ user.firstName }}
</current-user>
```

## Outros exemplos

**Props de _slot_ nos permitem transformar slots em templates reutilizáveis que podem renderizar diferentes conteúdos com base em adições de entrada.** Isso é mais útil quando você está projetando um componente reutilizável que encapsula a lógica de dados, enquanto permite que o componente pai consumidor personalize parte de seu _layout_.

Por exemplo, estamos implementando um componente `<todo-list>` que contém o _layout_ e a lógica de filtragem para uma lista:
=======
```

Attributes bound to a `<slot>` element are called **slot props**. Now, in the parent scope, we can use `v-slot` with a value to define a name for the slot props we've been provided:

``` html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

In this example, we've chosen to name the object containing all our slot props `slotProps`, but you can use any name you like.

### Abbreviated Syntax for Lone Default Slots

In cases like above, when _only_ the default slot is provided content, the component's tags can be used as the slot's template. This allows us to use `v-slot` directly on the component:

``` html
<current-user v-slot:default="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

This can be shortened even further. Just as non-specified content is assumed to be for the default slot, `v-slot` without an argument is assumed to refer to the default slot:

``` html
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
</current-user>
```

Note that the abbreviated syntax for default slot **cannot** be mixed with named slots, as it would lead to scope ambiguity:

``` html
<!-- INVALID, will result in warning -->
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
  <template v-slot:other="otherSlotProps">
    slotProps is NOT available here
  </template>
</current-user>
```

Whenever there are multiple slots, use the full `<template>` based syntax for _all_ slots:

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

Internally, scoped slots work by wrapping your slot content in a function passed a single argument:

```js
function (slotProps) {
  // ... slot content ...
}
```

That means the value of `v-slot` can actually accept any valid JavaScript expression that can appear in the argument position of a function definition. So in supported environments ([single-file components](single-file-components.html) or [modern browsers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Browser_compatibility)), you can also use [ES2015 destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring) to pull out specific slot props, like so:

``` html
<current-user v-slot="{ user }">
  {{ user.firstName }}
</current-user>
```

This can make the template much cleaner, especially when the slot provides many props. It also opens other possibilities, such as renaming props, e.g. `user` to `person`:

``` html
<current-user v-slot="{ user: person }">
  {{ person.firstName }}
</current-user>
```

You can even define fallbacks, to be used in case a slot prop is undefined:

``` html
<current-user v-slot="{ user = { firstName: 'Guest' } }">
  {{ user.firstName }}
</current-user>
```

## Dynamic Slot Names

> New in 2.6.0+

[Dynamic directive arguments](syntax.html#Dynamic-Arguments) also work on `v-slot`, allowing the definition of dynamic slot names:

``` html
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

## Named Slots Shorthand

> New in 2.6.0+

Similar to `v-on` and `v-bind`, `v-slot` also has a shorthand, replacing everything before the argument (`v-slot:`) with the special symbol `#`. For example, `v-slot:header` can be rewritten as `#header`:

```html
<base-layout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
```

However, just as with other directives, the shorthand is only available when an argument is provided. That means the following syntax is invalid:

``` html
<!-- This will trigger a warning -->
<current-user #="{ user }">
  {{ user.firstName }}
</current-user>
```

Instead, you must always specify the name of the slot if you wish to use the shorthand:

``` html
<current-user #default="{ user }">
  {{ user.firstName }}
</current-user>
```

## Other Examples

**Slot props allow us to turn slots into reusable templates that can render different content based on input props.** This is most useful when you are designing a reusable component that encapsulates data logic while allowing the consuming parent component to customize part of its layout.

For example, we are implementing a `<todo-list>` component that contains the layout and filtering logic for a list:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

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

<<<<<<< HEAD
Em vez de codificar o conteúdo para cada _todo_, podemos deixar que o componente pai assuma o controle fazendo de todos _todo_ um _slot_, então ligando `todo` como uma prop de _slot_:
=======
Instead of hard-coding the content for each todo, we can let the parent component take control by making every todo a slot, then binding `todo` as a slot prop:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

```html
<ul>
  <li
    v-for="todo in filteredTodos"
    v-bind:key="todo.id"
  >
    <!--
<<<<<<< HEAD
    Nós temos um slot para cada _todo_ passando o objeto
    `todo` como uma prop de slot.
    -->
    <slot name="todo" v-bind:todo="todo">
      <!-- conteúdo fallback -->
=======
    We have a slot for each todo, passing it the
    `todo` object as a slot prop.
    -->
    <slot name="todo" v-bind:todo="todo">
      <!-- Fallback content -->
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
      {{ todo.text }}
    </slot>
  </li>
</ul>
```

<<<<<<< HEAD
Agora, quando usamos o componente `<todo-list>`, podemos opcionalmente definir um `<template>` alternativo para itens do _todo_, mas com acesso aos dados do filho:
=======
Now when we use the `<todo-list>` component, we can optionally define an alternative `<template>` for todo items, but with access to data from the child:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

```html
<todo-list v-bind:todos="todos">
  <template v-slot:todo="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```

<<<<<<< HEAD
No entanto, mesmo isso apenas arranha a superfície do que _slots_ com escopo são capazes de fazer. Para exemplos completos e reais de uso, recomendamos buscar mais sobre bibliotecas como [Vue Virtual Scroller](https://github.com/Akryum/vue-virtual-scroller), [Vue Promised](https://github.com/posva/vue-promised), e [Portal Vue](https://github.com/LinusBorg/portal-vue).

## Sintaxe Obsoleta

> A diretiva `v-slot` foi introduzida no Vue 2.6, oferecendo uma _API_ melhorada e alternativa aos atributos `slot` e `slot-scope` ainda suportados. O raciocínio completo para introduzir o `v-slot` é descrito neste [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md). Os atributos `slot` e `slot-scope` continuarão a ser suportados em todas as futuras versões 2.X, mas serão oficialmente descontinuados e serão eventualmente removidos no Vue 3.

### _Slots_ Nomeados com o atributo `slot`

> <abbr title="Ainda suportado em todas as versões 2.X do Vue, mas não mais recomendada.">Obsoleta</abbr> no 2.6.0+. Veja [aqui](#slots-nomeados) a nova e recomendada sintaxe.

Para passar conteúdos para os _slots_ nomeados a partir do pai, use o atributo especial `slot` no `<template>` (usando o componente `<base-layout>` descrito [aqui](#slots-nomeados) como exemplo):

<!-- To pass content to named slots from the parent, use the special `slot` attribute on `<template>` (using the `<base-layout>` component described [here](#slots-nomeados) as example): -->
=======
However, even this barely scratches the surface of what scoped slots are capable of. For real-life, powerful examples of scoped slot usage, we recommend browsing libraries such as [Vue Virtual Scroller](https://github.com/Akryum/vue-virtual-scroller), [Vue Promised](https://github.com/posva/vue-promised), and [Portal Vue](https://github.com/LinusBorg/portal-vue).

## Deprecated Syntax

> The `v-slot` directive was introduced in Vue 2.6.0, offering an improved, alternative API to the still-supported `slot` and `slot-scope` attributes. The full rationale for introducing `v-slot` is described in this [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md). The `slot` and `slot-scope` attributes will continue to be supported in all future 2.x releases, but are officially deprecated and will eventually be removed in Vue 3.

### Named Slots with the `slot` Attribute

> <abbr title="Still supported in all 2.x versions of Vue, but no longer recommended.">Deprecated</abbr> in 2.6.0+. See [here](#Named-Slots) for the new, recommended syntax.

To pass content to named slots from the parent, use the special `slot` attribute on `<template>` (using the `<base-layout>` component described [here](#Named-Slots) as example):
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

```html
<base-layout>
  <template slot="header">
<<<<<<< HEAD
    <h1>Aqui pode ser o título da página</h1>
  </template>

  <p>Um parágrafo para o conteúdo principal.</p>
  <p>E um outro parágrafo.</p>

  <template slot="footer">
    <p>Aqui algumas informações de contato</p>
=======
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template slot="footer">
    <p>Here's some contact info</p>
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
  </template>
</base-layout>
```

<<<<<<< HEAD
Ou o atributo `slot` que também pode ser usado diretamente em um elemento normal:

``` html
<base-layout>
  <h1 slot="header">Aqui pode ser o título da página</h1>

  <p>Um parágrafo para o conteúdo principal.</p>
  <p>E um outro parágrafo.</p>

  <p slot="footer">Aqui algumas informações de contato</p>
</base-layout>
```

Ainda pode haver um slot sem nome, que é o **_slot default_** que serve como um agrupador para qualquer conteúdo que esteja solto. Em ambos exemplos acima, o HTML renderizado seria:
=======
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
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

``` html
<div class="container">
  <header>
<<<<<<< HEAD
    <h1>Aqui pode ser o título da página</h1>
  </header>
  <main>
    <p>Um parágrafo para o conteúdo principal.</p>
    <p>E um outro parágrafo.</p>
  </main>
  <footer>
    <p>Aqui algumas informações de contato</p>
=======
    <h1>Here might be a page title</h1>
  </header>
  <main>
    <p>A paragraph for the main content.</p>
    <p>And another one.</p>
  </main>
  <footer>
    <p>Here's some contact info</p>
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
  </footer>
</div>
```

<<<<<<< HEAD
### Slots de Escopo Definido com o atributo `slot-scope`

> <abbr title="Ainda suportado em todas as versões 2.X do Vue, mas não mais recomendada.">Obsoleta</abbr> no 2.6.0+. Veja [aqui](#Scoped-Slots) a nova e recomendada sintaxe.

Para receber as props passadas para um _slot_, o componente pai pode usar o `<template>` com o atributo `slot-scope` (usando o `<slot-exemplo>` descrito no exemplo [aqui](#Scoped-Slots)):
=======
### Scoped Slots with the `slot-scope` Attribute

> <abbr title="Still supported in all 2.x versions of Vue, but no longer recommended.">Deprecated</abbr> in 2.6.0+. See [here](#Scoped-Slots) for the new, recommended syntax.

To receive props passed to a slot, the parent component can use `<template>` with the `slot-scope` attribute (using the `<slot-example>` described [here](#Scoped-Slots) as example):
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

``` html
<slot-example>
  <template slot="default" slot-scope="slotProps">
    {{ slotProps.msg }}
  </template>
</slot-example>
```

<<<<<<< HEAD
Aqui, o `slot-scope` declara o objeto props recebido como a variável `slotProps`, e o torna disponível dentro do escopo do `<template>`. Você pode nomear o `slotProps` da maneira que preferir, semelhante a argumentos de funções em JavaScript.

Aqui `slot=default` pode ser omitido como está implicitado:
=======
Here, `slot-scope` declares the received props object as the `slotProps` variable, and makes it available inside the `<template>` scope. You can name `slotProps` anything you like similar to naming function arguments in JavaScript.

Here `slot="default"` can be omitted as it is implied:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

``` html
<slot-example>
  <template slot-scope="slotProps">
    {{ slotProps.msg }}
  </template>
</slot-example>
```

<<<<<<< HEAD
O atributo `slot-scope` também pode ser usado diretamente em elementos que não sejam `<template>` (incluindo componentes):
=======
The `slot-scope` attribute can also be used directly on a non-`<template>` element (including components):
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

``` html
<slot-example>
  <span slot-scope="slotProps">
    {{ slotProps.msg }}
  </span>
</slot-example>
```

<<<<<<< HEAD
O valor de `slot-scope` pode aceitar qualquer expressão _JavaScript_ válida que pode aparecer na posição de argumento de uma definição de função. Isso significa que em ambientes suportados ([single-file components](single-file-components.html) ou [browsers modernos](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Operators/Atribuicao_via_desestruturacao#Compatibilidade_do_navegador)) você também pode usar [desestruturação de objetos do ES2015](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Operators/Atribuicao_via_desestruturacao#Desestrutura%C3%A7%C3%A3o_de_objeto) na expressão, da seguinte forma:
=======
The value of `slot-scope` can accept any valid JavaScript expression that can appear in the argument position of a function definition. This means in supported environments ([single-file components](single-file-components.html) or [modern browsers](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Browser_compatibility)) you can also use [ES2015 destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring) in the expression, like so:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

``` html
<slot-example>
  <span slot-scope="{ msg }">
    {{ msg }}
  </span>
</slot-example>
```

<<<<<<< HEAD
Usando o `<todo-list>` descrito [aqui](#Outros-exemplos) como um exemplo, aqui está o uso equivalente usando `slot-scope`:
=======
Using the `<todo-list>` described [here](#Other-Examples) as an example, here's the equivalent usage using `slot-scope`:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

``` html
<todo-list v-bind:todos="todos">
  <template slot="todo" slot-scope="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```
