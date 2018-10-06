---
title: Lidando com Casos Extremos
type: guide
order: 106
---

<p class="tip">**Nota da Equipe de Tradução**
Este arquivo ainda não foi traduzido! Leia a versão original em inglês a seguir e, se puder, colabore com sua tradução: acesse [nosso projeto no GitHub](https://github.com/vuejs-br/br.vuejs.org/issues), avise que irá contribuir e inicie a tradução. Sua participação é muito importante!</p>

> Esta página assume que você já leu o [Básico sobre Componentes](components.html). Leia lá primeiro se você for novo com componentes.

<p class="tip">All the features on this page document the handling of edge cases, meaning unusual situations that sometimes require bending Vue's rules a little. Note however, that they all have disadvantages or situations where they could be dangerous. These are noted in each case, so keep them in mind when deciding to use each feature.</p>

## Element & Component Access

In most cases, it's best to avoid reaching into other component instances or manually manipulating DOM elements. There are cases, however, when it can be appropriate.

### Accessing the Root Instance

In every subcomponent of a `new Vue` instance, this root instance can be accessed with the `$root` property. For example, in this root instance:

```js
// The root Vue instance
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

All subcomponents will now be able to access this instance and use it as a global store:

```js
// Get root data
this.$root.foo

// Set root data
this.$root.foo = 2

// Access root computed properties
this.$root.bar

// Call root methods
this.$root.baz()
```

<p class="tip">This can be convenient for demos or very small apps with a handful of components. However, the pattern does not scale well to medium or large-scale applications, so we strongly recommend using <a href="https://github.com/vuejs/vuex">Vuex</a> to manage state in most cases.</p>

### Accessing the Parent Component Instance

Similar to `$root`, the `$parent` property can be used to access the parent instance from a child. This can be tempting to reach for as a lazy alternative to passing data with a prop.

<p class="tip">In most cases, reaching into the parent makes your application more difficult to debug and understand, especially if you mutate data in the parent. When looking at that component later, it will be very difficult to figure out where that mutation came from.</p>

There are cases however, particularly shared component libraries, when this _might_ be appropriate. For example, in abstract components that interact with JavaScript APIs instead of rendering HTML, like these hypothetical Google Maps components:

```html
<google-map>
  <google-map-markers v-bind:places="iceCreamShops"></google-map-markers>
</google-map>
```

The `<google-map>` component might define a `map` property that all subcomponents need access to. In this case `<google-map-markers>` might want to access that map with something like `this.$parent.getMap`, in order to add a set of markers to it. You can see this pattern [in action here](https://jsfiddle.net/chrisvfritz/ttzutdxh/).

Keep in mind, however, that components built with this pattern are still inherently fragile. For example, imagine we add a new `<google-map-region>` component and when `<google-map-markers>` appears within that, it should only render markers that fall within that region:

```html
<google-map>
  <google-map-region v-bind:shape="cityBoundaries">
    <google-map-markers v-bind:places="iceCreamShops"></google-map-markers>
  </google-map-region>
</google-map>
```

Then inside `<google-map-markers>` you might find yourself reaching for a hack like this:

```js
var map = this.$parent.map || this.$parent.$parent.map
```

This has quickly gotten out of hand. That's why to provide context information to descendent components arbitrarily deep, we instead recommend [dependency injection](#Dependency-Injection).

### Accessing Child Component Instances & Child Elements

Despite the existence of props and events, sometimes you might still need to directly access a child component in JavaScript. To achieve this you can assign a reference ID to the child component using the `ref` attribute. For example:

```html
<base-input ref="usernameInput"></base-input>
```

Now in the component where you've defined this `ref`, you can use:

```js
this.$refs.usernameInput
```

to access the `<base-input>` instance. This may be useful when you want to, for example, programmatically focus this input from a parent. In that case, the `<base-input>` component may similarly use a `ref` to provide access to specific elements inside it, such as:

```html
<input ref="input">
```

And even define methods for use by the parent:

```js
methods: {
  // Used to focus the input from the parent
  focus: function () {
    this.$refs.input.focus()
  }
}
```

Thus allowing the parent component to focus the input inside `<base-input>` with:

```js
this.$refs.usernameInput.focus()
```

When `ref` is used together with `v-for`, the ref you get will be an array containing the child components mirroring the data source.

<p class="tip"><code>$refs</code> are only populated after the component has been rendered, and they are not reactive. It is only meant as an escape hatch for direct child manipulation - you should avoid accessing <code>$refs</code> from within templates or computed properties.</p>

### Dependency Injection

Earlier, when we described [Accessing the Parent Component Instance](#Accessing-the-Parent-Component-Instance), we showed an example like this:

```html
<google-map>
  <google-map-region v-bind:shape="cityBoundaries">
    <google-map-markers v-bind:places="iceCreamShops"></google-map-markers>
  </google-map-region>
</google-map>
```

In this component, all descendants of `<google-map>` needed access to a `getMap` method, in order to know which map to interact with. Unfortunately, using the `$parent` property didn't scale well to more deeply nested components. That's where dependency injection can be useful, using two new instance options: `provide` and `inject`.

The `provide` options allows us to specify the data/methods we want to **provide** to descendent components. In this case, that's the `getMap` method inside `<google-map>`:

```js
provide: function () {
  return {
    getMap: this.getMap
  }
}
```

Then in any descendants, we can use the `inject` option to receive specific properties we'd like to add to that instance:

```js
inject: ['getMap']
```

You can see the [full example here](https://jsfiddle.net/chrisvfritz/tdv8dt3s/). The advantage over using `$parent` is that we can access `getMap` in _any_ descendant component, without exposing the entire instance of `<google-map>`. This allows us to more safely keep developing that component, without fear that we might change/remove something that a child component is relying on. The interface between these components remains clearly defined, just as with `props`.

In fact, you can think of dependency injection as sort of "long-range props", except:

* ancestor components don't need to know which descendants use the properties it provides
* descendant components don't need to know where injected properties are coming from

<p class="tip">However, there are downsides to dependency injection. It couples components in your application to the way they're currently organized, making refactoring more difficult. Provided properties are also not reactive. This is by design, because using them to create a central data store scales just as poorly as <a href="#Accessing-the-Root-Instance">using <code>$root</code></a> for the same purpose. If the properties you want to share are specific to your app, rather than generic, or if you ever want to update provided data inside ancestors, then that's a good sign that you probably need a real state management solution like <a href="https://github.com/vuejs/vuex">Vuex</a> instead.</p>

Learn more about dependency injection in [the API doc](https://vuejs.org/v2/api/#provide-inject).

## Escuta de eventos programática

Até então, você viu usos do `$emit` e escutou eventos com o `v-on`, porém instâncias Vue também oferecem outros métodos em sua interface de eventos. Nós podemos:

- Escutar um evento com `$on(eventName, eventHandler)`
- Escutar um evento, uma única vez, com `$once(eventName, eventHandler)`
- Deixar de escutar um evento com `$off(eventName, eventHandler)`

Normalmente, você não terá que usar nenhum desses métodos, porém eles estão disponíveis para casos em que você precise escutar, manualmente, eventos na instância de um componente. Eles também podem ser úteis como uma ferramenta para organização de código. Por exemplo, você pode observar, frequentemente, esse padrão para integração com uma biblioteca de terceiros:

```js
// Anexe o datepicker ao input uma única vez
// ele está montado no DOM.
mounted: function () {
  // Pikaday é uma biblioteca datepicker de terceiros
  this.picker = new Pikaday({
    field: this.$refs.input,
    format: 'YYYY-MM-DD'
  })
},
// Imediatamente antes do componente ser destruído,
// também destrua o datepicker.
beforeDestroy: function () {
  this.picker.destroy()
}
```

Essa abordagem traz dois problemas em potencial:

- Demanda que o `picker` seja salvo na instância do componente, enquanto é possível que somente os gatilhos de ciclos de vida precisem acessá-lo. Não se trata de uma abordagem terrível, mas pode ser considerada confusa.
- Nosso código de configuração ou _setup_ é mantido separado do nosso código de limpeza, fazendo com que seja mais difícil de, programaticamente, limpar qualquer coisa que tenhamos configurado.

Pode-se resolver ambos os problemas com uma escuta de eventos programática:

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

Usando essa estratégia, nós podemos até mesmo usar o Pickaday com vários elementos _input_, com cada nova instância sendo automaticamente limpa depois de si.

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

Veja [este _fiddle_](https://jsfiddle.net/chrisvfritz/1Leb7up8/) com o código completo. Note, entretanto, que caso esteja tendo que fazer muito _setup_ e limpeza em um único componente, a melhor solução será, usualmente, criar componentes mais modulares. Neste caso, recomendaríamos criar um componente `<input-datepicker>` reutilizável.

Para aprender mais sobre _listeners_ programáticos, dê uma conferida na API de [Métodos de Instância de Eventos](https://vuejs.org/v2/api/#Instance-Methods-Events).

<p class="tip">Note que o sistema de eventos do Vue é diferente do utilizado pelo navegador <a href="https://developer.mozilla.org/en-US/docs/Web/API/EventTarget">EventTarget API</a>. Embora eles funcionem de modo semelhante, <code>$emit</code>, <code>$on</code>, e <code>$off</code>  <strong>não</strong> são aliases para <code>dispatchEvent</code>, <code>addEventListener</code>, e <code>removeEventListener</code>.</p>

## Referências Circulares 

### Componentes Recursivos

Componentes podem invocar a si mesmos recursivamente em seu próprio `template`. Todavia, eles só podem fazer isso com a opção `name`:

``` js
name: 'unique-name-of-my-component'
```

Quando se registra um componente globalmente usando `Vue.component`, o ID global é automaticamente configurado como a opção `name`.

``` js
Vue.component('unique-name-of-my-component', {
  // ...
})
```

Se você não for cuidadoso, componentes recursivos também podem levar a _loops_ infinitos:

``` js
name: 'stack-overflow',
template: '<div><stack-overflow></stack-overflow></div>'
```

Um componente como o mostrado acima resultará e um erro _"max stack size exceeded"_, então garanta que a invocação recursiva é condicional (ou seja, use um `v-if` que virá a ser, eventualmente, `false`).

### Referências Circulares Entre Componentes

Digamos que você está construindo uma árvore de diretórios de arquivos, como em um Buscador ou Explorador de Arquivos. Você pode ter um componente `tree-folder` com este _template_:

``` html
<p>
  <span>{{ folder.name }}</span>
  <tree-folder-contents :children="folder.children"/>
</p>
```

Então, um componente `tree-folder-contents`, com este _template_:

``` html
<ul>
  <li v-for="child in children">
    <tree-folder v-if="child.children" :folder="child"/>
    <span v-else>{{ child.name }}</span>
  </li>
</ul>
```

Quando você olha mais de perto, verá que tais componentes irão, na verdade, ser descendente e ancestral um do outro na árvore de renderização - um paradóxo! Quando for registrar esses componentes globalmente com o `Vue.component`, tal paradóxo será resolvido para você automaticamente. Se este é o seu caso, você pode parar sua leitura aqui.

Todavia, se você está `requiring/importing` componentes usando um __sistema de módulos__, e.g. via Webpack ou Browserify, você irá se deparar com um erro: 

```
Failed to mount component: template or render function not defined.
```

Para explicar o que está acontecendo, vamos chamar nossos componentes de A e B. O sistema de módulos vê que ele precisa de A, mas primeiro precisa de B, mas B precisa de A, mas A precisa de B, etc. Ele fica preso no _loop_, sem saber como resolver nenhum componente, sem antes resolver o outro. Para que isso seja consertado, precisamos dar ao sistema de módulos um ponto no qual ele possa dizer, "A precisa de B _eventualmente_, mas não há necessidade de resolver B primeiro."

No nosso caso, vamos construir esse ponto no componente `tree-folder`. Nós sabemos que o componente filho que cria o paradóxo é o `tree-folder-contents`, então vamos esperar até que o gatilho de ciclo de vida `beforeCreate` registre-o:

``` js
beforeCreate: function () {
  this.$options.components.TreeFolderContents = require('./tree-folder-contents.vue').default
}
```

Ou, alternativamente, você poderia utilizar o `import` assíncrono do Webpack quando você registra seu componente localmente:

``` js
components: {
  TreeFolderContents: () => import('./tree-folder-contents.vue')
}
```

Problema resolvido!

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
