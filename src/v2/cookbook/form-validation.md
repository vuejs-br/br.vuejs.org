---
title: Validação de Formulários
type: cookbook
order: 3
---

## Exemplo Base

<div class="vueschool"><a href="https://vueschool.io/lessons/vuejs-form-validation-diy?friend=vuejs" target="_blank" rel="noopener" title="Lição Gratuita sobre Validação de Formulários com Vue.js">Assista a uma lição gratuita na Vue School</a></div>

Validação de formulários é algo suportado nativamente pelo navegador, mas eventualmente navegadores diferentes liderão com as coisas de um jeito que pode ser um pouco complicado. Mesmo quando a validação é suportada perfeitamente, poderão haver necessidades de personalização e, portanto, uma solução baseada em Vue poderá ser mais apropriada. Vamos começar com um exemplo simples.

Dado um formulário com três campos, faremos dois obrigatórios. Vamos ver o HTML primeiro:

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
>

  <p v-if="errors.length">
    <b>Por favor, corrija o(s) seguinte(s) erro(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    <label for="name">Nome</label>
    <input
      id="name"
      v-model="name"
      type="text"
      name="name"
    >
  </p>

  <p>
    <label for="age">Idade</label>
    <input
      id="age"
      v-model="age"
      type="number"
      name="age"
      min="0"
    >
  </p>

  <p>
    <label for="movie">Filme favorito</label>
    <select
      id="movie"
      v-model="movie"
      name="movie"
    >
      <option>Star Wars</option>
      <option>Vanilla Sky</option>
      <option>Atomic Blonde</option>
    </select>
  </p>

  <p>
    <input
      type="submit"
      value="Enviar"
    >
  </p>

</form>
```

Vamos avaliar do início. A _tag_ `<form>` tem um ID que nós usaremos para o componente Vue. Há um botão de envio, que falaremos sobre em um momento, e o `action` do formulário, uma URL temporária que aponta para algum caminho real em algum lugar (onde haverá uma réplica da validação no lado do servidor, claro).

Abaixo do formulário, há um parágrafo que irá se mostrar ou se esconder baseando-se em um estado de erro. Isso renderizará uma lista de erros no topo do formulário. Além disso, note que colocamos a validação para ser disparada no evento de envio do formulário.

A última coisa a se observar é que cada um dos três campos tem um `v-model` correspondente para conectá-lo com valores que iremos trabalhar no JavaScript. Agora, vamos ao exemplo.

``` js
const app = new Vue({
  el: '#app',
  data: {
    errors: [],
    name: null,
    age: null,
    movie: null
  },
  methods:{
    checkForm: function (e) {
      if (this.name && this.age) {
        return true;
      }

      this.errors = [];

      if (!this.name) {
        this.errors.push('O nome é obrigatório.');
      }
      if (!this.age) {
        this.errors.push('A idade é obrigatória.');
      }

      e.preventDefault();
    }
  }
})
```

Bastante curto e simples. Definimos um Array para manter erros e fixamos valores iniciais nulos para os três campos do formulário. A lógica do `checkForm` (lembrando que é executado no envio do formulário) valida apenas nome e idade, uma vez que o filme é opcional. Caso esteja vazio, checamos cada um e definimos um erro específico. E é apenas isso. Você pode testar abaixo. Não se esqueça que, em um envio bem-sucedido, você será direcionado a uma URL temporária vazia.

<p data-height="265" data-theme-id="0" data-slug-hash="yRMVLj" data-default-tab="html,result" data-user="ErickPetru" data-embed-version="2" data-pen-title="Validação de Formulários com Vue 1" class="codepen">Veja o Pen <a href="https://codepen.io/ErickPetru/pen/yRMVLj/">Validação de Formulários com Vue 1</a> por Erick Eduardo Petrucelli (<a href="https://codepen.io/ErickPetru">@ErickPetru</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Usando Validação Personalizada

Para o segundo exemplo, o segundo campo de texto (_age_) será modificado para _email_, necessitando de uma pequena lógica de validação personalizada. O código foi retirado de uma pergunta do StackOverflow, [How to validate email address in JavaScript?](https://stackoverflow.com/questions/46155/how-to-validate-email-address-in-javascript). Esse é um ótimo exemplo de pergunta, pois faz com que seu maior embate político/religioso no Facebook pareça um leve desentendimento sobre quem faz a melhor cerveja. Sério - é insano. Aqui está o HTML, ainda que seja bem próximo do primeiro exemplo.

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
  novalidate="true"
>

  <p v-if="errors.length">
    <b>Por favor, corrija o(s) seguinte(s) erro(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    <label for="name">Nome</label>
    <input
      id="name"
      v-model="name"
      type="text"
      name="name"
    >
  </p>

  <p>
    <label for="email">Email</label>
    <input
      id="email"
      v-model="email"
      type="email"
      name="email"
    >
  </p>

  <p>
    <label for="movie">Filme favorito</label>
    <select
      id="movie"
      v-model="movie"
      name="movie"
    >
      <option>Star Wars</option>
      <option>Vanilla Sky</option>
      <option>Atomic Blonde</option>
    </select>
  </p>

  <p>
    <input
      type="submit"
      value="Enviar"
    >
  </p>

</form>
```

Embora a mudança no _template_ seja pequena, perceba o `novalidate="true"` no início. Isso é muito importante, pois sem isso o navegador iria se responsabilizar pela validação do endereço de e-mail para o campo do tipo `type="email"`. Francamente, faria mais sentido confiar na validação do navegador nesse caso, mas como queremos um exemplo de validação com personalização, iremos desabilitá-la. Aqui está o JavaScript atualizado:

``` js
const app = new Vue({
  el: '#app',
  data: {
    errors: [],
    name: null,
    email: null,
    movie: null
  },
  methods: {
    checkForm: function (e) {
      this.errors = [];

      if (!this.name) {
        this.errors.push('O nome é obrigatório.');
      }
      if (!this.email) {
        this.errors.push('O e-mail é obrigatório.');
      } else if (!this.validEmail(this.email)) {
        this.errors.push('Utilize um e-mail válido.');
      }

      if (!this.errors.length) {
        return true;
      }

      e.preventDefault();
    },
    validEmail: function (email) {
      var re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(email);
    }
  }
})
```

Como você pode ver, adicionamos `validEmail` como um novo método, sendo chamado através do `checkForm`. Você pode testar este novo exemplo aqui:

<p data-height="265" data-theme-id="0" data-slug-hash="zmZove" data-default-tab="html,result" data-user="ErickPetru" data-embed-version="2" data-pen-title="Validação de Formulários com Vue 2" class="codepen">Veja o Pen <a href="https://codepen.io/ErickPetru/pen/zmZove/">Validação de Formulários com Vue 2</a> por Erick Eduardo Petrucelli (<a href="https://codepen.io/ErickPetru">@ErickPetru</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Outro Exemplo de Validação Personalizada

Para um terceiro exemplo, construiremos algo que provavelmente você já viu em aplicativos de questionários. O usuário é orientado a gastar o "orçamento" para um conjunto de recursos, digamos, para um novo modelo de Estrela da Morte. O total precisa ser igual a 100. Veja primeiro o HTML:

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
  novalidate="true"
>

  <p v-if="errors.length">
    <b>Por favor, corrija o(s) seguinte(s) erro(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    Dado um orçamento de 100 dólares, indique
    quanto você gastaria nos seguintes recursos
    para uma próxima geração da Estrela da Morte.
    O valor total deve ser 100.
  </p>

  <p>
    <input
      v-model.number="weapons"
      type="number"
      name="weapons"
    > Armas <br/>
    <input
      v-model.number="shields"
      type="number"
      name="shields"
    > Escudos <br/>
    <input
      v-model.number="coffee"
      type="number"
      name="coffee"
    > Café <br/>
    <input
      v-model.number="ac"
      type="number"
      name="ac"
    > Ar condicionado <br/>
    <input
      v-model.number="mousedroids"
      type="number"
      name="mousedroids"
    > Droides rato <br/>
  </p>

  <p>
    Total atual: {{total}}
  </p>

  <p>
    <input
      type="submit"
      value="Enviar"
    >
  </p>

</form>
```

Note que os conjuntos de `input` cobrem os cinco diferentes recursos possíveis. Observe a adição do modificador `.number` para o atributo `v-model`. Isso diz ao Vue que deve converter automaticamente o valor informado para um Number quando for preenchido. No entanto, existe um problema em potencial com este recurso, pois, quando o valor é vazio, ele se mantém como String. Você verá como contornar esta situação abaixo. Para tornar a utilização um pouco mais fácil para o usuário, adicionamos também uma exibição do total atual em tempo real, logo abaixo dos campos. Agora vamos ver o JavaScript:

``` js
const app = new Vue({
  el: '#app',
  data:{
    errors: [],
    weapons: 0,
    shields: 0,
    coffee: 0,
    ac: 0,
    mousedroids: 0
  },
  computed: {
     total: function () {
       // Deve-se converter, pois o Vue mantém como String os valores vazios
       return Number(this.weapons) +
         Number(this.shields) +
         Number(this.coffee) +
         Number(this.ac) +
         Number(this.mousedroids);
     }
  },
  methods:{
    checkForm: function (e) {
      this.errors = [];

      if (this.total != 100) {
        this.errors.push('O total deve ser 100!');
      }

      if (!this.errors.length) {
        return true;
      }

      e.preventDefault();
    }
  }
})
```

Definimos o valor total como um dado computado, fora do trecho que faz a validação, algo muito simples de se configurar. O método `checkForm`, agora, apenas precisa observar se o total é 100, e é só isso. Você pode testar isso aqui:

<p data-height="265" data-theme-id="0" data-slug-hash="pxeNyK" data-default-tab="html,result" data-user="ErickPetru" data-embed-version="2" data-pen-title="Validação de Formulários com Vue 3" class="codepen">Veja o Pen <a href="https://codepen.io/ErickPetru/pen/pxeNyK/">Validação de Formulários com Vue 3</a> por Erick Eduardo Petrucelli (<a href="https://codepen.io/ErickPetru">@ErickPetru</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Validação no Lado do Servidor

No último exemplo, construiremos algo que faz uso de Ajax para validar no servidor. O formulário pedirá para nomear um novo produto e, então, checará para garantir que o nome escolhido é único. Escrevemos uma rápida ação no _serverless_ [OpenWhisk](http://openwhisk.apache.org/) para fazer a validação desejada. Embora não seja muito importante, aqui está a lógica utilizada para fins de exemplo:

``` js
function main(params) {
    return new Promise((resolve, reject) => {
        // Nome de produtos ruins: vista, empire, mbp
        const badNames = ['vista', 'empire', 'mbp'];

        if (badNames.includes(params.name.toLowerCase())) {
          reject({error: 'Produto já existente.'});
        }

        resolve({status: 'ok'});
    });
}
```

Basicamente, qualquer nome exceto "vista", "empire" e "mbp" são aceitáveis. Vamos ver o formulário:

``` html
<form
  id="app"
  @submit="checkForm"
  method="post"
>

  <p v-if="errors.length">
    <b>Por favor, corrija o(s) seguinte(s) erro(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    <label for="name">Novo nome do produto: </label>
    <input
      id="name"
      v-model="name"
      type="text"
      name="name"
    >
  </p>

  <p>
    <input
      type="submit"
      value="Enviar"
    >
  </p>

</form>
```

Não há nada especial aqui. Então vamos para o JavaScript:

``` js
const apiUrl = 'https://openwhisk.ng.bluemix.net/api/v1/web/vue_dev/get-http-resource/productName.json?name=';

const app = new Vue({
  el: '#app',
  data: {
    errors: [],
    name: ''
  },
  methods:{
    checkForm: function (e) {
      e.preventDefault();

      this.errors = [];

      if (this.name === '') {
        this.errors.push('O nome do produto é obrigatório.');
      } else {
        fetch(apiUrl + encodeURIComponent(this.name))
        .then(res => res.json())
        .then(res => {
          if (res.error) {
            this.errors.push(res.error);
          } else {
            // Poderia redirecionar a uma nova URL ou fazer qualquer outra coisa
            alert('Ok!');
          }
        });
      }
    }
  }
})
```

Começamos com uma variável representando a URL da API que está executando no OpenWhisk. Veja que o `checkForm`. Nessa versão, nós sempre impedimos que o formulário envie (o que, a propósito, poderia ser feito no HTML com Vue também). Você pode ver uma verificação básica para identificar se `this.name` está vazio e, então, disparamos a API. Se algo der errado, adicionamos um erro como feito antes. Se estiver certo, não estamos fazendo nada especial (apenas um alerta), mas você pode encaminhar o usuário para uma nova página com o nome do produto na URL, ou fazer outras ações conforme desejar. Você pode testar esse exemplo abaixo:

<p data-height="265" data-theme-id="0" data-slug-hash="QZpGKQ" data-default-tab="html,result" data-user="ErickPetru" data-embed-version="2" data-pen-title="Validação de Formulários com Vue 4" class="codepen">Veja o Pen <a href="https://codepen.io/ErickPetru/pen/QZpGKQ/">Validação de Formulários com Vue 4</a> por Erick Eduardo Petrucelli (<a href="https://codepen.io/ErickPetru">@ErickPetru</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Padrões Alternativos

Embora esse tópico do Livro de Receitas tenha se focado em fazer validação de formulários "à mão", existem, claro, algumas ótimas bibliotecas Vue que vão lidar com muitos destes detalhes para você. Trocando para uma biblioteca previamente construída, você pode sentir um impacto no tamanho final de sua aplicação, mas o benefício pode ser enorme. Você terá código (muito provavelmente) testado e também atualizado regularmente. Alguns exemplos de bibliotecas de validação de formulários para o Vue incluem:

* [vuelidate](https://github.com/monterail/vuelidate)
* [VeeValidate](https://logaretm.github.io/vee-validate/)
