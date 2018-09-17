---
title: Básico sobre Componentes
type: guide
order: 11
---

## Exemplo Base

Aqui está um exemplo de um componente Vue:

``` js
// Definindo novo componente chamado button-counter
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count++">Você clicou em mim {{ count }} vezes.</button>'
})
```

Componentes são instâncias reutilizáveis do Vue com um nome: Nesse caso, `<button-counter>`. Podemos usar esses componentes como um elemento personalizado dentro da instância Vue raiz criada com `new Vue`:

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
```

```js
new Vue({ el: '#components-demo' })
```

{% raw %}
<div id="components-demo" class="demo">
  <button-counter></button-counter>
</div>
<script>
Vue.component('button-counter', {
  data: function () {
    return {
      count: 0
    }
  },
  template: '<button v-on:click="count += 1">Você clicou em mim {{ count }} vezes.</button>'
})
new Vue({ el: '#components-demo' })
</script>
{% endraw %}

Como componentes são instância Vue reutilizáveis, eles aceitam as mesmas opções que `new Vue`, como `data`, `computed`, `watch`, `methods` e gatilhos de ciclo de vida. As únicas exceções são as poucas opções específicas de raiz, como `el`.

## Reutilizando Componentes

Componentes podem ser reutilizados quantas vezes você quiser:

```html
<div id="components-demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

{% raw %}
<div id="components-demo2" class="demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
<script>
new Vue({ el: '#components-demo2' })
</script>
{% endraw %}

Perceba que ao clicar nos botões, cada um mantêm seu próprio e único `count`. Isso acontece porque cada vez que você usa um componente, uma nova  **instância** dele é criada.

### Use `data` como uma Função

Quando definimos o componente `<button-counter>`, você talvez percebeu que a opção `data` não recebeu um objeto, como abaixo:

```js
data: {
  count: 0
}
```

Em vez disso, **a opção `data` de um componente precisa ser uma função**, para que cada instância possa manter uma cópia independente do object data retornado:

```js
data: function () {
  return {
    count: 0
  }
}
```

Se Vue não tivesse essa regra, clicar em um botão afetaria a data de _todas outras instâncias_, como abaixo:

{% raw %}
<div id="components-demo3" class="demo">
  <button-counter2></button-counter2>
  <button-counter2></button-counter2>
  <button-counter2></button-counter2>
</div>
<script>
var buttonCounter2Data = {
  count: 0
}
Vue.component('button-counter2', {
  data: function () {
    return buttonCounter2Data
  },
  template: '<button v-on:click="count++">Você clicou em mim {{ count }} vezes.</button>'
})
new Vue({ el: '#components-demo3' })
</script>
{% endraw %}

## Organizando Componentes

É comum que um app seja organizado em uma árvore de componentes aninhados:

![Component Tree](/images/components.png)

Por exemplo, você pode ter componentes para o cabeçalho, barra lateral e área de conteúdo, cada um normalmente contendo outros componentes para navegação, como links, postagens de blog, etc.

Para usar esses componentes em templates, eles devem ser registrados para que Vue saiba deles. Há dois tipos de registro de componentes, sendo eles: **global** e **local**. Até agora, nós só registramos componentes de maneira global, usando `Vue.component`:

```js
Vue.component('my-component-name', {
  // ... options ...
})
```

Componentes registrados globalmente podem ser usados no template de qualquer instância Vue raiz (`new Vue`) criada posteriormente -- e até mesmo dentro de todos subcomponentes dessa árvore de componentes da instância Vue.

Isso é tudo que você precisa saber sobre registro por hora, mas assim que você terminar de ler essa página e se sentir confortável com o conteúdo, recomendamos retornar mais tarde para ler o guia completo em [Component Registration](components-registration.html).

## Passando Dados aos Filhos com Props

Anteriormente falamos sobre criar componentes para postagens de blog. O problema é que, o componente não será útil a não ser que você possa passar dados para ele, como título e conteúdo da postagem específica que desejamos mostrar. É aí que props entra.

Props são atributos personalizáveis que você pode registrar em um componente. Quando um valor é passado para um atributo prop, ele torna-se uma propriedade daquela instância de componente. Para passar um título ao nosso componente de postagem de blog, podemos incluir na lista de props que esse componente aceita, usando a opção `props`:

```js
Vue.component('blog-post', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
```

Um componente pode ter quantos props você quiser, e por padrão, qualquer valor pode ser passado para qualquer prop. No template acima, você verá que nós podemos acessar esse valor dentro da instância do componente, da mesma forma como `data`.

Assim que um prop é registrado, você pode passar dados para ele como um atributo personalizado, dessa forma:

```html
<blog-post title="Minha jornada com Vue"></blog-post>
<blog-post title="Postagens com Vue"></blog-post>
<blog-post title="Porque Vue é tão divertido"></blog-post>
```

{% raw %}
<div id="blog-post-demo" class="demo">
  <blog-post1 title="Minha jornada com Vue"></blog-post1>
  <blog-post1 title="Postagens com Vue"></blog-post1>
  <blog-post1 title="Porque Vue é tão divertido"></blog-post1>
</div>
<script>
Vue.component('blog-post1', {
  props: ['title'],
  template: '<h3>{{ title }}</h3>'
})
new Vue({ el: '#blog-post-demo' })
</script>
{% endraw %}

Todavia, tipicamente você vai querer ter um array de posts em `data`:

```js
new Vue({
  el: '#blog-post-demo',
  data: {
    posts: [
      { id: 1, title: 'Minha jornada com Vue' },
      { id: 2, title: 'Postagens com Vue' },
      { id: 3, title: 'Porque Vue é tão divertido' }
    ]
  }
})
```

Quando quisermos renderizar um componente para cada:

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:title="post.title"
></blog-post>
```

No exemplo acima, você verá que podemos usar `v-bind` para dinamicamente passar props. Isso é especialmente útil quando você não sabe, de antemão, exatamente que tipo de conteúdo irá renderizar, como ao [obter postagens de uma API](https://jsfiddle.net/chrisvfritz/sbLgr0ad).

Isso é tudo que você precisa saber de props por hora, mas assim que você terminar de ler essa página e se sentir confortável com o conteúdo, recomendamos retornar mais tarde para ler o guia completo em [Props](components-props.html).

## Um Elemento Raiz Único

Quando construindo um componente `<blog-post>`, seu template eventualmente conterá mais que apenas o título:

```html
<h3>{{ title }}</h3>
```

No mínimo, você vai querer incluir o conteúdo da postagem:

```html
<h3>{{ title }}</h3>
<div v-html="content"></div>
```

No entando, se você tentar isso no seu template, Vue mostrará um erro, explicando que **todo componente deve ter um único elemento raiz**. Você pode corrigir esse problema por envolver todo o template em um elemento pai, como abaixo:

```html
<div class="blog-post">
  <h3>{{ title }}</h3>
  <div v-html="content"></div>
</div>
```

À medida que nosso componente cresce, é provável que nós iremos mostrar mais que apenas o título e o conteúdo da postagem, como também a data de publicação, comentários e mais. Definir um prop para cada pedaço de informação pode se tornar bem irritante:

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:title="post.title"
  v-bind:content="post.content"
  v-bind:publishedAt="post.publishedAt"
  v-bind:comments="post.comments"
></blog-post>
```

Então agora é uma boa hora para reestruturar o componente `<blog-post>` para aceitar um único prop `post`:

```html
<blog-post
  v-for="post in posts"
  v-bind:key="post.id"
  v-bind:post="post"
></blog-post>
```

```js
Vue.component('blog-post', {
  props: ['post'],
  template: `
    <div class="blog-post">
      <h3>{{ post.title }}</h3>
      <div v-html="post.content"></div>
    </div>
  `
})
```

<p class="tip">O exemplo acima (e alguns outros futuramente) usa [Template strings](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/template_strings) para fazer templates de mais de uma linha mais legíveis. No entanto, isso não é suportado pelo Internet Explorer (IE), se você precisa de suporte para IE e não está usando uma ferramenta como Babel ou TypeScript, você ainda pode fazer da forma [pré-ES6](https://css-tricks.com/snippets/javascript/multiline-string-variables-in-javascript/).</p>

Agora, toda vez que uma nova propriedade é adicionado ao objeto `post`, ela será automaticamente acessível dentro do componente `<blog-post>`.

## Enviando Mensagens ao Pai com Eventos

À medida que desenvolvemos nosso componente `<blog-post>`, alguns recursos podem precisar comunicar de volta para o componente pai. Por exemplo, talvez decidamos incluir uma funcionalidade de acessibilidade que aumenta o tamanho da fonte das nossas postagens, enquanto deixa o resto da página no tamanho padrão:

No componente pai, nós podemos suportar essa funcionalidade por adicionar a propriedade `postFontSize` para data:

```js
new Vue({
  el: '#blog-posts-events-demo',
  data: {
    posts: [/* ... */],
    postFontSize: 1
  }
})
```

Que pode ser usada no template para controlar o tamanho de todas postagens:

```html
<div id="blog-posts-events-demo">
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post
      v-for="post in posts"
      v-bind:key="post.id"
      v-bind:post="post"
    ></blog-post>
  </div>
</div>
```

Agora vamos adicionar um botão para aumentar o tamanho do texto logo à direita de cada postagem:

```js
Vue.component('blog-post', {
  props: ['post'],
  template: `
    <div class="blog-post">
      <h3>{{ post.title }}</h3>
      <button>
        Aumentar fonte
      </button>
      <div v-html="post.content"></div>
    </div>
  `
})
```

O problema é que, esse botão não faz nada:

```html
<button>
  Aumentar fonte
</button>
```

Quando clicamos no botão, precisamos comunicar o componente pai que ele deve aumentar o tamanho da fonte de todas postagens. Felizmente, instâncias Vue provêm um evento para resolver esse problema. Para emitir um evento para o componente pai, nós chamamos o método [**`$emit`**](../api/#Instance-Methods-Events), passando o nome do evento:

```html
<button v-on:click="$emit('enlarge-text')">
  Aumentar fonte
</button>
```

Em nossa postagem, podemos ouvir esse evento com `v-on`, como faríamos com um evento DOM nativo:

```html
<blog-post
  ...
  v-on:enlarge-text="postFontSize += 0.1"
></blog-post>
```

{% raw %}
<div id="blog-posts-events-demo" class="demo">
  <div :style="{ fontSize: postFontSize + 'em' }">
    <blog-post
      v-for="post in posts"
      v-bind:key="post.id"
      v-bind:post="post"
      v-on:enlarge-text="postFontSize += 0.1"
    ></blog-post>
  </div>
</div>
<script>
Vue.component('blog-post', {
  props: ['post'],
  template: '\
    <div class="blog-post">\
      <h3>{{ post.title }}</h3>\
      <button v-on:click="$emit(\'enlarge-text\')">\
        Aumentar fonte\
      </button>\
      <div v-html="post.content"></div>\
    </div>\
  '
})
new Vue({
  el: '#blog-posts-events-demo',
  data: {
    posts: [
      { id: 1, title: 'Minha jornada com Vue', content: '...content...' },
      { id: 2, title: 'Postagens com Vue', content: '...content...' },
      { id: 3, title: 'Porque Vue é tão divertido', content: '...content...' }
    ],
    postFontSize: 1
  }
})
</script>
{% endraw %}

### Emitindo um Valor com um Evento

Às vezes é útil emitir um valor específico com um evento. Por exemplo, nós talvez queiramos que o componente `<blog-post>` seja responsável de quando em quanto aumentar a fonte. Nesses casos, podemos usar o 2° parâmetro do método `$emit` para prover esse valor:

```html
<button v-on:click="$emit('enlarge-text', 0.1)">
  Aumentar fonte
</button>
```

Quando ouvirmos por esse evento no componente pai, podemos acessar o valor emitido com `$event`:

```html
<blog-post
  ...
  v-on:enlarge-text="postFontSize += $event"
></blog-post>
```

Ou, se o manipulador de eventos é um método:

```html
<blog-post
  ...
  v-on:enlarge-text="onEnlargeText"
></blog-post>
```

Então o valor será passado como o primeiro parâmetro desse método:

```js
methods: {
  onEnlargeText: function (enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
```

### Usando `v-model` em Componentes

Eventos personalizados podem também ser usados para criar inputs personalizados que funcionam `v-model`. Lembre-se que:

```html
<input v-model="searchText">
```

tem a mesma funcionalidade que:

```html
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
>
```

No entanto, quando usado em um componente, `v-model` fará isso:

``` html
<custom-input
  v-bind:value="searchText"
  v-on:input="searchText = $event"
></custom-input>
```

Para isso realmente funcionar, o `<input>` dentro do componente precisa:

- Vincular o atributo `value` com o prop `value`
- No `input`, emitir seu próprio evento `input` personalizado com o novo valor

Aqui isso em ação:

```js
Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `
})
```

Agora `v-model` deve funcionar perfeitamente com esse componente:

```html
<custom-input v-model="searchText"></custom-input>
```

Isso é tudo que você precisa saber sobre eventos personalizados de componentes por hora, mas assim que você terminar de ler essa página e se sentir confortável com o conteúdo, recomendamos retornar mais tarde para ler o guia completo em [Custom Events](components-custom-events.html).

## Distribuição de Conteúdo com Slots

Assim como em elementos HTML, muitas vezes é útil ser capaz de passar conteúdo para um componente, dessa forma:

``` html
<alert-box>
  Algo ruim aconteceu.
</alert-box>
```

Que renderizaria algo assim:

{% raw %}
<div id="slots-demo" class="demo">
  <alert-box>
    Algo ruim aconteceu.
  </alert-box>
</div>
<script>
Vue.component('alert-box', {
  template: '\
    <div class="demo-alert-box">\
      <strong>Erro!</strong>\
      <slot></slot>\
    </div>\
  '
})
new Vue({ el: '#slots-demo' })
</script>
<style>
.demo-alert-box {
  padding: 10px 20px;
  background: #f3beb8;
  border: 1px solid #f09898;
}
</style>
{% endraw %}

Felizmente, isso é facilmente realizado com um elemento personalizado `<slot>`:

```js
Vue.component('alert-box', {
  template: `
    <div class="demo-alert-box">
      <strong>Erro!</strong>
      <slot></slot>
    </div>
  `
})
```

Como você vê acima, nós só adicionamos o slot para aonde queremos que o conteúdo vá -- e é isso!

Isso é tudo que você precisa saber sobre slots por hora, mas assim que você terminar de ler essa página e se sentir confortável com o conteúdo, recomendamos retornar mais tarde para ler o guia completo em [Slots](components-slots.html).

## Componentes Dinâmicos

Às vezes, é útil alternar dinamicamente entre componentes, como em uma interface de abas:

{% raw %}
<div id="dynamic-component-demo" class="demo">
  <button
    v-for="tab in tabs"
    v-bind:key="tab"
    class="dynamic-component-demo-tab-button"
    v-bind:class="{ 'dynamic-component-demo-tab-button-active': tab === currentTab }"
    v-on:click="currentTab = tab"
  >
    {{ tab }}
  </button>
  <component
    v-bind:is="currentTabComponent"
    class="dynamic-component-demo-tab"
  ></component>
</div>
<script>
Vue.component('tab-home', { template: '<div>Componente Home</div>' })
Vue.component('tab-postagens', { template: '<div>Componente Postagens</div>' })
Vue.component('tab-arquivado', { template: '<div>Componente Arquivado</div>' })
new Vue({
  el: '#dynamic-component-demo',
  data: {
    currentTab: 'Home',
    tabs: ['Home', 'Postagens', 'Arquivado']
  },
  computed: {
    currentTabComponent: function () {
      return 'tab-' + this.currentTab.toLowerCase()
    }
  }
})
</script>
<style>
.dynamic-component-demo-tab-button {
  padding: 6px 10px;
  border-top-left-radius: 3px;
  border-top-right-radius: 3px;
  border: 1px solid #ccc;
  cursor: pointer;
  background: #f0f0f0;
  margin-bottom: -1px;
  margin-right: -1px;
}
.dynamic-component-demo-tab-button:hover {
  background: #e0e0e0;
}
.dynamic-component-demo-tab-button-active {
  background: #e0e0e0;
}
.dynamic-component-demo-tab {
  border: 1px solid #ccc;
  padding: 10px;
}
</style>
{% endraw %}

O exemplo acima é possível por causa do elemento `<component>` com o atributo especial `is`:

```html
<!-- Component changes when currentTabComponent changes -->
<component v-bind:is="currentTabComponent"></component>
```

No exemplo acima, `currentTabComponent` pode conter:

- o nome do componente registrado, ou
- o objeto de opções de um componente

Veja [esse exemplo](https://jsfiddle.net/chrisvfritz/o3nycadu/) para experimentar com todo o código, ou [essa versão](https://jsfiddle.net/chrisvfritz/b2qj69o1/) para um exemplo ligando ao objeto de opções de um componente em vez de seu nome registrado.

Isso é tudo que você precisa saber sobre componentes dinâmicos por hora mas, assim que você terminar de ler essa página e se sentir confortável com o conteúdo, recomendamos retornar mais tarde para ler o guia completo sobre componentes [Dinâmicos & Assíncronos](components-dynamic-async.html).

## Ressalvas na Análise do Template DOM

Alguns elementos HTML, como `<ul>`, `<ol>`, `<table>` e `<select>` têm restrições do que pode aparecer dentro deles, e alguns elementos como `<li>`, `<tr>`, e `<option>` podem aparecer apenas dentro de certos elementos.

Isso nos leva a problemas quando usamos componentes com element que tem tais restrições. Por exemplo:

``` html
<table>
  <blog-post-row></blog-post-row>
</table>
```

O componente `<blog-post-row>` será renderizado antes do elemento `<table>` por ser um conteúdo inválido, causando erros na renderização. Felizmente, o atributo especial `is` oferece uma solução alternativa:

``` html
<table>
  <tr is="blog-post-row"></tr>
</table>
```

Deve ser notado que **essa limitação _não_ se aplica se você está usando string templates de uma das seguintes fontes**:

- _Template Strings_ (Ex: `template: '...'`)
- [Componentes Single-File (`.vue`)](single-file-components.html)
- [`<script type="text/x-template">`](components-edge-cases.html#X-Templates)

Isso é tudo que você precisa saber sobre componentes dinâmicos por hora -- na verdade, esse é o fim da seção _Essenciais_ do Vue. Parabéns! Ainda há mais a ser aprendido, mas antes, recomendamos que tire um tempo para brincar com o Vue você mesmo e criar alguma coisa divertida.

Assim que você se sentir confortável com o conteúdo que vimos, recomendamos retornar mais tarde para ler o guia completo de componentes [Dinâmicos & Assíncronos](components-dynamic-async.html), como também as outras páginas na seção _Componentes em Detalhes_ da barra lateral.
