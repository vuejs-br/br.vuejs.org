---
title: Propriedades
type: guide
order: 102
---

> Esta página assume que você já leu o [Básico sobre Componentes](components.html). Leia lá primeiro se você for novo com componentes.

## Prop Casing (camelCase vs kebab-case)

O nome de atributos do HTML são insensíveis a maiúsculas e minúsculas, dessa forma os navegadores irão interpretar qualquer letra maiúscula como minúscula. Isso significa que quando você está usando in-DOM templates (quando o Vue é utilizado logo em uma página HTML), nomes de propriedades em camelCase precisam utilizar os seus equivalentes em kebab-case (delimitados por hífen):

``` js
Vue.component('blog-post', {
  // camelCase em JavaScript
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
``


``` html
<!-- kebab-case em HTML -->
<blog-post post-title="hello!"></blog-post>
```
Novamente, se você está utilizando string templates (como os arquivos .vue), essa limitação não se aplica.


## Tipos de propriedades

Até aqui, nós apenas vimos propriedades listadas como um array de strings:

```js
props: ['title', 'likes', 'isPublished', 'commentIds', 'author']
```

Geralmente, você irá querer que cada propriedade tenha um tipo específico de valor. Nesses casos, você pode listar as propriedades como um objeto, onde as chaves e os valores contém os nomes e o tipos das propriedades, respectivamente:

```js
props: {
  title: String,
  likes: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object
}
```

Isso não apenas documenta o seu componente, mas também irá avisar os usuários no console de Javascript do navegador se eles atribuírem um tipo errado. Você irá aprender muito mais sobre [checagem de tipos e outras validações de propriedades](#Prop-Validation) logo abaixo nesta página.

## Passando propriedades estáticas ou dinamicas

Até aqui, você viu propriedades sendo passadas através de um valor estático, por exemplo:

```html
<blog-post title="Minha jornada com Vue"></blog-post>
```

Você também viu propriedades sendo associadas dinamicamente com `v-bind`, como em:

```html
<!-- Associando um valor dinamicamente a uma variável -->
<blog-post v-bind:title="post.title"></blog-post>

<!-- Associando dinamicamente o valor de uma expressão complexa -->
<blog-post v-bind:title="post.title + ' by ' + post.author.name"></blog-post>
```

Nos dois exemplos acima, acontece de nós passarmos valores do tipo string, mas _qualquer_ tipo de valor pode ser passado.

### Passando um Número

```html
<!-- Embora `42` seja estático, nós precisamos da v-bind para dizer ao Vue que -->
<!-- essa é uma expressão em Javascript em vez de uma string.       -->
<blog-post v-bind:likes="42"></blog-post>

<!-- Associando dinamicamente através do valor de uma variável. -->
<blog-post v-bind:likes="post.likes"></blog-post>
```

### Passando um Booleano

```html
<!-- Incluindo a propriedade sem nenhum valor irá implicar em `true`. -->
<blog-post is-published></blog-post>

<!-- Embora `false` seja estático, nós precisamos da v-bind para dizer ao Vue que -->
<!-- essa é uma expressão em Javascript em vez de uma string.          -->
<blog-post v-bind:is-published="false"></blog-post>

<!-- Associando dinamicamente através do valor de uma variável. -->
<blog-post v-bind:is-published="post.isPublished"></blog-post>
```

### Passando um Array

```html
<!-- Embora o array seja estático, nós precisamos da v-bind para dizer ao Vue que -->
<!-- essa é uma expressão em Javascript em vez de uma string.            -->
<blog-post v-bind:comment-ids="[234, 266, 273]"></blog-post>

<!-- Associando dinamicamente através do valor de uma variável. -->
<blog-post v-bind:comment-ids="post.commentIds"></blog-post>
```

### Passando um Objeto

```html
<!-- Embora o objeto seja estático, nós precisamos da v-bind para dizer ao Vue que -->
<!-- essa é uma expressão em Javascript em vez de uma string.             -->
<blog-post v-bind:author="{ name: 'Veronica', company: 'Veridian Dynamics' }"></blog-post>

<!-- Associando dinamicamente através do valor de uma variável. -->
<blog-post v-bind:author="post.author"></blog-post>
```

### Passando as propriedades de um Objeto

Se você gostaria de passar todas as propriedades de um objeto como parâmetros, você pode utilizar `v-bind` sem nenhum argumento (`v-bind` em vez de `v-bind:nome-da-propriedade`). Por exemplo, dado o objeto `post`:

``` js
post: {
  id: 1,
  title: 'Minha jornada com Vue'
}
```

Os seguintes templates:

``` html
<blog-post v-bind="post"></blog-post>
```

Serão equivalentes a:

``` html
<blog-post
  v-bind:id="post.id"
  v-bind:title="post.title"
></blog-post>
```

## Fluxo de dados unidirecional

Todas as propriedades formam uma **atribuição unidirecional** entre as propriedades do filho e a do pai: quando as propriedades do pai atualizam, irá seguir um fluxo para os filhos, mas não ao contrário. Isso irá prever que os componentes filhos acidentalmente mudem o estado do pai, o que pode fazer o fluxo de dados da sua aplicação complexo para entender.

Além disso, cada vez que o componente pai é atualizado, todas as propridades no filho serão atualizadas com o último valor. Isso significa que você **não** deveria tentar mudar uma propriedade dentro de um componente filho. Se você fizer, o Vue irá te avisar no console.

Normalmente há dois casos onde você tentará mudar uma propriedade:

1. **A propriedade é utilizada para passar um valor inicial; logo após, o componente filho quer utiliza-lá como um dado local.** Neste caso, é melhor definir uma propriedade local que utiliza a propriedade atribuída como valor inicial:

  ``` js
  props: ['initialCounter'],
  data: function () {
    return {
      counter: this.initialCounter
    }
  }
  ```

2. **A propriedade é passada como um valor cru que precisa ser transformado.** Neste caso, o melhor é definir uma propriedade computada utilizando o valor da propriedade atribuída como valor inicial:

  ``` js
  props: ['size'],
  computed: {
    normalizedSize: function () {
      return this.size.trim().toLowerCase()
    }
  }
  ```

<p class="tip">Note que objetos e arrays em Javascript são passados através de referência, então se a propriedade é um array ou objeto, mudar o array ou objeto dentro do componente filho **irá** alterar o estado do componente pai.</p>

## Validação de Propriedades

Componentes podem especificar os requisitos para suas propriedades, como por exemplo os tipos que você já viu. Se um requisito não é obedecido, o Vue irá avisar você no console Javascript do navegador. Isso é especialmente útil quando se está desenvolvendo um componente que será utilizado por outros.

Para especificar validações de propriedades, você pode atribuir um objeto com requisitos de validações ao valor de `props`, ao invés de um array de strings. Por exemplo:

``` js
Vue.component('my-component', {
  props: {
    // Checagem básica de tipos (`null` corresponde a qualquer tipo)
    propA: Number,
    // Multíplos tipos possíveis
    propB: [String, Number],
    // String obrigatória
    propC: {
      type: String,
      required: true
    },
    // Número com um valor padrão
    propD: {
      type: Number,
      default: 100
    },
    // Objeto com um valor padrão
    propE: {
      type: Object,
      // Objetos ou arrays padrões devem ser retornadas de
      // uma função fabricadora
      default: function () {
        return { message: 'hello' }
      }
    },
    // Função customizada de validação
    propF: {
      validator: function (value) {
        // O valor precisa corresponder a alguma dessas strings
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
  }
})
```

Quando validações de propriedades falham, o Vue irá produzir um aviso de console (se você estiver um ambiente de desenvolvimento).

<p class="tip">Note que propriedades são validadas **antes** da instância de um componente ser criada, então propriedades de instâncias (por exemplo: `data`, `computed`, etc) não irão estar disponíveis dentro de funções `padrão` ou de `validação`.</p>

### Checagem de tipos

O `type` pode ser um dos seguintes construtores:

- String
- Number
- Boolean
- Array
- Object
- Date
- Function
- Symbol

Além disso, `type` também pode ser uma função construtora e sua asserção será feita através de uma checagem de `instanceof`. Por exemplo, dado a seguinte função construtora existente:

```js
function Person (firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}
```

você pode utilizar:

```js
Vue.component('blog-post', {
  props: {
    author: Person
  }
})
```

para validar que o valor da propriedade `author` foi criada com `new Person`.

## Atributos não propriedades

Um atributo não propriedade é um atributo que é passado para um componente, mas não tem uma propriedade correspondente definida.

Enquanto propriedades explicitamente definidas são preferidas para passar informações a um componente filho, autores de bibliotecas de componentes não podem sempre prever o contexto ao qual seus componentes podem ser utilizados. É por isso que componentes podem aceitar atributos arbitrários, que são incluídos ao elemento raiz do componente.

Por exemplo, imagine que você está utilizando um componente de terceiros chamado `bootstrap-date-input` com um plugin do Bootstrap que requer um atributo `data-date-picker` no `input`. Nós podemos adicionar esse atributo a instância do nosso componente:

``` html
<bootstrap-date-input data-date-picker="activated"></bootstrap-date-input>
```

E o atributo `data-date-picker="activated"` irá automaticamente ser adicionado ao elemento raiz de `bootstrap-date-input`.

### Substituindo/Fundindo com atributos existentes

Imagine que esse é o template para `bootstrap-date-input`:

``` html
<input type="date" class="form-control">
```

Para específicar um tema para nosso plugin de data, nós talvez precisamos adicionar classes específicas, por exemplo:

``` html
<bootstrap-date-input
  data-date-picker="activated"
  class="date-picker-theme-dark"
></bootstrap-date-input>
```

Neste caso, dois valores diferentes para `class` são definidos:

- `form-control`, o qual é atribuído pelo componente ao seu template
- `date-picker-theme-dark`, o qual é atribuído ao componente pelo seu pai

Para a maioria dos atributos, o valor fornecido ao componente irá substituir o valor atribuído pelo componente. Então para exemplo, passando `type="text"` irá substituir `type="date"` e provávelmente irá quebrá-lo! Felizmente, os atributos `class` e `style` são um pouco mais inteligentes, os dois irão ser fundidos, criando um valor final: `form-control date-picker-theme-dark`.

### Desabilitando a herança de atributos

Se você **não** gostaria que o elemento raíz de um componente receba atributos de herança, você pode atribuir `inheritAttrs: false` nas opções do componente. Por exemplo:

```js
Vue.component('my-component', {
  inheritAttrs: false,
  // ...
})
```

Isso pode ser especialmente útil quando combinado com a propridade de instância `$attrs`, que contem os nomes de atributos e seus valores passados a um componente, por exemplo:

```js
{
  class: 'username-input',
  placeholder: 'Digite seu usuário'
}
```

Com `inheritAttrs: false` e `$attrs` você pode decidir manualmente para qual elemento deseja encaminhar os atributos, o que geralmente é desejável para [componentes base](../style-guide/#Base-component-names-strongly-recommended):

```js
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  template: `
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      >
    </label>
  `
})
```

Esse padrão permite que você use componentes base mais como elementos padrões do HTML, sem a necessidade de ter que se preocupar sobre qual elemento está em sua raíz:

```html
<base-input
  v-model="username"
  class="username-input"
  placeholder="Digite seu usuário"
></base-input>
```
