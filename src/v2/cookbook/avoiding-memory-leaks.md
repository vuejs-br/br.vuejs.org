---
title: Evitando Vazamentos de Memória
type: cookbook
order: 10
---

## Introdução

Se você estiver desenvolvendo aplicativos com Vue, precisará estar atento a vazamentos de memória. Atenção à esse problema é especialmente importante em Aplicativos de Página Única (SPAs) pois, por arquitetura, os usuários não devem atualizar seu navegador ao usar um SPA, por isso, cabe ao aplicativo JavaScript limpar os componentes e garantir que a coleta de lixo ocorra conforme o esperado.

Os vazamentos de memória nos aplicativos Vue geralmente não vêm do próprio Vue, mas podem acontecer ao incorporar outras bibliotecas em um aplicativo.

## Exemplo Simples

O exemplo a seguir mostra um vazamento de memória causado pelo uso da biblioteca [Choices.js](https://github.com/jshjohnson/Choices) em um componente Vue, sem limpá-lo corretamente. Mais tarde, mostraremos como remover os usos excessivos de memória do Choices.js e evitar o vazamento de memória.

No exemplo abaixo, nós carregamos um select com muitas opções e então usamos um botão mostrar/esconder com uma diretiva [v-if](/v2/guide/conditional.html) para adicioná-lo e removê-lo do DOM virtual. O problema com este exemplo é que a diretiva `v-if` remove o elemento pai do DOM, mas nós não limpamos as peças adicionais do DOM criadas pelo Choices.js, causando um vazamento de memória.

```html
<link rel='stylesheet prefetch' href='https://joshuajohnson.co.uk/Choices/assets/styles/css/choices.min.css?version=3.0.3'>
<script src='https://joshuajohnson.co.uk/Choices/assets/scripts/dist/choices.min.js?version=3.0.3'></script>

<div id="app">
  <button
    v-if="showChoices"
    @click="hide"
  >Esconder</button>
  <button
    v-if="!showChoices"
    @click="show"
  >Mostrar</button>
  <div v-if="showChoices">
    <select id="choices-single-default"></select>
  </div>
</div>
```

```js
new Vue({
  el: "#app",
  data: function () {
    return {
      showChoices: true
    }
  },
  mounted: function () {
    this.initializeChoices()
  },
  methods: {
    initializeChoices: function () {
      let list = []
      // vamos carregar nosso select com muitas escolhas
      // e isso irá usar muita memória
      for (let i = 0; i < 1000; i++) {
        list.push({
          label: "Item " + i,
          value: i
        })
      }
      new Choices("#choices-single-default", {
        searchEnabled: true,
        removeItemButton: true,
        choices: list
      })
    },
    show: function () {
      this.showChoices = true
      this.$nextTick(() => {
        this.initializeChoices()
      })
    },
    hide: function () {
      this.showChoices = false
    }
  }
})
```
Para ver este vazamento de memória em ação, abra este [Exemplo do CodePen](https://codepen.io/GuiDevloper/pen/jerMRo) usando Chrome e então abra o Gerenciador de Tarefas do Chrome. Para abri-lo no Mac, escolha Navegação do Topo do Chrome > Janela > Gerenciador de Tarefa ou no Windows, use o atalho Shift+Esc. Agora, clique no botão mostrar/esconder 50 ou mais vezes. Você precisa ver o uso de memória no Gerenciador de Tarefas do Chrome aumentando sem haver recuperação.

![Exemplo de Vazamento de Memória](/images/memory-leak-example.png)

## Resolvendo o Vazamento de Memória

No exemplo acima, nós podemos usar nosso método `hide()` para fazer uma limpeza e resolver o vazamento de memória antes de remover o select do DOM. Para fazer isso, nós iremos manter uma propriedade no objeto de dados de nossa instância Vue e iremos usar o método `destroy()` da [API do Choices](https://github.com/jshjohnson/Choices) para executar a limpeza.

Veja o uso de memória novamente com este [exemplo do CodePen atualizado](https://codepen.io/GuiDevloper/pen/mzEOMQ).

```js
new Vue({
  el: "#app",
  data: function () {
    return {
      showChoices: true,
      choicesSelect: null
    }
  },
  mounted: function () {
    this.initializeChoices()
  },
  methods: {
    initializeChoices: function () {
      let list = []
      for (let i = 0; i < 1000; i++) {
        list.push({
          label: "Item " + i,
          value: i
        })
      }
      // Defina uma referência para nosso choicesSelect
      // no objeto de dados de nossa instância Vue
      this.choicesSelect = new Choices("#choices-single-default", {
        searchEnabled: true,
        removeItemButton: true,
        choices: list
      })
    },
    show: function () {
      this.showChoices = true
      this.$nextTick(() => {
        this.initializeChoices()
      })
    },
    hide: function () {
      // agora nós podemos usar a referência do Choices para
      // executar a limpeza aqui, antes de remover os elementos do DOM
      this.choicesSelect.destroy()
      this.showChoices = false
    }
  }
})
```

## Detalhes sobre o Valor desta Prática

Gerenciamento de memória e teste de performance pode facilmente ser negligenciado na animação de entregar rapidamente, de qualquer forma, manter um pequeno uso de memória ainda é importante para a experiência geral do usuário.

Considere os tipos de dispositivos que seus usuários poderão usar e qual será o fluxo de uso deles. Eles poderão usar laptop com memória restritiva ou dispositivos móveis? Seus usuários tipicamente navegam muito no aplicativo? Se qualquer um destes for verdade, então práticas de bom gerenciamento de memória podem ajudar você a evitar o pior cenário possível de travar o navegador do usuário. Até se nenhum destes forem verdadeiros, você ainda pode ter uma potencial degradação de performance no uso estendido do seu app, se não for cuidadoso.

## Exemplo do Mundo Real

No exemplo acima, nós usamos uma diretiva `v-if` para ilustrar o vazamento de memória, mas o  exemplo real mais comum acontece quando usando o [vue-router](https://router.vuejs.org/en/) roteando para componentes em um Aplicativo de Página Única.

Assim como a diretiva `v-if`, o `vue-router` remove elementos do DOM virtual e substitui este por novos elementos quando um usuário navega em sua aplicação. O [gatilho do ciclo de vida](/v2/guide/instance.html#Lifecycle-Diagram) `beforeDestroy()` do Vue é um bom lugar para resolver o mesmo tipo de problema em uma aplicação baseada em `vue-router`.

Nós podemos mover nossa limpeza para dentro do gatilho `beforeDestroy()` assim:

```js
beforeDestroy: function () {
    this.choicesSelect.destroy()
}
```

## Padrões Alternativos

Nós discutimos o gerenciamento de memória quando removendo elementos, mas e se você intencionalmente quer preservar o estado e manter os elementos na memória? Neste caso, você pode usar o componente interno [keep-alive](/v2/api/#keep-alive).

Quando você embrulhar um componente com `keep-alive`, seu estado será preservado e já guardado na memória.

```html
<button @click="show = false">Esconder</button>
<keep-alive>
  <!-- my-component será intencionalmente guardado na memória quando removido -->
  <my-component v-if="show"></my-component>
</keep-alive>
```
Esta técnica pode ser útil para melhorar a experiência do usuário. Por exemplo, imagine que um usuário comece a inserir comentários em uma entrada de texto e então decide mudar de página. Se o usuário então navegar de volta, seus comentários ainda estarão preservados.

Desde que você use o `keep-alive`, então você terá acesso à mais dois gatilhos do ciclo de vida: `activated` e `deactivated`. Se você quiser limpar ou mudar dados quando um componente `keep-alive` é removido, você poderá faze-lo no gatilho `deactivated`.

```js
deactivated: function () {
  // remova alguns dados que você não quer manter
}
```

## Finalizando o Pacote

Vue torna muito fácil desenvolver aplicativos JavaScript espetaculares e reativos, mas você ainda precisa ser cuidadoso sobre vazamentos de memória. Estes vazamentos irão frequentemente ocorrer quando usando bibliotecas adicionais de terceiros que manipulam o DOM fora do Vue. Lembre-se de testar sua aplicação procurando vazamentos de memória e realizar os passos apropriados para limpar componentes quando necessário.
