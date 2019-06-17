---
title: Slots
type: guide
order: 104
---

> Esta página assume que você já leu o [Básico sobre Componentes](components.html). Leia lá primeiro se você for novo com componentes.

> Na versão 2.6, Nós introduzimos uma nova sintaxe unificada (A diretiva `v-slot`) para os slots nomeados e com escopo. Ele substitui os atributos `slot` e `slot-scope`, os quais estão agora obsoletos, mas _não_ foram removidos e ainda estão documentados [aqui](#Sintaxe-Obsoleta). Os fundamentos para a nova sintaxe estão descritos neste [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md).

## Conteúdo do _Slot_

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

Quando o componente renderizar, `<slot></slot>` será substituido por "Seu Perfil". Slots podem conter qualquer tipo de código template, incluindo _HTML_:

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

Se no `<navigation-link>`  **não** contém um elemento `<slot>`, qualquer conteúdo passado para ele simplesmente será descartado.

## Escopo de compilação

Quando você quer usar um dado dentro de um _slot_, como em:

``` html
<navigation-link url="/profile">
  Logado como {{ user.name }}
</navigation-link>
```

Esse `slot` tem acesso as mesmas propriedades da instância (isto é, ao mesmo "escopo") como o resto do _template_. O _slot_ **não** tem acesso ao escopo do `<navigation-link>`. Por exemplo, tentando acessar a `url` não funcionaria:

``` html
<navigation-link url="/profile">
  Clicando aqui te enviará para: {{ url }}
  <!--
  O `url` será undefined, porque esse conteúdo é passado
  para <navigation-link>, em vez de definido dentro do componente <navigation-link>.
  -->
</navigation-link>
```

Como regra, lembre-se de que:

> Tudo no _template_ pai é compilado no escopo do pai; Tudo no _template_ filho é compilado no escopo do filho.

## Conteúdo de _Fallback_

Há casos em que é útil especificar o conteúdo de _fallback_ (isto é, padrão) para um _slot_, a ser renderizado somente quando nenhum conteúdo é fornecido. Por exemplo, no componente `<submit-button>`:

```html
<button type="submit">
  <slot></slot>
</button>
```

Podemos querer que o texto "Enviar" seja renderizado dentro do `<button>` na maioria das vezes. para "Enviar" o conteúdo de _fallback_, podemos colocá-lo entre as _tags_ `<slot>`:

```html
<button type="submit">
  <slot>Enviar</slot>
</button>
```

Agora, quando usamos `<submit-button>` no componente pai, sem fornecer conteúdo para o _slot_:

```html
<submit-button></submit-button>
```

Irá renderizar o conteúdo de _fallback_, "Enviar":

```html
<button type="submit">
  Enviar
</button>
```

Mas se nós fornecermos conteúdo:

```html
<submit-button>
  Salvar
</submit-button>
```

Então, o conteúdo fornecido será renderizado:

```html
<button type="submit">
  Salvar
</button>
```

## Slots Nomeados

> Atualizado em 2.6.0+. [Veja aqui](#Sintaxe-Obsoleta) para a sintaxe obsoleta usando o atributo `slot`.

Há momentos em que é útil ter múltiplos elementos slots. Por exemplo, em um componente `<base-layout>` com o seguinte template:

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

Uma saída `<slot>` sem `name` tem implicitamente o nome _"default"_.

Para fornecer conteúdo para _slots_ nomeados, nós podemos usar a diretiva `v-slot` em um `<template>`, fornecendo o nome do _slot_ como argumento do `v-slot`:

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

Agora tudo dentro dos elementos `<template>` serão passados aos _slots_ correspondentes. Qualquer conteúdo não envolvido por um `<template>` usando `v-slot` é assumido como sendo o _slot default_.

No entanto, você ainda pode incluir o conteúdo do _slot default_ em um `<template>` se você deseja ser explícito:

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

De qualquer forma, O _HTML_ renderizado será:

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

Observe que **`v-slot` só pode ser adicionado a um `<template>`** (com [única exceção](#sintaxe-abreviada-para-slots-default)), ao contrário dos  [atributos `slot`](#Sintaxe-Obsoleta).

## Slots com Escopo Definido

> Atualizado em 2.6.0+. [Veja aqui](#Sintaxe-Obsoleta) para a sintaxe obsoleta usando o atributo `slot-scope`.

As vezes, é útil que o conteúdo do _slot_ tenha acesso aos dados disponíveis apenas no componente filho. Por exemplo, imagine um componente `<current-user>` com o seguinte _template_:

```html
<span>
  <slot>{{ user.lastName }}</slot>
</span>
```
Podemos querer substituir esse conteúdo de _fallback_ para exibir o primeiro nome do usuário, em vez do último, assim:

``` html
<current-user>
  {{ user.firstName }}
</current-user>
```

Isso não funcionará, no entanto, porque somente o componente `<current-user>` tem acesso ao `user` e o conteúdo que estamos fornecendo é renderizado no pai.

Para tornar `user` disponível para o conteúdo do _slot_ no pai, podemos vincular `user` como um atributo do elemento `<slot>`:

``` html
<span>
  <slot v-bind:user="user">
    {{ user.lastName }}
  </slot>
</span>
```

Atributos vinculados a um elemento `<slot>` são chamados de  **props do _slot_**. Agora, no escopo pai, podemos usar o `v-slot` com um valor para definir um nome para as propriedades _slot_ que fornecemos:

``` html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>
```

Neste exemplo, escolhemos nomear o objeto que contém todos os nossos props do _slot_ `slotProps`, mas você pode usar qualquer nome que desejar.

### Sintaxe abreviada para slots default

Em casos como acima, quando _apenas_ o _slot default_ é fornecido, as _tags_ do componente podem ser usadas como _template_. Isso nos permite usar o `v-slot` diretamente no componente:

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
<!-- INVÁLIDO, resultará em aviso -->
<current-user v-slot="slotProps">
  {{ slotProps.user.firstName }}
  <template v-slot:other="otherSlotProps">
    slotProps NÃO está disponível aqui!
  </template>
</current-user>
```

Sempre que houver vários _slots_, use a sintaxe completa com base em `<template>` para _todos_ os _slots_:

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
Isso significa que o valor de `v-slot` pode realmente aceitar qualquer expressão JavaScript válida que possa aparecer na posição de argumento em uma definição de função. Então, em ambientes suportados ([componentes single-file](single-file-components.html) ou [browsers modernos](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Operators/Atribuicao_via_desestruturacao#Compatibilidade_do_navegador)), você também pode usar [desestruturação de objetos do ES2015](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Operators/Atribuicao_via_desestruturacao#Desestrutura%C3%A7%C3%A3o_de_objeto) para usar props específicos de slots, assim:

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

Você pode até mesmo definir _fallbacks_, para serem usados no caso de um prop do _slot_ ser `undefined`:

``` html
<current-user v-slot="{ user = { firstName: 'Guest' } }">
  {{ user.firstName }}
</current-user>
```

## Slots de nomes dinâmicos

> Novo na versão 2.6.0+

[Argumentos de diretiva dinâmicos](syntax.html#Argumentos-Dinamicos) também funciona no `v-slot`, permitindo a definição de nomes de slots dinâmicos:

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

**Props de _slot_ nos permitem transformar slots em templates reutilizáveis que podem renderizar diferentes conteúdos com base na inserção de props.** Isso é mais útil quando você está projetando um componente reutilizável que encapsula a lógica de dados, enquanto permite que o componente pai consumidor personalize parte de seu _layout_.

Por exemplo, estamos implementando um componente `<todo-list>` que contém o _layout_ e a lógica de filtragem para uma lista:

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

Em vez de codificar o conteúdo para cada _todo_, podemos deixar que o componente pai assuma o controle fazendo de todos _todo_ um _slot_, então vinculando `todo` como uma prop de _slot_:

```html
<ul>
  <li
    v-for="todo in filteredTodos"
    v-bind:key="todo.id"
  >
    <!--
    Nós temos um slot para cada _todo_ passando o objeto
    `todo` como uma prop de slot.
    -->
    <slot name="todo" v-bind:todo="todo">
      <!-- conteúdo fallback -->
      {{ todo.text }}
    </slot>
  </li>
</ul>
```

Agora, quando usamos o componente `<todo-list>`, podemos opcionalmente definir um `<template>` alternativo para itens do _todo_, mas com acesso aos dados do filho:

```html
<todo-list v-bind:todos="todos">
  <template v-slot:todo="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```

No entanto, mesmo isso apenas arranha a superfície do que _slots_ com escopo são capazes de fazer. Para exemplos completos e reais de uso, recomendamos buscar mais sobre bibliotecas como [Vue Virtual Scroller](https://github.com/Akryum/vue-virtual-scroller), [Vue Promised](https://github.com/posva/vue-promised), e [Portal Vue](https://github.com/LinusBorg/portal-vue).

## Sintaxe Obsoleta

> A diretiva `v-slot` foi introduzida no Vue 2.6.0, oferecendo uma _API_ melhorada e alternativa aos atributos `slot` e `slot-scope` ainda suportados. O raciocínio completo para introduzir o `v-slot` é descrito neste [RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md). Os atributos `slot` e `slot-scope` continuarão a ser suportados em todas as futuras versões 2.X, mas serão oficialmente descontinuados e serão eventualmente removidos no Vue 3.

### _Slots_ Nomeados com o atributo `slot`

> <abbr title="Ainda suportado em todas as versões 2.X do Vue, mas não mais recomendada.">Obsoleta</abbr> no 2.6.0+. Veja [aqui](#slots-nomeados) a nova e recomendada sintaxe.

Para passar conteúdos para os _slots_ nomeados a partir do pai, use o atributo especial `slot` no `<template>` (usando o componente `<base-layout>` descrito [aqui](#slots-nomeados) como exemplo):

```html
<base-layout>
  <template slot="header">
    <h1>Aqui pode ser o título da página</h1>
  </template>

  <p>Um parágrafo para o conteúdo principal.</p>
  <p>E um outro parágrafo.</p>

  <template slot="footer">
    <p>Aqui algumas informações de contato</p>
  </template>
</base-layout>
```

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

``` html
<div class="container">
  <header>
    <h1>Aqui pode ser o título da página</h1>
  </header>
  <main>
    <p>Um parágrafo para o conteúdo principal.</p>
    <p>E um outro parágrafo.</p>
  </main>
  <footer>
    <p>Aqui algumas informações de contato</p>
  </footer>
</div>
```

### Slots de Escopo Definido com o atributo `slot-scope`

> <abbr title="Ainda suportado em todas as versões 2.X do Vue, mas não mais recomendada.">Obsoleta</abbr> no 2.6.0+. Veja [aqui](#Scoped-Slots) a nova e recomendada sintaxe.

Para receber as props passadas para um _slot_, o componente pai pode usar o `<template>` com o atributo `slot-scope` (usando o `<slot-exemplo>` descrito no exemplo [aqui](#Scoped-Slots)):

``` html
<slot-example>
  <template slot="default" slot-scope="slotProps">
    {{ slotProps.msg }}
  </template>
</slot-example>
```

Aqui, o `slot-scope` declara o objeto props recebido como a variável `slotProps`, e o torna disponível dentro do escopo do `<template>`. Você pode nomear o `slotProps` da maneira que preferir, semelhante a argumentos de funções em JavaScript.

Aqui `slot=default` pode ser omitido como está implicitado:

``` html
<slot-example>
  <template slot-scope="slotProps">
    {{ slotProps.msg }}
  </template>
</slot-example>
```

O atributo `slot-scope` também pode ser usado diretamente em elementos que não sejam `<template>` (incluindo componentes):

``` html
<slot-example>
  <span slot-scope="slotProps">
    {{ slotProps.msg }}
  </span>
</slot-example>
```

O valor de `slot-scope` pode aceitar qualquer expressão JavaScript válida que pode aparecer na posição de argumento de uma definição de função. Isso significa que em ambientes suportados ([single-file components](single-file-components.html) ou [browsers modernos](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Operators/Atribuicao_via_desestruturacao#Compatibilidade_do_navegador)) você também pode usar [desestruturação de objetos do ES2015](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Operators/Atribuicao_via_desestruturacao#Desestrutura%C3%A7%C3%A3o_de_objeto) na expressão, da seguinte forma:

``` html
<slot-example>
  <span slot-scope="{ msg }">
    {{ msg }}
  </span>
</slot-example>
```

Usando o `<todo-list>` descrito [aqui](#Outros-exemplos) como um exemplo, aqui está o uso equivalente usando `slot-scope`:

``` html
<todo-list v-bind:todos="todos">
  <template slot="todo" slot-scope="{ todo }">
    <span v-if="todo.isComplete">✓</span>
    {{ todo.text }}
  </template>
</todo-list>
```
