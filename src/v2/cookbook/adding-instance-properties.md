---
title: Adicionando Propriedades à Instância
type: cookbook
order: 2
---

## Exemplo Base

Este exemplo pode ser útil se você precisar usar muitos dados/utilitários em vários componentes, mas sem [poluir o escopo global](https://github.com/getify/You-Dont-Know-JS/blob/2nd-ed/scope-closures/ch3.md). Nesses casos, você pode torná-los disponíveis em cada instância Vue, definindo-os no _prototype_:

```js
Vue.prototype.$appName = 'Meu Aplicativo'
```

Agora `$appName` está disponível em todas as instâncias Vue, mesmo antes da criação. Se nós executarmos: 

```js
new Vue({
  beforeCreate: function() {
    console.log(this.$appName)
  }
})
```

Então `"Meu Aplicativo"` será exibido no console!

## A Importância do Escopo para Propriedades de Instância

Você deve estar se perguntando:

> "Por que `appName` inicia com `$`? Isso é importante? O que isso faz?

Nenhuma mágica acontece aqui. `$` é uma convenção que o Vue usar para propriedades que estão disponíveis em todas as instâncias. Isso evita conflitos com qualquer definição de dados, dados computados ou métodos.

> "Conflitos? O que isso significa?"

Outra grande questão! Se você definir:

```js
Vue.prototype.appName = 'Meu Aplicativo'
```

Então o que será esperado para ser logado abaixo?

```js
new Vue({
  data: {
    // Uh oh - appName é *também* o nome 
    // da propriedade de instâncua que definimos!
    appName: 'O nome de algum outro aplicativo'
  },
  beforeCreate: function() {
    console.log(this.appName)
  },
  created: function() {
    console.log(this.appName)
  }
})
```

Seria `"Meu Aplicativo"`, então `"O nome de algum outro aplicativo"`, porque `this.appName` é sobrescrito ([Mais ou menos](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26% %20prototypes/ch5.md)) pelo `data` quando a instância é criada. Nós definimos o escopo de propriedades de instância com `$` para evitar isto. Você pode até usar a sua própria convenção se você preferir, algo como `$_appName` ou `ΩappName`, para evitar conflitos com plugins ou futuras funcionalidades.

## Exemplo do Mundo Real: Substituindo Vue Resource por Axios

Vamos dizer que você está substituindo o [aposentado Vue Resource](https://medium.com/the-vue-point/retiring-vue-resource-871a82880af4). Você realmente gostava de acessar os métodos de requisição através de `this.$http` e você gostaria de fazere a mesma coisa agora com o Axios.

Tudo que você tem que fazer é incluir o axios em seu projeto. 

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.15.2/axios.js"></script>

<div id="app">
  <ul>
    <li v-for="user in users">{{ user.name }}</li>
  </ul>
</div>
```

Atribuir `axios` para `Vue.prototype.$http`:

```js
Vue.prototype.$http = axios
```

Então você será capaz de usar métodos como `this.$http.get` em qualquer instância Vue.

```js
new Vue({
  el: '#app',
  data: {
    users: []
  },
  created() {
    var vm = this
    this.$http
      .get('https://jsonplaceholder.typicode.com/users')
      .then(function(response) {
        vm.users = response.data
      })
  }
})
```

## O Contexto de Métodos de Prototype

Para o caso de você não estar consciente, métodos adicionados no _prototype_ do JavaScript ganham o contexto da instância. Isso quer dizer que pode-se usar `this` para acessar dados, dados computados, métodos ou qualquer coisa definida na instância.

Vamos tomar vantagem disso no método `$reverseText`:

```js
Vue.prototype.$reverseText = function(propertyName) {
  this[propertyName] = this[propertyName]
    .split('')
    .reverse()
    .join('')
}

new Vue({
  data: {
    message: 'Hello'
  },
  created: function() {
    console.log(this.message) // => "Hello"
    this.$reverseText('message')
    console.log(this.message) // => "olleH"
  }
})
```

Note que a conexão de contexto **não** vai funcionar se você usar arrow function do ES6/2015,porque elas implicitamente se ligam ao seu escopo pai.
Isso quer dizer que a versão arrow function:


```js
Vue.prototype.$reverseText = propertyName => {
  this[propertyName] = this[propertyName]
    .split('')
    .reverse()
    .join('')
}
```

Jogaria um erro: 

```log
Uncaught TypeError: Cannot read property 'split' of undefined
```

## Quando Evitar Esse Padrão

Desde que você esteja vigilante em escopo das propriedades de _prototype_, usar esse padrão é bastante seguro, e provavelmente, não deve produzir erros.

Entretanto, em alguns casos pode causar confusão entre desenvolvedores. Eles pode ver `this.$http`, por exemplo e pensar, "Oh, eu não conhecia esse recurso do Vue!" então ele pode se mover para um projeto diferente e ficar confuso quando `this.$http` estiver indefinido. Ou talvez ele pode ir ao Google para ver como fazer algo, mas não encontrar resultados porque ele não percebeu que na realidade o que está sendo usado é Axios.

**A conveniência vem ao custo da clareza.** Quando olhamos um componente, é impossível dizer de onde `$http` vem. Vem do Vue em si? um plugin? Um colega de trabalho?

E quais são as alternativas?

## Padrões Alternativos

### Quando Não Está se Usando um Sistema de Módulos

Em aplicações **sem** sistema de módulos (por exemplo Webpack ou Browserify), existe um padrão que é frequentemente usado com _qualquer_ front-end JavaScript avançado: Um objeto `App` global. 

Se o que você deseja adicionar não tem nada a ver com o Vue especificamente, esse pode ser uma boa alternativa. Aqui um exemplo:

```js
var App = Object.freeze({
  name: 'My App',
  version: '2.1.4',
  helpers: {
    // Essa é uma versão puramente funcional
    // do método $reverseText que vimos mais cedo
    reverseText: function(text) {
      return text
        .split('')
        .reverse()
        .join('')
    }
  }
})
```

<p class="tip">Se você ergueu uma sombrancelha para `Object.freeze`, o que isso faz é previnir o objeto de ser alterado no futuro. Isso essencialmenete faz todas as propriedades serem constantes, protegendo você de futuros erros de estado.</p>

Agora a fonte dessas propriedades compartilhadas é mais óbvia: existe um objeto `App` definido em algum lugar do aplicativo. Para encontra-lo, os desenvolvedores pode realizar uma pesquisa em todo o projeto.

Outra vantagem é que `App` pode agora ser usado _em qualquer lugar_ do seu código, seja relacionado ao Vue ou não. Isso inclui adicionar valores diretamente as opções da instância, ao invês de ter que inserir uma funcão para acessar as propriedades em `this`.

```js
new Vue({
  data: {
    appVersion: App.version
  },
  methods: {
    reverseText: App.helpers.reverseText
  }
})
```

### Quando se Está Usando um Sistema de Módulos
Quando você tem acesso a um sistema de módulos, você pode facilmente organizar seu código em módulos e então `require`/`import` esses módulos onde você precisar deles. Isso é o resumo da clareza, porque em cada arquivo você ganha uma lista de dependências. Você sabe _exatamente_ de onde cada um deles veio.

Enquanto isso é certamente mais verboso, essa abordagem é definitivamente mais sustentável, especialmente quando trabalhando com outros desenvolvedores ou/e construindo aplicativos de grande porte.
