---
title: Plugins
type: guide
order: 304
---

_Plugins_ geralmente acrescentam funcionalidade ao Vue em nível global. Não há um escopo estrito definido para um _plugin_ - existem vários tipos que você pode escrever:

1. Adicionar alguns métodos e propriedades globais. Ex.: [vue-custom-element](https://github.com/karol-f/vue-custom-element)

2. Adicionar um ou mais recursos globais: diretivas/filtros/transições etc. Ex.: [vue-touch](https://github.com/vuejs/vue-touch)

3. Adicionar algumas opções de componente via _mixin_ global. Ex.: [vue-router](https://github.com/vuejs/vue-router)

4. Adicionar métodos para instâncias Vue incluindo-os em `Vue.prototype`.

5. Uma biblioteca com uma API própria, que ao mesmo tempo injeta alguma combinação dos anteriores. Ex.: [vue-router](https://github.com/vuejs/vue-router)

## Usando um Plugin

Use _plugins_ chamando o método global `Vue.use()`. Isto precisa ser feito antes que você inicie sua aplicação através de `new Vue()`:

``` js
// chama `MyPlugin.install(Vue)`
Vue.use(MyPlugin)

new Vue({
  //... opções
})
```

Você pode, opcionalmente, passar algumas opções ao _plugin_:

``` js
Vue.use(MyPlugin, { someOption: true })
```

`Vue.use` automaticamente lhe previne de usar o mesmo _plugin_ mais de uma vez, portanto, chamá-lo múltiplas vezes no mesmo _plugin_ irá instalá-lo apenas uma vez.

Alguns _plugins_ oferecidos pela equipe do Vue de forma oficial, como o `vue-router`, automaticamente chamam `Vue.use()` se `Vue` estiver disponível como uma variável global. Entretanto, em um ambiente de módulos como CommonJS, você sempre precisará chamar `Vue.use()` explicitamente:

``` js
// Quando usar CommonJS via Browserify ou Webpack
var Vue = require('vue')
var VueRouter = require('vue-router')

// Não esqueça de chamar isto
Vue.use(VueRouter)
```

Dê uma olhada na lista [awesome-vue](https://github.com/vuejs/awesome-vue#components--libraries) para uma enorme coleção de _plugins_ e bibliotecas criadas a partir de contribuições da comunidade.

## Escrevendo um Plugin

Um _plugin_ do Vue deve expor um método `install`. Esse método será chamado tendo o construtor `Vue` como primeiro argumento, junto com possíveis `options`:

``` js
MyPlugin.install = function (Vue, options) {
  // 1. adicionar método ou propriedade global
  Vue.myGlobalMethod = function () {
<<<<<<< HEAD
    // alguma lógica ...
=======
    // some logic ...
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
  }

  // 2. adicionar um recurso global
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
<<<<<<< HEAD
      // alguma lógica ...
=======
      // some logic ...
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
    }
    ...
  })

  // 3. adicionar algumas opções de componente
  Vue.mixin({
    created: function () {
<<<<<<< HEAD
      // alguma lógica ...
=======
      // some logic ...
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
    }
    ...
  })

  // 4. adicionar um método de instância
  Vue.prototype.$myMethod = function (methodOptions) {
<<<<<<< HEAD
    // algo lógico ...
=======
    // some logic ...
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
  }
}
```
