---
title: Usando Axios para Consumir APIs
type: cookbook
order: 9
---

## Exemplo Base

Há diversos momentos quando você está desenvolvendo uma aplicação Web que podem necessitar consumir e exibir dados de uma API. Há várias maneiras de se fazer isso, mas a maneira mais popular é usando [axios](https://github.com/axios/axios), um cliente HTTP baseado em [Promises](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Promise).

Neste exercício, usaremos a [API CoinDesk](https://www.coindesk.com/api/) para exibir os preços do Bitcoin, atualizados a cada minuto. Primeiramente, precisamos instalar o axios via npm/yarn, ou através do endereço CDN.

Temos várias maneiras de recuperarmos informações de uma API, mas primeiro é interessante descobrir o formato dos dados, para sabermos o que mostraremos. Para fazer isso, faremos uma requisição para o _endpoint_ da API, para podermos ver os dados. Podemos ver na documentação da API CoinDesk que esta chamada será feita para `https://api.coindesk.com/v1/bpi/currentprice.json`. Assim, criaremos uma propriedade de dados que eventualmente abrigará nossa informação e, então, recuperaremos os dados e atribuiremos usando o gatilho de ciclo de vida `mounted`:

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

<p data-height="350" data-theme-id="32763" data-slug-hash="80043dfdb7b90f138f5585ade1a5286f" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Primeiro Passo com Axios e Vue" class="codepen">Veja o código <a href="https://codepen.io/team/Vue/pen/80043dfdb7b90f138f5585ade1a5286f/">Primeiro Passo com Axios e Vue</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Excelente! Já temos algumas informações. Mas parece um pouco confuso agora. Então, vamos exibir adequadamente a informação desejada e também adicionar algum tratamento de exceção no caso de alguma coisa não funcionar conforme esperado ou levar mais tempo do que esperávamos para recuperar as informações.

## Exemplo do Mundo Real: Trabalhando os Dados

### Mostrando o Dado Desejado

É muito comum que as informações que precisaremos estejam internamente na resposta obtida, então teremos que analisar o que acabamos de obter para conseguirmos acessar adequadamente. Neste caso, podemos ver que o preço informado está em `response.data.bpi`. Se usarmos isso, nossa saída seria a seguinte:

```js
axios
  .get('https://api.coindesk.com/v1/bpi/currentprice.json')
  .then(response => (this.info = response.data.bpi))
```

<p data-height="200" data-theme-id="32763" data-slug-hash="6100b10f1b4ac2961208643560ba7d11" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Segundo Passo com Axios e Vue" class="codepen">Veja o código <a href="https://codepen.io/team/Vue/pen/6100b10f1b4ac2961208643560ba7d11/">Segundo Passo com Axios e Vue</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Isso é muito mais fácil para nós exibirmos, então podemos atualizar o HTML para mostrar somente a informação que recebemos. Criaremos um [filter](../api/#Vue-filter) para ter certeza que a casa decimal estará no lugar correto.

```html
<div id="app">
  <h1>Preços do Bitcoin</h1>
  <div
    v-for="currency in info"
    class="currency">
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

<p data-height="300" data-theme-id="32763" data-slug-hash="9d59319c09eaccfaf35d9e9f11990f0f" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Terceiro Passo com Axios e Vue" class="codepen">Veja o código <a href="https://codepen.io/team/Vue/pen/9d59319c09eaccfaf35d9e9f11990f0f/">Terceiro Passo com Axios e Vue</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### Lidando com Erros

Às vezes, podemos não receber os dados que precisamos da API. Há vários motivos para que nossa chamada assíncrona possa falhar, seguem alguns exemplos:

* A API está fora do ar.
* A requisição foi realizada de forma incorreta.
* A API não retornou as informações da maneira que esperávamos.

Ao realizar a requisição, devemos checar todas essas circustâncias, obtendo informações em todos os casos para que possamos lidar com o problema. Em uma chamada _axios_, fazemos isso usando `catch`.

```js
axios
  .get('https://api.coindesk.com/v1/bpi/currentprice.json')
  .then(response => (this.info = response.data.bpi))
  .catch(error => console.log(error))
```

Assim, saberemos se algo falhou durante a requisição da API. Mas, se o dado está desconfigurado ou a API está fora do ar? Neste caso, o usuário não verá nada. Podemos exibir uma informação de carregamento (um _loader_) durante a requisição e, em seguida, avisar o usuário se não conseguirmos recuperar os dados.

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
  <h1>Preços do Bitcoin</h1>

  <section v-if="errored">
    <p>Pedimos desculpas, não estamos conseguindo recuperar as informações no momento. Por favor, tente novamente mais tarde.</p>
  </section>

  <section v-else>
    <div v-if="loading">Carregando...</div>

    <div
      v-else
      v-for="currency in info"
      class="currency">
      {{ currency.description }}:
      <span class="lighten">
        <span v-html="currency.symbol"></span>{{ currency.rate_float | currencydecimal }}
      </span>
    </div>

  </section>
</div>
```

Você pode clicar no botão reexecutar no exemplo abaixo, para poder ver rapidamente o estado de carregamento, enquanto buscamos os dados da API:

<p data-height="300" data-theme-id="32763" data-slug-hash="6c01922c9af3883890fd7393e8147ec4" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Quarto Passo com Axios e Vue" class="codepen">Veja o código <a href="https://codepen.io/team/Vue/pen/6c01922c9af3883890fd7393e8147ec4/">Quarto Passo com Axios e Vue</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Isso ainda poderia ser melhorado com o uso de componentes para diferentes seções e relatórios de erros mais distintos, dependendo da API que você está usando e da complexidade da sua aplicação.

## Padrões Alternativos

### Fetch API

[Fetch](https://developers.google.com/web/updates/2015/03/introduction-to-fetch) é uma API poderosa e nativa para requisições. Talvez você já tenha ouvido falar que um dos benefícios dela é que você não precisa carregar um código externo para utilizá-la, o que é verdade! Exceto… que ainda não é totalmente suportada, então você precisará de um _polyfill_. Existem também algumas armadilhas quando se trabalha com esta API, por isso que atualmente muitos preferem utilizar _axios_. Isso pode, muito bem, mudar no futuro.

Se você tem interesse em usar a Fetch API, existem [excelentes artigos](https://scotch.io/@bedakb/lets-build-type-ahead-component-with-vuejs-2-and-fetch-api) que explicam como fazer isso.

## Considerações Finais

Existem vários caminhos para trabalhar com Vue e _axios_, além de consumir e mostrar um _endpoint_ de uma API. Você também pode realizar comunicação com _Serveless Functions_, postar/editar/deletar de uma API que você tenha permissão de escrita, e entre outros casos. Devido à integração direta dessas duas bibliotecas, acabou se tornando uma escolha comum entre os desenvolvedores que precisam integrar clientes HTTP em seu fluxo de trabalho.
