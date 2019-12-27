---
title: Propriedades
type: guide
order: 102
---

> Esta página assume que você já leu o [Básico sobre Componentes](components.html). Leia lá primeiro se você for novo com componentes.

## Notação (camelCase vs. kebab-case)

O nome de atributos do HTML são insensíveis a maiúsculas e minúsculas, dessa forma os navegadores irão interpretar qualquer letra maiúscula como minúscula. Isso significa que quando você está usando _templates_ diretamente no DOM (quando o Vue é utilizado diretamente em uma página HTML existente), nomes de propriedades em camelCase precisam utilizar os seus equivalentes em kebab-case (delimitados por hífen):

``` js
Vue.component('blog-post', {
  // camelCase em JavaScript
  props: ['postTitle'],
  template: '<h3>{{ postTitle }}</h3>'
})
```

``` html
<!-- kebab-case em HTML -->
<blog-post post-title="hello!"></blog-post>
```

Novamente, utilizando _templates_ baseados em Strings, tal limitação não se aplica.

## Tipos de Propriedades

Até aqui, nós apenas vimos propriedades listadas como um Array de Strings:

```js
props: ['title', 'likes', 'isPublished', 'commentIds', 'author']
```

Geralmente, você desejará que cada propriedade tenha um tipo específico de valor. Nesses casos, você pode listar as propriedades como um objeto, onde as chaves e os valores contém os nomes e o tipos das propriedades, respectivamente:

```js
props: {
  title: String,
  likes: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object,
  callback: Function,
  contactsPromise: Promise // or any other constructor
}
```

Isso não apenas documenta o seu componente, mas também avisará os usuários no console de JavaScript do navegador se eles atribuírem um tipo incorreto. Você aprenderá muito mais sobre [checagem de tipos e outras validações de propriedades](#Prop-Validation) logo abaixo nesta página.

## Passando Propriedades Estáticas ou Dinâmicas

Até aqui, você viu propriedades passadas com um valor estático, por exemplo:

```html
<blog-post title="Minha jornada com Vue"></blog-post>
```

Você também viu propriedades associadas dinamicamente com `v-bind`, como em:

```html
<!-- Associando um valor dinamicamente a uma variável -->
<blog-post v-bind:title="post.title"></blog-post>

<!-- Associando dinamicamente o valor de uma expressão complexa -->
<blog-post
  v-bind:title="post.title + ' by ' + post.author.name"
></blog-post>
```

Nos dois exemplos acima, aconteceu de passarmos valores do tipo String, mas _qualquer_ tipo de dado pode ser utilizado em uma propriedade.

### Passando um Número

```html
<!-- Embora `42` seja estático, usamos v-bind para dizer ao Vue -->
<!-- que essa é uma expressão JavaScript em vez de uma String.  -->
<blog-post v-bind:likes="42"></blog-post>

<!-- Associando dinamicamente através do valor de uma variável. -->
<blog-post v-bind:likes="post.likes"></blog-post>
```

### Passando um Booleano

```html
<!-- Incluindo a propriedade sem nenhum valor implicará em `true`. -->
<blog-post is-published></blog-post>

<!-- Embora `false` seja estático, usamos v-bind para dizer ao Vue -->
<!-- que essa é uma expressão JavaScript em vez de uma String.     -->
<blog-post v-bind:is-published="false"></blog-post>

<!-- Associando dinamicamente através do valor de uma variável. -->
<blog-post v-bind:is-published="post.isPublished"></blog-post>
```

### Passando um Array

```html
<!-- Embora o Array seja estático, usamos v-bind para dizer ao Vue -->
<!-- que essa é uma expressão JavaScript em vez de uma String.     -->
<blog-post v-bind:comment-ids="[234, 266, 273]"></blog-post>

<!-- Associando dinamicamente através do valor de uma variável. -->
<blog-post v-bind:comment-ids="post.commentIds"></blog-post>
```

### Passando um Objeto

```html
<!-- Embora o Object seja estático, usamos v-bind para dizer ao Vue -->
<!-- que essa é uma expressão JavaScript em vez de uma String.      -->
<blog-post
  v-bind:author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
></blog-post>

<!-- Associando dinamicamente através do valor de uma variável. -->
<blog-post v-bind:author="post.author"></blog-post>
```

### Passando as Propriedades de um Objeto

Se você precisar passar todas as propriedades de um objeto como parâmetro, pode utilizar `v-bind` sem argumentos (`v-bind` em vez de `v-bind:nome-da-propriedade`). Por exemplo, dado o objeto `post`:

``` js
post: {
  id: 1,
  title: 'Minha jornada com Vue'
}
```

O _template_ a seguir:

``` html
<blog-post v-bind="post"></blog-post>
```

Será equivalente a:

``` html
<blog-post
  v-bind:id="post.id"
  v-bind:title="post.title"
></blog-post>
```

## Fluxo de Dados Unidirecional

Todas as propriedades formam uma **atribuição unidirecional** entre as propriedades do filho e a do pai: quando as propriedades do pai atualizam, seguirão um fluxo para os filhos, mas não o contrário. Isso previne que os componentes filhos acidentalmente mudem o estado do pai, o que pode tornar o fluxo de dados da sua aplicação demasiadamente complexo de entender.

Além disso, cada vez que o componente pai é atualizado, todas as propridades no filho serão atualizadas com o valor mais recente. Isso significa que você **não** deveria tentar mudar uma propriedade dentro de um componente filho. Se fizer isso, o Vue irá te avisar no console.

Normalmente há dois casos onde você tentará mudar uma propriedade:

1. **A propriedade é utilizada para passar um valor inicial; logo após, o componente filho quer utiliza-lá como um dado local.** Neste caso, é melhor definir um dado local que utiliza a propriedade atribuída como valor inicial:

  ``` js
  props: ['initialCounter'],
  data: function () {
    return {
      counter: this.initialCounter
    }
  }
  ```

2. **A propriedade é passada como um valor primitivo que precisa ser transformado.** Neste caso, é melhor definir um dado computado local que utiliza a propriedade atribuída como valor inicial:

  ``` js
  props: ['size'],
  computed: {
    normalizedSize: function () {
      return this.size.trim().toLowerCase()
    }
  }
  ```

<p class="tip">Lembre-se que Objects e Arrays em JavaScript são passados por referência, então, se a propriedade é de um destes tipos, alterá-la diretamente dentro do componente filho **irá** alterar o estado do componente pai.</p>

## Validação de Propriedades

<<<<<<< HEAD
Componentes podem especificar requisitos para suas propriedades, como, por exemplo, os tipos de dados que você já viu. Se um requisito não é obedecido, o Vue irá avisar no console JavaScript do navegador. Isso é especialmente útil quando se está desenvolvendo um componente que será utilizado por outros.
=======
Components can specify requirements for their props, such as the types you've already seen. If a requirement isn't met, Vue will warn you in the browser's JavaScript console. This is especially useful when developing a component that's intended to be used by others.
>>>>>>> e91a60306843e19d3cd2a3d76856a8433a7d895e

Para especificar validações de propriedades, atribua um objeto com requisitos de validações ao valor de `props`, ao invés de um Array de Strings. Exemplos:

``` js
Vue.component('my-component', {
  props: {
    // Checagem básica de tipos (`null` and `undefined` passarão em qualquer validação de tipo)
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
      // Objetos ou Arrays padrões devem ser retornadas
      // a partir de uma função fabricadora
      default: function () {
        return { message: 'hello' }
      }
    },
    // Função personalizada de validação
    propF: {
      validator: function (value) {
        // O valor precisa corresponder a alguma dessas Strings
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
  }
})
```

Quando validações de propriedades falham, o Vue produzirá um aviso no console (se você estiver em um ambiente de desenvolvimento).

<p class="tip">Observe que propriedades são validadas **antes** da instância de um componente ser criada, ou seja, propriedades das instâncias (como `data`, `computed`, etc.) não estarão disponíveis dentro das funções `default` ou `validator`.</p>

### Checagem de Tipos

O `type` pode ser um dos seguintes construtores nativos:

- String
- Number
- Boolean
- Array
- Object
- Date
- Function
- Symbol

Além disso, `type` também pode ser uma função construtora e sua asserção será feita através de `instanceof`. Por exemplo, dada a seguinte função construtora:

```js
function Person (firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}
```

Você pode utilizar:

```js
Vue.component('blog-post', {
  props: {
    author: Person
  }
})
```

Assim, você valida se o valor da propriedade `author` será criado com `new Person`.

## Atributos Não-Propriedades

Um atributo não-propriedade é um atributo passado para um componente, sem uma propriedade correspondente definida.

Enquanto propriedades explicitamente definidas são preferidas para passar informações a um componente filho, autores de bibliotecas de componentes não podem prever sempre o contexto em que seus componentes serão utilizados. Por isso componentes podem aceitar atributos arbitrários, incluídos apenas no elemento raiz do componente.

Por exemplo, imagine que você está utilizando um componente de terceiros chamado `bootstrap-date-input` com um _plugin_ do Bootstrap que requer um atributo `data-date-picker` no `input`. Podemos adicionar o atributo à instância:

``` html
<bootstrap-date-input data-date-picker="activated"></bootstrap-date-input>
```

E o atributo `data-date-picker="activated"` será automaticamente adicionado ao elemento raiz do `bootstrap-date-input`.

### Substituindo/Mesclando Atributos

Imagine que esse é o _template_ para `bootstrap-date-input`:

``` html
<input type="date" class="form-control">
```

Para específicar um tema para nosso _plugin_ de data, talvez precisemos adicionar classes específicas, por exemplo:

``` html
<bootstrap-date-input
  data-date-picker="activated"
  class="date-picker-theme-dark"
></bootstrap-date-input>
```

Neste caso, dois valores diferentes para `class` são definidos:

- `form-control`, o qual é atribuído pelo componente ao seu _template_
- `date-picker-theme-dark`, o qual é atribuído ao componente pelo seu pai

Para a maioria dos atributos, o valor fornecido ao componente irá substituir o valor atribuído pelo componente. Por exemplo, passando `type="text"` irá substituir `type="date"` e provávelmente quebrar o componente! Felizmente, os atributos `class` e `style` são um pouco mais inteligentes, sendo automaticamente mesclados e criando um valor final composto: `form-control date-picker-theme-dark`.

### Desabilitando Herança de Atributos

Se você **não** quiser que o elemento raíz de um componente receba atributos de herança, você pode atribuir `inheritAttrs: false` nas opções do componente. Por exemplo:

```js
Vue.component('my-component', {
  inheritAttrs: false,
  // ...
})
```

Isso pode ser especialmente útil quando combinado com a propridade de instância `$attrs`, que contem os atributos e valores passados a um componente, por exemplo:

```js
{
  required: true,
  placeholder: 'Digite seu usuário'
}
```

Com `inheritAttrs: false` e `$attrs`, você pode decidir para qual elemento interno deseja encaminhar os atributos, geralmente desejável para [componentes base](../style-guide/#Base-component-names-strongly-recommended):

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

<p class="tip">Observe que a opção `inheritAttrs: false` **não** afeta vínculos `style` e `class`.</p>

Esse padrão permite utilizar componentes base mais parecidos com elementos padrão do HTML, sem a necessidade de se preocupar sobre qual elemento está na raiz:

```html
<base-input
  v-model="username"
  required
  placeholder="Digite seu usuário"
></base-input>
```
