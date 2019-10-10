---
title: Mixins
type: guide
order: 301
---

## Introdução

Mixins são uma forma flexível de distribuir funcionalidade reutilizável em diversos componentes Vue. Um objeto _mixin_ pode conter quaisquer opções de componente. Quando um componente utiliza um _mixin_, todas as opções deste serão misturadas (em inglês, _mixed in_) com as opções do próprio componente.

<div class="vue-mastery"><a href="https://www.vuemastery.com/courses/next-level-vue/mixins" target="_blank" rel="noopener" title="Tutorial sobre Mixins com Vue.js">Assista a uma lição gratuita em vídeo na Vue Mastery</a></div>

Exemplo:

``` js
// define um objeto mixin
var myMixin = {
  created: function () {
    this.hello()
  },
  methods: {
    hello: function () {
      console.log('Bem-vindo ao mixin!')
    }
  }
}

// define um componente que usa esse mixin
var Component = Vue.extend({
  mixins: [myMixin]
})

var component = new Component() // => "Bem-vindo ao mixin!"
```

## Mesclagem de Opções

Quando um _mixin_ e o próprio componente contêm opções que se sobrepõem, elas serão mescladas usando estratégias apropriadas.

Por exemplo, objetos de dados passam por uma mesclagem recursiva, com os dados do componente tomando prioridade em caso de conflitos. 

``` js
var mixin = {
  data: function () {
    return {
      message: 'olá',
      foo: 'abc'
    }
  }
}

new Vue({
  mixins: [mixin],
  data: function () {
    return {
      message: 'adeus',
      bar: 'def'
    }
  },
  created: function () {
    console.log(this.$data)
    // => { message: "adeus", foo: "abc", bar: "def" }
  }
})
```

Funções de gatilho com o mesmo nome são mescladas em um Array, de forma que todas possam ser chamadas. Gatilhos do _mixin_ serão chamados **antes** dos gatilhos do próprios componente.

``` js
var mixin = {
  created: function () {
    console.log('gatilho do mixin')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('gatilho do componente')
  }
})

// => "gatilho do mixin"
// => "gatilho do componente"
```

Opções que aceitam objetos, como `methods`, `components` e `directives`, serão mescladas em um único objeto. As opções do próprio componente terão prioridade, caso ocorra algum conflito nas chaves desses objetos:

``` js
var mixin = {
  methods: {
    foo: function () {
      console.log('algo')
    },
    conflicting: function () {
      console.log('escrito pelo mixin')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('outra coisa')
    },
    conflicting: function () {
      console.log('escrito pelo componente')
    }
  }
})

vm.foo() // => "algo"
vm.bar() // => "outra coisa"
vm.conflicting() // => "escrito pelo componente"
```

Observe que são as mesmas estratégias utilizadas em `Vue.extend()`.

## Registrando Mixin Global

Você também pode aplicar um _mixin_ globalmente. Use com cautela! Assim que aplicar um _mixin_ globalmente, isto irá afetar **todas** as instâncias Vue criadas depois. Quando utilizado apropriadamente, permite injetar lógica de processamento para opções personalizadas:

``` js
// injeta um manipulador para a opção personalizada `myOption`
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

new Vue({
  myOption: 'beleza!'
})

// => "beleza!"
```

<p class="tip">Utilize _mixins_ globais raramente e cuidadosamente, porque eles afetam cada uma das instâncias Vue criadas, incluindo os componentes de terceiros. Na maioria dos casos, você deveria utilizá-los somente para tratar opções personalizadas, como demonstrado no exemplo acima. É também uma boa ideia disponibilizá-los como [Plugins](plugins.html), para evitar uso duplicado.</p>

## Mesclagem de Opções Personalizada

Quando opções personalizadas são mescladas, elas usam a estratégia padrão que sobrescreve o valor existente. Se você quiser que uma opção seja mesclada usando uma lógica personalizada, você precisa vincular uma função em `Vue.config.optionMergeStrategies`:

``` js
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) {
  // return mergedVal
}
```

Para a maioria das opções baseadas em objetos, pode usar a estratégia igual a `methods`:

``` js
var strategies = Vue.config.optionMergeStrategies
strategies.myOption = strategies.methods
```

Um exemplo mais avançado pode ser encontrado nesta estratégia de mesclagem do [Vuex 1.x](https://github.com/vuejs/vuex):

``` js
const merge = Vue.config.optionMergeStrategies.computed
Vue.config.optionMergeStrategies.vuex = function (toVal, fromVal) {
  if (!toVal) return fromVal
  if (!fromVal) return toVal
  return {
    getters: merge(toVal.getters, fromVal.getters),
    state: merge(toVal.state, fromVal.state),
    actions: merge(toVal.actions, fromVal.actions)
  }
}
```
