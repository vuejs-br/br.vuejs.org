---
title: Usando Axios para Consumir APIs
type: cookbook
order: 9
---

## Exemplo Básico

Em diversos momentos quando voce esta desenvolvendo uma aplicacao web que voce deseja consumir e exibir dados de uma API. Há várias maneiras de fazer isso, mas a maneira mais popular é usando [axios](https://github.com/axios/axios), um cliente HTTP baseado em promise.

Neste exercício, usaremos a [API CoinDesk](https://www.coindesk.com/api/) para exibir os preços do Bitcoin, atualizado a cada minuto. Primeiros precisamos instalar o axios com npm/yarn ou através do endereço CDN.

Existem diversas maneiras para recuperarmos informações da API, mas primeiro é interessante descobrir como é o formato dos dados, para sabermos o que mostraremos. Para fazer isso, faremos uma requisição para o endpoint da API e podemos ver o formato dos dados. Podemos ver na documentação da API CoinDesk que esta chamada será feita para `https://api.coindesk.com/v1/bpi/currentprice.json`. Então primeiro, criaremos uma propriedade de dados que eventualmente abrigará nossa informação e que recuperamos os dados e atribuímos usando o gatilho de ciclo de vida `mounted`.

```js
new Vue({
  el: '#app',
  data () {
    return {
      info: null
    }
  },
  mounted () {
    axios
      .get('https://api.coindesk.com/v1/bpi/currentprice.json')
      .then(response => (this.info = response))
  }
})
```

```html
<div id="app">
  {{ info }}
</div>
```

E o que temos é isso:

<p data-height="350" data-theme-id="32763" data-slug-hash="80043dfdb7b90f138f5585ade1a5286f" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="First Step Axios and Vue" class="codepen">Veja o código <a href="https://codepen.io/team/Vue/pen/80043dfdb7b90f138f5585ade1a5286f/">Primeiro Passo com Axios e Vue</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Excelente! Já temos algumas informações. Mas parece um pouco confuso agora. Então vamos adicionar algum tratamento de exceção no caso de alguma coisa não funcionar conforme esperado ou levar mais tempo do que esperávamos para recuperar as informações.

## Exemplo do Mundo Real: Trabalhando com o Dado

### Mostrando o Dado de uma API

É muito comum que as informações que precisaremos estejam dentro da resposta e teremos que analisar o que acabamos de armazenar para conseguirmos acessá-las adequadamente. Por outro lado, podemos ver que o preço informado está em `response.data.bpi`. Se usarmos isso, nossa saída seria a seguinte:

```js
axios
  .get('https://api.coindesk.com/v1/bpi/currentprice.json')
  .then(response => (this.info = response.data.bpi))
```

<p data-height="200" data-theme-id="32763" data-slug-hash="6100b10f1b4ac2961208643560ba7d11" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Second Step Axios and Vue" class="codepen">Veja o código <a href="https://codepen.io/team/Vue/pen/6100b10f1b4ac2961208643560ba7d11/">Segundo Passo com Axios e Vue</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Isso é muito fácil para exibirmos, então podemos agora atualizar nosso HTML para mostrar somente a informação que recebemos e criarmos um [filter](../api/#Vue-filter) para ter certeza que o decimal está no lugar correto.

```html
<div id="app">
  <h1>Página Inicial Preço Bitcoin</h1>
  <div
    v-for="currency in info"
    class="currency"
  >
    {{ currency.description }}:
    <span class="lighten">
      <span v-html="currency.symbol"></span>{{ currency.rate_float | currencydecimal }}
    </span>
  </div>
</div>
```

```js
filters: {
  currencydecimal (value) {
    return value.toFixed(2)
  }
},
```

<p data-height="300" data-theme-id="32763" data-slug-hash="9d59319c09eaccfaf35d9e9f11990f0f" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Third Step Axios and Vue" class="codepen">Veja o código <a href="https://codepen.io/team/Vue/pen/9d59319c09eaccfaf35d9e9f11990f0f/">Terceiro Passo com Axios e Vue</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### Lidando com Erros

Às vezes podemos não receber os dados que precisamos da API. Há vários motivos para que nossa chamada axios possa falhar, seguem alguns exemplos:

* A API está fora do ar.
* A requisição foi realizada de forma incorreta.
* A API não retornou as informações da maneira que esperávamos.

Ao realizar a requisição, devemos realizar a verificação a cada  passo da nossa requisição e retornarmos informações em todos os casos, desta forma então sabemos como lidar com o problema. Em uma chamada axios, fazemos isso usando `catch`.

```js
axios
  .get('https://api.coindesk.com/v1/bpi/currentprice.json')
  .then(response => (this.info = response.data.bpi))
  .catch(error => console.log(error))
```

Assim saberemos se algo falhou durante a requisição da API, mas se o dado está desconfigurado ou a API está fora do ar? Neste momento o usuário não verá nada. Podemos construir um carregador (loader) para este caso e em seguida avisar o usuário se não conseguirmos recuperar os dados.

```js
new Vue({
  el: '#app',
  data () {
    return {
      info: null,
      loading: true,
      errored: false
    }
  },
  filters: {
    currencydecimal (value) {
      return value.toFixed(2)
    }
  },
  mounted () {
    axios
      .get('https://api.coindesk.com/v1/bpi/currentprice.json')
      .then(response => {
        this.info = response.data.bpi
      })
      .catch(error => {
        console.log(error)
        this.errored = true
      })
      .finally(() => this.loading = false)
  }
})
```

```html
<div id="app">
  <h1>Página Inicial Preço Bitcoin</h1>

  <section v-if="errored">
    <p>Pedimos desculpas, não estamos recuperamos essas informações no momento, por favor tente novamente mais tarde</p>
  </section>

  <section v-else>
    <div v-if="loading">Carregando...</div>

    <div
      v-else
      v-for="currency in info"
      class="currency"
    >
      {{ currency.description }}:
      <span class="lighten">
        <span v-html="currency.symbol"></span>{{ currency.rate_float | currencydecimal }}
      </span>
    </div>

  </section>
</div>
```

Você pode clicar no botão reexecutar no exemplo abaixo para ver rapidamente o status do carregamento enquanto recuperamos os dados da API:

<p data-height="300" data-theme-id="32763" data-slug-hash="6c01922c9af3883890fd7393e8147ec4" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Fourth Step Axios and Vue" class="codepen">Veja o código <a href="https://codepen.io/team/Vue/pen/6c01922c9af3883890fd7393e8147ec4/">Quarto Passo com Axios e Vue</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Isso ainda pode ser melhorado com o uso de componentes para diferentes seções e relatórios de erros mais distintos, dependendo da API que você está usando e da complexidade da sua aplicação.

## Padrões Alternativos

### Fetch API

[Fetch API](https://developers.google.com/web/updates/2015/03/introduction-to-fetch) é uma API poderosa para esses tipos de requisições. Você pode já ter ouvido falar que um dos benefícios do Fetch API é que você não precisa carregar um código externo para utilizá-lo, o que é verdade! Exceto… que ainda não é totalmente suportado, você ainda precisa de um polyfill. Existem também algumas armadilhas quando se trabalha com Fetch API e é por isso que atualmente há uma preferência em utilizar axios. Isso pode muito bem mudar no futuro.

Se você tem interesse em usar a Fetch API, existem [excelentes artigos](https://scotch.io/@bedakb/lets-build-type-ahead-component-with-vuejs-2-and-fetch-api) que explicam como Fetch API funciona.

## Empacotamento

Existem vários caminhos para trabalhar com Vue e axios além de consumir e mostra uma API. Você também pode realizar uma comunicação com Function Serveless, post/edit/delete de uma API onde você pode ter permissão de escrita e entre outros.
Devido a integração direta dessas duas bibliotecas, ela se tornou uma escolha muito comum entre os desenvolvedores que precisam integrar clientes HTTP em seu fluxo de trabalho.