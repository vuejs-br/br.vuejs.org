---
title: Testes Unitários
type: guide
order: 402
---

> O [Vue CLI](https://cli.vuejs.org/) tem opções internas para testes unitários com [Jest](https://github.com/facebook/jest) ou [Mocha](https://mochajs.org/) que funcionam imediatamente. Também temos o [Vue Test Utils](https://vue-test-utils.vuejs.org/) oficial, que fornece orientações mais detalhadas para configurações personalizadas.

## Declarações Simples

Você não precisa fazer nada especial em seus componentes para torná-los testáveis. A exportação padrão de opções basta:

``` html
<template>
  <span>{{ message }}</span>
</template>

<script>
  export default {
    data () {
      return {
        message: 'hello!'
      }
    },
    created () {
      this.message = 'bye!'
    }
  }
</script>
```

Em seguida, importe as opções do componente junto com o Vue, e você pode fazer muitas asserções comuns (aqui estamos usando asserções `expect` estilo Jasmine/Jest apenas como um exemplo):

``` js
// Importando o Vue e o componente a ser testado
import Vue from 'vue'
import MyComponent from 'path/to/MyComponent.vue'

// Alguns testes do Jasmine 2.0, apesar de que você pode usar
// qualquer test runner / assertion library que preferir
describe('MyComponent', () => {
  // Inspeciona as opções do componente
  it('has a created hook', () => {
    expect(typeof MyComponent.created).toBe('function')
  })

  // Avalia os resultados das funções
  // nas opções do componente
  it('sets the correct default data', () => {
    expect(typeof MyComponent.data).toBe('function')
    const defaultData = MyComponent.data()
    expect(defaultData.message).toBe('hello!')
  })

  // Inspeciona a instância do componente ao montar
  it('correctly sets the message when created', () => {
    const vm = new Vue(MyComponent).$mount()
    expect(vm.message).toBe('bye!')
  })

  // Monta uma instância e inspeciona a saída de renderização
  it('renders the correct message', () => {
    const Constructor = Vue.extend(MyComponent)
    const vm = new Constructor().$mount()
    expect(vm.$el.textContent).toBe('bye!')
  })
})
```

## Escrevendo Componentes Testáveis

O resultado da renderização de componentes é primariamente determinado pelas `props` que recebem. Se a renderização de um componente somente depender disto, torna-se simples testar, similar a garantir o valor de retorno de uma função usando diferentes argumentos. Tome um exemplo simplificado:

``` html
<template>
  <p>{{ msg }}</p>
</template>

<script>
  export default {
    props: ['msg']
  }
</script>
```
Você pode definir a sua saída de renderização com diferentes propriedades, usando a opção `propsData`:

``` js
import Vue from 'vue'
import MyComponent from './MyComponent.vue'

// helper function that mounts and returns the rendered text
function getRenderedText (Component, propsData) {
  const Constructor = Vue.extend(Component)
  const vm = new Constructor({ propsData: propsData }).$mount()
  return vm.$el.textContent
}

describe('MyComponent', () => {
  it('renders correctly with different props', () => {
    expect(getRenderedText(MyComponent, {
      msg: 'Hello'
    })).toBe('Hello')

    expect(getRenderedText(MyComponent, {
      msg: 'Bye'
    })).toBe('Bye')
  })
})
```

## Definindo Atualizações Assíncronas

Desde que o Vue [executa atualizações do DOM de forma assíncrona](reactivity.html#Async-Update-Queue), as definições sobre atualizações do DOM resultantes da mudança de estado deverão ser feitas em um callback `Vue.nextTick`:

``` js
// Inspect the generated HTML after a state update
it('updates the rendered message when vm.message updates', done => {
  const vm = new Vue(MyComponent).$mount()
  vm.message = 'foo'

  // wait a "tick" after state change before asserting DOM updates
  Vue.nextTick(() => {
    expect(vm.$el.textContent).toBe('foo')
    done()
  })
})
```

Para maiores informações sobre testes unitários no Vue, veja [Vue Test Utils](https://vue-test-utils.vuejs.org/) e nosso _cookbook_ sobre [testes unitários em componentes Vue](../cookbook/unit-testing-vue-components.html).
