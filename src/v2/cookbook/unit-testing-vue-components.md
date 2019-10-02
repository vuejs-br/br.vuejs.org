---
title: Testes Unitários e Componentes Vue
type: cookbook
order: 6
---

## Exemplo Base

O teste unitário é uma parte fundamental do desenvolvimento de software. Eles executam as menores unidades de código isoladamente, a fim de aumentar a facilidade de adicionar novos recursos e rastrear erros. O uso dos [Componentes Single-File](../guide/single-file-components.html) do Vue simplifica a tarefa de escrever testes unitários para componentes isolados. Isso permite desenvolver novos recursos com a confiança de que você não está quebrando os já existentes e ajuda outros desenvolvedores a entender o que seu componente faz.

Este exemplo simples testa se algum texto é renderizado:

```html
<template>
  <div>
    <input v-model="username">
    <div 
      v-if="error"
      class="error"
    >
      {{ error }}
    </div>
  </div>
</template>

<script>
export default {
  name: 'Hello',
  data () {
    return {
      username: ''
    }
  },

  computed: {
    error () {
      return this.username.trim().length < 7
        ? 'Por favor insira um nome de usuário mais longo'
        : ''
    }
  }
}
</script>
```

```js
import { shallowMount } from '@vue/test-utils'
import Hello from './Hello.vue'

test('Hello', () => {
  // renderiza o componente
  const wrapper = shallowMount(Hello)

  // não deve permitir `username` menor que 7 caracteres, exclui espaço em branco
  wrapper.setData({ username: ' '.repeat(7) })

  // afirma se a mensagem de erro está renderizada
  expect(wrapper.find('.error').exists()).toBe(true)

  // atualiza o nome para ser longo o suficiente
  wrapper.setData({ username: 'Lachlan' })

  // afirma se a mensagem de erro se foi
  expect(wrapper.find('.error').exists()).toBe(false)
})
```
O trecho de código acima mostra como testar se uma mensagem de erro é exibida com base no comprimento do nome de usuário. Ele demonstra a ideia geral dos componentes Vue de teste unitário: renderizar o componente e afirmar que o código corresponde ao estado do componente.

## Por Que Testar?

Testes unitários em componentes têm muitos benefícios:

- Fornecer documentação sobre como o componente deve se comportar
- Economize mais tempo que testando manualmente
- Reduza erros em novos recursos
- Aprimorar arquitetura do código
- Facilitar refatoração

Testes automatizados permitem que grandes equipes de desenvolvedores mantenham bases de código complexas.

#### Começando

[Vue Test Utils](https://github.com/vuejs/vue-test-utils) é a biblioteca oficial para testes unitários em componentes Vue. O template `webpack` que pode ser criado via [vue-cli](https://github.com/vuejs/vue-cli) vem com Karma ou Jest, ambos executadores de teste bem suportados, e há alguns [guias](https://vue-test-utils.vuejs.org/guides/) na documentação do Vue Test Utils.

## Exemplo do Mundo Real

Os testes unitários devem ser:

- Rápido na execução
- Fácil de entender
- Testador de apenas _uma única unidade de código_

Vamos continuar construindo no exemplo anterior, enquanto introduzimos a ideia de uma <a href="https://en.wikipedia.org/wiki/Factory_(object-oriented_programming)">factory function</a> para tornar nosso teste mais compacto e legível. O componente deve:

- mostrar uma saudação "Bem-vindo ao livro de receitas do Vue.js"
- solicitar ao usuário que digite seu nome de usuário
- exibir um erro se o nome de usuário digitado for menor que sete letras

Vamos dar uma olhada no código do componente primeiro:

```html
<template>
  <div>
    <div class="message">
      {{ message }}
    </div>
    Digite seu nome de usuário: <input v-model="username">
    <div 
      v-if="error"
      class="error"
    >
      Por favor, digite um nome de usuário com pelo menos sete letras.
    </div>
  </div>
</template>

<script>
export default {
  name: 'Foo',

  data () {
    return {
      message: 'Bem-vindo ao livro de receitas do Vue.js',
      username: ''
    }
  },

  computed: {
    error () {
      return this.username.trim().length < 7
    }
  }
}
</script>
```

As coisas que devemos testar são:

- a `message` é exibida?
- se `error` é `true`, então a `<div class="error">` deve estar presente
- se `error` é `false`, então `<div class="error">` não pode estar presente

E nossa primeira tentativa de teste:

```js
import { shallowMount } from '@vue/test-utils'
import Foo from './Foo.vue'

describe('Foo', () => {
  it('exibe uma mensagem e responde corretamente à entrada do usuário', () => {
    const wrapper = shallowMount(Foo, {
      data: {
        message: 'Hello World',
        username: ''
      }
    })

    // afirma se a mensagem é processada
    expect(wrapper.find('.message').text()).toEqual('Hello World')

    // afirma que o erro é exibido
    expect(wrapper.find('.error').exists()).toBeTruthy()

    // atualiza o `username` e afirma que erro não é mais exibido
    wrapper.setData({ username: 'Lachlan' })
    expect(wrapper.find('.error').exists()).toBeFalsy()
  })
})
```

Existem alguns problemas com o código acima:

- um único teste está fazendo afirmações sobre coisas diferentes
- difícil dizer os diferentes estados em que o componente pode estar, e o que deve ser exibido

O exemplo abaixo melhora o teste pois:

- faz apenas uma afirmação para cada bloco `it`
- tem descrições de teste curtas e claras
- fornece apenas os dados mínimos necessários para o teste
- refatora a lógica duplicada (criando o `wrapper` e configurando a variável `username`) em uma _factory function_

*Teste atualizado*:
```js
import { shallowMount } from '@vue/test-utils'
import Foo from './Foo'

const factory = (values = {}) => {
  return shallowMount(Foo, {
    data () {
      return {
        ...values
      }
    }
  })
}

describe('Foo', () => {
  it('exibe uma mensagem de boas vindas', () => {
    const wrapper = factory()

    expect(wrapper.find('.message').text()).toEqual("Bem-vindo ao livro de receitas do Vue.js")
  })

  it('exibe um erro quando o nome de usuário tem menos de 7 caracteres', () => {
    const wrapper = factory({ username: ''  })

    expect(wrapper.find('.error').exists()).toBeTruthy()
  })

  it('exibe um erro quando o nome de usuário é espaço em branco', () => {
    const wrapper = factory({ username: ' '.repeat(7) })

    expect(wrapper.find('.error').exists()).toBeTruthy()
  })

  it('não exibe um erro quando o nome de usuário tem 7 caracteres ou mais', () => {
    const wrapper = factory({ username: 'Lachlan'  })

    expect(wrapper.find('.error').exists()).toBeFalsy()
  })
})
```

Pontos a serem observados:
Na parte superior, declaramos a factory function que mescla o objeto `values` na `data` e retorna uma nova instância de `wrapper`. Dessa forma, não é necessário duplicar `const wrapper = shallowMount(Foo)` em todos os testes. Outro grande benefício para isso é com componentes mais complexos contendo um método ou dados computados, você pode querer simular ou esboça-lo em cada teste, você só precisará declará-lo uma vez.

## Notas Adicionais

O teste acima é bastante simples, mas, na prática, os componentes do Vue costumam ter outros comportamentos que você deseja testar, como:

- fazendo chamadas de API
- cometer ou expedir mutações ou ações com um `Vuex` store
- testar interações

Há exemplos mais completos mostrando estes testes nos [guias](https://vue-test-utils.vuejs.org/guides/) do Vue Test Utils.

O Vue Test Utils e o enorme ecossistema JavaScript oferecem muitas ferramentas para facilitar quase 100% da cobertura por teste. Entretanto, testes unitários são apenas uma parte da pirâmide de testes. Alguns outros tipos de testes incluem testes _e2e_ (ponta a ponta) e testes _snapshot_. Os testes unitários são os menores e mais simples dos testes - eles fazem afirmações sobre as menores unidades de código, isolando cada parte de um único componente.

Os testes _snapshot_ salvam o código do seu componente Vue e o comparam com o novo gerado a cada vez que o teste é executado. Se algo mudar, o desenvolvedor é notificado e pode decidir se a alteração foi intencional (o componente foi atualizado) ou acidental (o componente está se comportando incorretamente).

Testes de ponta a ponta garantem que vários componentes interagem bem juntos. Eles são de nível alto. Alguns exemplos podem estar testando se um usuário pode se inscrever, efetuar login e atualizar seu nome de usuário. Estes são mais lentos para executar do que testes unitários ou testes _snapshot_.

Os testes de unidade são mais úteis durante o desenvolvimento, de modo que ajude um desenvolvedor a arquitetar um componente ou refatorar um existente e, geralmente, são executados sempre que o código é alterado.

Testes de nível alto, como testes de ponta a ponta, são executados muito mais lentamente. Eles geralmente são executados antes da implantação, para garantir que cada parte do sistema esteja funcionando corretamente.

Mais informações sobre como testar os componentes do Vue podem ser encontradas em [Testing Vue.js Applications](https://www.manning.com/books/testing-vuejs-applications) escrito pelo membro da equipe principal, [Edd Yerburgh](https://eddyerburgh.me/).

## Quando Evitar o Padrão

O teste unitário é uma parte importante de qualquer aplicativo sério. No início, quando a visão de uma aplicação não é clara, o teste de unidade pode retardar o desenvolvimento, mas uma vez que uma visão é estabelecida e usuários reais estarão interagindo com a aplicação, testes de unidade (e outros tipos de testes automatizados) são absolutamente essenciais para garantir que a base de código seja sustentável e escalável.
