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

Em seguida, importe o componente junto com o [Vue Test Utils](https://vue-test-utils.vuejs.org/), e você pode fazer muitas asserções comuns (aqui estamos usando asserções `expect` estilo Jest apenas como um exemplo):

``` js
// Importe `shallowMount` do Vue Test Utils e o componente sendo testado
import { shallowMount } from '@vue/test-utils'
import MyComponent from './MyComponent.vue'

// Monte o componente
const wrapper = shallowMount(MyComponent)

// Aqui estão alguns testes Jest, apesar de poder usar
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
    expect(wrapper.vm.$data.message).toBe('bye!')
  })

  // Monta uma instância e inspeciona a saída de renderização
  it('renders the correct message', () => {
    expect(wrapper.text()).toBe('bye!')
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

Você pode definir a sua saída de renderização com diferentes propriedades, usando [Vue Test Utils](https://vue-test-utils.vuejs.org/):

``` js
import { shallowMount } from '@vue/test-utils'
import MyComponent from './MyComponent.vue'

// função auxiliar que monta e retorna o componente renderizado
function getMountedComponent(Component, propsData) {
  return shallowMount(Component, {
    propsData
  })
}

describe('MyComponent', () => {
  it('renders correctly with different props', () => {
    expect(
      getMountedComponent(MyComponent, {
        msg: 'Hello'
      }).text()
    ).toBe('Hello')

    expect(
      getMountedComponent(MyComponent, {
        msg: 'Bye'
      }).text()
    ).toBe('Bye')
  })
})
```

## Definindo Atualizações Assíncronas

Desde que o Vue [executa atualizações do DOM de forma assíncrona](reactivity.html#Async-Update-Queue), as definições sobre atualizações do DOM resultantes da mudança de estado deverão ser feitas após `Vue.nextTick` ser resolvido:

``` js
// Inspect the generated HTML after a state update
it('updates the rendered message when wrapper.message updates', async () => {
  const wrapper = shallowMount(MyComponent)
  wrapper.setData({ message: 'foo' })

  // Wait a "tick" after state change before asserting DOM updates
  await wrapper.vm.$nextTick()
  expect(wrapper.text()).toBe('foo')
})
```

Para maiores informações sobre testes unitários no Vue, veja [Vue Test Utils](https://vue-test-utils.vuejs.org/) e nosso _cookbook_ sobre [testes unitários em componentes Vue](../cookbook/unit-testing-vue-components.html).
