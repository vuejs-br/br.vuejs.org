---
title: Lidando com Casos Extremos
type: guide
order: 106
---

> Esta página assume que você já leu o [Básico sobre Componentes](components.html). Leia lá primeiro se você for novo com componentes.

<p class="tip">Todas os recursos desta página documentam o tratamento de casos extremos, ou seja, situações incomuns que algumas vezes requerem que as regras do Vue sejam um pouco contornadas. Entretanto, note que todas elas possuem desvantagens ou podem ser perigosas. Isso é observado em casa caso, portanto, lembre-se delas ao decidir usar cada recurso.</p>

## Acesso a Elementos e Componentes

Na maioria dos casos, é melhor evitar alcançar outras instâncias de componentes ou manualmente manipular elementos de DOM. Entretanto, há casos em que isso pode ser apropriado.

### Acessando a Instância Raiz

Em cada subcomponente de uma instância `new Vue`, a instância raiz pode ser acessada com a propriedade `$root`. Por exemplo, nessa instância raiz:

```js
// A instância Raiz do Vue
new Vue({
  data: {
    foo: 1
  },
  computed: {
    bar: function () { /* ... */ }
  },
  methods: {
    baz: function () { /* ... */ }
  }
})
```

Todos os subcomponentes agora são capazes de acessar esta instância e usá-la como *store* global:

```js
// Recupera dados da raiz
this.$root.foo

// Atribui dados na raiz
this.$root.foo = 2

// Acessa dados computados da raiz
this.$root.bar

// Chama métodos da raiz
this.$root.baz()
```

<p class="tip">Isso pode ser conveniente para demonstrações ou aplicações muito pequenas com um punhado de componentes. Porém, o padrão não se adapta muito bem a aplicações de média ou larga escala, então, nós recomendamos fortemente a utilização do <a href="https://github.com/vuejs/vuex">Vuex</a> para gerenciar o estado na maioria dos casos.</p>

### Acessando a Instância do Componente Pai

Semelhantemente a `$root`, a propriedade `$parent` pode ser utilizada para acessar a instância pai a partir de um filho. Isso é uma alternativa, preguiçosa e tentadora, a passar os dados via propriedades.

<p class="tip">Na maioria dos casos, alcançar o interior dos componentes pais torna sua aplicação muito difícil de depurar e entender, especiamente se você modifica dados no componente pai. Ao olhar para o componente posteriormente, será muito difícil de compreender onde aquela mutação se originou.</p>

Há casos, entretanto, particulamente em bibliotecas de componentes compartilhadas, que isso *pode* ser apropriado. Por exemplo, em componentes abstratos que interagem com com APIs JavaScript em vez de renderizarem HTML, assim como esse componente hipotético do Google Maps:

```html
<google-map>
  <google-map-markers v-bind:places="iceCreamShops"></google-map-markers>
</google-map>
```

O componente `<google-map>` pode definir uma propriedade `map` que todos os subcomponentes precisam acessar. Neste caso, o componente `<google-map-markers>` pode acessar aquele mapa através de algo como `this.$parent.getMap`, para poder adicionar alguns marcadores à ela. Você pode conferir esse padrão [em ação aqui](https://jsfiddle.net/chrisvfritz/ttzutdxh/).

Tenha em mente, no entanto, que componentes construidos com esse padrão são inerentemente frágeis. Por exemplo, imagine que nós adicionaremos um novo componente `<google-map-region>` e quando `<google-map-markers>` aparecer dentro dele, ele só deve renderizar marcadores que estiverem nessa região:
```html
<google-map>
  <google-map-region v-bind:shape="cityBoundaries">
    <google-map-markers v-bind:places="iceCreamShops"></google-map-markers>
  </google-map-region>
</google-map>
```

Então dentro de `<google-map-markers>` você poderá se encontrar buscando por um *hack* como esse:

```js
var map = this.$parent.map || this.$parent.$parent.map
```

Isso rapidamente ficou fora de controle. É por isso que para fornecer informação de contexto para os componentes descendentes arbitráriamente profundos, nós recomendamos a [injeção de dependência](#Injecao-de-Dependencia).

### Acessando Instâncias de Componentes Filhos & Elementos Filhos

Apesar da existência de propriedades e eventos, algumas vezes você ainda pode precisar acessar diretamente um componente filho em JavaScript. Para alcançar isso, você pode atribuir um ID de referência ao componente filho usando o atributo `ref`. Por exemplo:

```html
<base-input ref="usernameInput"></base-input>
```

Agora no componente onde você definiu o `ref`, você pode usar:

```js
this.$refs.usernameInput
```

para acessar a instância `<base-input>`. Isso pode ser útil quando você quer, por exemplo, "focar" esse *input* programaticamente à partir de um pai. Neste caso, o componente `<base-input>` pode, similarmente, usar o `ref` para prover acesso a elementos específicos dentro dele, como:

```html
<input ref="input">
```

E até mesmo definir métodos para serem utilizados pelo pai:

```js
methods: {
  // Usado pelo pai para focar o input
  focus: function () {
    this.$refs.input.focus()
  }
}
```

Permitindo assim que o componente pai foque o *input* dentro de `<base-input>` com:

```js
this.$refs.usernameInput.focus()
```

Quando o `ref` é usado junto com `v-for`, a referência que você obtém será um *array* contendo os componentes filhos espelhando a fonte de dados.

<p class="tip"><code>$refs</code> são populados somente depois que o componente foi renderizado, e não são reativos. É somente uma escotilha para manipulação direta dos filhos - você deve evitar usar <code>$refs</code> de dentro dos *templates* ou dos dados computados.</p>

### Injeção de Dependência

Anteriormente, quando descrevemos [Acessando a Instância do Componente Pai](#Acessando-a-Instancia-do-Componente-Pai), mostramos um exemplo como esse:

```html
<google-map>
  <google-map-region v-bind:shape="cityBoundaries">
    <google-map-markers v-bind:places="iceCreamShops"></google-map-markers>
  </google-map-region>
</google-map>
```

Neste componente, todos os descendentes de `<google-map>` necessitam do acesso ao método `getMap`, para saberem com qual mapa estão interagindo. Infelizmente, usando a propriedade `$parent` não escalaremos muito bem para muitos componentes profundamente aninhados. É aí que a injeção de dependência pode ser útil, usando duas novas opções da instância: `provide` e `inject`.

A opção `provide` nos permite especificar quais dados/métodos nós queremos **prover** para os componentes descendentes. Neste caso, é o método `getMap` dentro de `<google-map>`:

```js
provide: function () {
  return {
    getMap: this.getMap
  }
}
```

Então em qualquer descendente, nós podemos usar a opção `inject` para receber as propriedades especificas que desejamos adicionar à instância:

```js
inject: ['getMap']
```

Você pode ver o [exemplo completo](https://jsfiddle.net/chrisvfritz/tdv8dt3s/). A vantagem em usar `$parent` é que podemos acessar `getMap` em *qualquer* componente descendente, sem expor a instância inteira do `<google-map>`. Isso nos permite continuar o desenvolvimento desse componente com mais segurança, sem medo de que podemos alterar/remover algo que o componente filho esteja confiando. A interface entre esses componentes permanece claramente definida, assim como usando `props`.

Na verdade, você porde pensar a injeção de dependência como uma espécie de "propriedade de longo alcance", exceto por:

* componentes ancestrais não precisarem saber quais descendentes usam a propriedade que ele provê
* componentes descendentes não precisarem saber de onde as propriedades injetadas vieram

<p class="tip">Entretanto, existem desvantagens na injeção de dependência. Ela associa os componentes de sua aplicação da maneira que estão organizados atualmente, tornando a refatoração mais difícil. Propriedades providas pelos componentes pais também não são reativas. Isso ocorre por *design*, porque usá-las para criar um *store* de dados central fará sua aplicação escalar tão ruim quanto <a href="#Acessando-a-Instancia-Raiz"> usando <code>$root</code></a> para o mesmo propósito. Se as propriedades que você deseja compartilhar são especificas para sua aplicação, em vez de genéricas, ou se você deseja sempre atualizar os dados providos dentro do componentes ancestrais, então isso é um bom sinal que você provavelmente precisará de uma solução como <a href="https://github.com/vuejs/vuex">Vuex</a> para gerenciar o estado real.</p>

Aprenda mais sobre injeção de dependência na [documentação da API](https://vuejs.org/v2/api/#provide-inject).

## Programmatic Event Listeners

So far, you've seen uses of `$emit`, listened to with `v-on`, but Vue instances also offer other methods in its events interface. We can:

- Listen for an event with `$on(eventName, eventHandler)`
- Listen for an event only once with `$once(eventName, eventHandler)`
- Stop listening for an event with `$off(eventName, eventHandler)`

You normally won't have to use these, but they're available for cases when you need to manually listen for events on a component instance. They can also be useful as a code organization tool. For example, you may often see this pattern for integrating a 3rd-party library:

```js
// Attach the datepicker to an input once
// it's mounted to the DOM.
mounted: function () {
  // Pikaday is a 3rd-party datepicker library
  this.picker = new Pikaday({
    field: this.$refs.input,
    format: 'YYYY-MM-DD'
  })
},
// Right before the component is destroyed,
// also destroy the datepicker.
beforeDestroy: function () {
  this.picker.destroy()
}
```

This has two potential issues:

- It requires saving the `picker` to the component instance, when it's possible that only lifecycle hooks need access to it. This isn't terrible, but it could be considered clutter.
- Our setup code is kept separate from our cleanup code, making it more difficult to programmatically clean up anything we set up.

You could resolve both issues with a programmatic listener:

```js
mounted: function () {
  var picker = new Pikaday({
    field: this.$refs.input,
    format: 'YYYY-MM-DD'
  })

  this.$once('hook:beforeDestroy', function () {
    picker.destroy()
  })
}
```

Using this strategy, we could even use Pikaday with several input elements, with each new instance automatically cleaning up after itself:

```js
mounted: function () {
  this.attachDatepicker('startDateInput')
  this.attachDatepicker('endDateInput')
},
methods: {
  attachDatepicker: function (refName) {
    var picker = new Pikaday({
      field: this.$refs[refName],
      format: 'YYYY-MM-DD'
    })

    this.$once('hook:beforeDestroy', function () {
      picker.destroy()
    })
  }
}
```

See [this fiddle](https://jsfiddle.net/chrisvfritz/1Leb7up8/) for the full code. Note, however, that if you find yourself having to do a lot of setup and cleanup within a single component, the best solution will usually be to create more modular components. In this case, we'd recommend creating a reusable `<input-datepicker>` component.

To learn more about programmatic listeners, check out the API for [Events Instance Methods](https://vuejs.org/v2/api/#Instance-Methods-Events).

<p class="tip">Note that Vue's event system is different from the browser's <a href="https://developer.mozilla.org/en-US/docs/Web/API/EventTarget">EventTarget API</a>. Though they work similarly, <code>$emit</code>, <code>$on</code>, and <code>$off</code> are <strong>not</strong> aliases for <code>dispatchEvent</code>, <code>addEventListener</code>, and <code>removeEventListener</code>.</p>

## Circular References

### Recursive Components

Components can recursively invoke themselves in their own template. However, they can only do so with the `name` option:

``` js
name: 'unique-name-of-my-component'
```

When you register a component globally using `Vue.component`, the global ID is automatically set as the component's `name` option.

``` js
Vue.component('unique-name-of-my-component', {
  // ...
})
```

If you're not careful, recursive components can also lead to infinite loops:

``` js
name: 'stack-overflow',
template: '<div><stack-overflow></stack-overflow></div>'
```

A component like the above will result in a "max stack size exceeded" error, so make sure recursive invocation is conditional (i.e. uses a `v-if` that will eventually be `false`).

### Circular References Between Components

Let's say you're building a file directory tree, like in Finder or File Explorer. You might have a `tree-folder` component with this template:

``` html
<p>
  <span>{{ folder.name }}</span>
  <tree-folder-contents :children="folder.children"/>
</p>
```

Then a `tree-folder-contents` component with this template:

``` html
<ul>
  <li v-for="child in children">
    <tree-folder v-if="child.children" :folder="child"/>
    <span v-else>{{ child.name }}</span>
  </li>
</ul>
```

When you look closely, you'll see that these components will actually be each other's descendent _and_ ancestor in the render tree - a paradox! When registering components globally with `Vue.component`, this paradox is resolved for you automatically. If that's you, you can stop reading here.

However, if you're requiring/importing components using a __module system__, e.g. via Webpack or Browserify, you'll get an error:

```
Failed to mount component: template or render function not defined.
```

To explain what's happening, let's call our components A and B. The module system sees that it needs A, but first A needs B, but B needs A, but A needs B, etc. It's stuck in a loop, not knowing how to fully resolve either component without first resolving the other. To fix this, we need to give the module system a point at which it can say, "A needs B _eventually_, but there's no need to resolve B first."

In our case, let's make that point the `tree-folder` component. We know the child that creates the paradox is the `tree-folder-contents` component, so we'll wait until the `beforeCreate` lifecycle hook to register it:

``` js
beforeCreate: function () {
  this.$options.components.TreeFolderContents = require('./tree-folder-contents.vue').default
}
```

Or alternatively, you could use Webpack's asynchronous `import` when you register the component locally:

``` js
components: {
  TreeFolderContents: () => import('./tree-folder-contents.vue')
}
```

Problem solved!

## Alternate Template Definitions

### Inline Templates

When the `inline-template` special attribute is present on a child component, the component will use its inner content as its template, rather than treating it as distributed content. This allows more flexible template-authoring.

``` html
<my-component inline-template>
  <div>
    <p>These are compiled as the component's own template.</p>
    <p>Not parent's transclusion content.</p>
  </div>
</my-component>
```

<p class="tip">However, <code>inline-template</code> makes the scope of your templates harder to reason about. As a best practice, prefer defining templates inside the component using the <code>template</code> option or in a <code>&lt;template&gt;</code> element in a <code>.vue</code> file.</p>

### X-Templates

Another way to define templates is inside of a script element with the type `text/x-template`, then referencing the template by an id. For example:

``` html
<script type="text/x-template" id="hello-world-template">
  <p>Hello hello hello</p>
</script>
```

``` js
Vue.component('hello-world', {
  template: '#hello-world-template'
})
```

<p class="tip">These can be useful for demos with large templates or in extremely small applications, but should otherwise be avoided, because they separate templates from the rest of the component definition.</p>

## Controlling Updates

Thanks to Vue's Reactivity system, it always knows when to update (if you use it correctly). There are edge cases, however, when you might want to force an update, despite the fact that no reactive data has changed. Then there are other cases when you might want to prevent unnecessary updates.

### Forcing an Update

<p class="tip">If you find yourself needing to force an update in Vue, in 99.99% of cases, you've made a mistake somewhere.</p>

You may not have accounted for change detection caveats [with arrays](https://vuejs.org/v2/guide/list.html#Caveats) or [objects](https://vuejs.org/v2/guide/list.html#Object-Change-Detection-Caveats), or you may be relying on state that isn't tracked by Vue's reactivity system, e.g. with `data`.

However, if you've ruled out the above and find yourself in this extremely rare situation of having to manually force an update, you can do so with [`$forceUpdate`](../api/#vm-forceUpdate).

### Cheap Static Components with `v-once`

Rendering plain HTML elements is very fast in Vue, but sometimes you might have a component that contains **a lot** of static content. In these cases, you can ensure that it's only evaluated once and then cached by adding the `v-once` directive to the root element, like this:

``` js
Vue.component('terms-of-service', {
  template: `
    <div v-once>
      <h1>Terms of Service</h1>
      ... a lot of static content ...
    </div>
  `
})
```

<p class="tip">Once again, try not to overuse this pattern. While convenient in those rare cases when you have to render a lot of static content, it's simply not necessary unless you actually notice slow rendering -- plus, it could cause a lot of confusion later. For example, imagine another developer who's not familiar with <code>v-once</code> or simply misses it in the template. They might spend hours trying to figure out why the template isn't updating correctly.</p>
