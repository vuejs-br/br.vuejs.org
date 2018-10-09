---
title: Evitando Vazamentos de Memória
type: cookbook
order: 10
---

## Introdução

Se você estiver desenvolvendo aplicativos com Vue, poderá precisar se atentar a vazamentos de memória. A atenção a esse problema é especialmente importante em _Single Page Applications_ (SPAs) pois, por arquitetura, os usuários não devem precisar atualizar seu navegador enquanto usam um SPA e, por isso, cabe ao aplicativo JavaScript limpar os componentes da memória e garantir que a coleta de lixo ocorra conforme o esperado.

Os vazamentos de memória nos aplicativos Vue geralmente não vêm do próprio Vue, mas podem acontecer ao incorporar outras bibliotecas em um aplicativo.

## Exemplo Simples

O exemplo a seguir mostra um vazamento de memória causado pelo uso da biblioteca [Choices.js](https://github.com/jshjohnson/Choices) em um componente Vue, sem limpá-lo corretamente. Mais para frente, mostraremos como remover os usos excessivos de memória desta biblioteca e evitar o vazamento de memória.

No exemplo abaixo, nós carregamos um _select_ com muitas opções e, então, usamos um botão mostrar/esconder com uma diretiva [v-if](/v2/guide/conditional.html) para adicioná-lo e removê-lo do DOM virtual. O problema com este exemplo é que a diretiva `v-if` remove o elemento pai do DOM, mas nós não limpamos as peças adicionais do DOM criadas pelo Choices.js, causando um vazamento de memória.

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
      // vamos carregar o select com muitas opções,
      // o que irá consumir muita memória
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

Para ver este vazamento de memória em ação, abra este [Exemplo do CodePen](https://codepen.io/GuiDevloper/pen/jerMRo) usando Chrome e, então, abra o Gerenciador de Tarefas do Chrome. No Mac, escolha o menu de navegação do Chrome > Janela > Gerenciador de Tarefas. No Windows, use o atalho Shift+Esc. Agora, clique no botão mostrar/esconder por volta de 50 vezes. Você poderá observar o uso de memória no Gerenciador de Tarefas do Chrome aumentando, sem nunca se recuperar.

![Exemplo de Vazamento de Memória](/images/memory-leak-example.png)

## Resolvendo o Vazamento de Memória

No exemplo acima, nós podemos usar nosso método `hide()` para fazer uma limpeza e resolver o vazamento de memória, antes de remover o _select_ do DOM. Para fazer isso, iremos manter uma propriedade no objeto de dados de nossa instância Vue e usaremos o método `destroy()` da [API do Choices](https://github.com/jshjohnson/Choices) para executar a limpeza.

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
      // Defina a referência para nosso choicesSelect
      // no objeto de dados da nossa instância Vue
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
      // Agora, podemos usar a referência ao Choices para
      // executar a limpeza aqui, antes de remover elementos
      this.choicesSelect.destroy()
      this.showChoices = false
    }
  }
})
```

## Detalhes da Importância

Gerenciamento de memória e testes de desempenho podem facilmente ser negligenciados na animação de entregar rapidamente. De qualquer forma, manter o uso de memória baixo ainda é importante para a experiência geral do usuário.

Considere os tipos de dispositivos que seus usuários poderão utilizar e qual será o fluxo de uso deles. Eles poderão usar algum _laptop_ ou dispositivo móvel com restrições de memória? Seus usuários tipicamente navegam muito no aplicativo? Se qualquer uma destas perguntas tiver resposta afirmativa, práticas de bom gerenciamento de memória podem ajudar a evitar o pior cenário possível: travar o navegador do usuário. E, ainda que nenhuma das respostas seja afirmativa, você ainda pode ter uma potencial degradação de desempenho durante o uso estendido da aplicação, se não for cuidadoso.

## Exemplo do Mundo Real

No exemplo acima, usamos uma diretiva `v-if` para ilustrar o vazamento de memória, mas o exemplo real mais comum acontece quando usamos o [vue-router](https://router.vuejs.org/en/) para rotear componentes em um SPA.

Assim como a diretiva `v-if`, o `vue-router` remove elementos do DOM virtual e substitui estes por novos elementos quando um usuário navega em sua aplicação. O [gatilho do ciclo de vida](/v2/guide/instance.html#Lifecycle-Diagram) `beforeDestroy()` do Vue é um bom lugar para resolver o mesmo tipo de problema em uma aplicação baseada em `vue-router`.

Nós podemos mover nossa limpeza para dentro do gatilho `beforeDestroy()` assim:

```js
beforeDestroy: function () {
    this.choicesSelect.destroy()
}
```

## Padrões Alternativos

Nós discutimos o gerenciamento de memória enquanto removendo elementos. Mas, e se você intencionalmente quer preservar o estado e manter os elementos na memória? Neste caso, você pode usar o componente interno [keep-alive](/v2/api/#keep-alive).

Quando embrulhar um componente com `keep-alive`, o estado será preservado e mantido em memória.

```html
<button @click="show = false">Esconder</button>
<keep-alive>
  <!-- my-component será intencionalmente mantido na memória -->
  <my-component v-if="show"></my-component>
</keep-alive>
```

Esta técnica pode ser útil para melhorar a experiência do usuário. Por exemplo, imagine que um usuário comece a inserir comentários em uma postagem e, antes de enviar, decida mudar de página. Se o usuário navegar de volta, os comentários ainda estarão preservados.

Desde que você use o `keep-alive`, terá acesso a mais dois gatilhos do ciclo de vida: `activated` e `deactivated`. Se quiser limpar memória ou mudar dados quando um componente `keep-alive` é removido, poderá fazê-lo no gatilho `deactivated`.

```js
deactivated: function () {
  // remova alguns dados que você não quer manter
}
```

## Considerações Finais

Vue torna muito fácil desenvolver aplicativos JavaScript espetaculares e reativos, mas você ainda precisa ser cuidadoso sobre vazamentos de memória. Estes vazamentos ocorrerão com frequência quando usar bibliotecas adicionais de terceiros, que manipulam o DOM por fora do Vue. Lembre-se de testar sua aplicação, procurando vazamentos de memória, e realizar os passos apropriados para limpar componentes, quando necessário.
