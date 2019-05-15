<<<<<<< HEAD
---
title: Validação de Formulários
type: cookbook
order: 3
---

## Exemplo Base

Validação de formulários é algo suportado nativamente pelo navegador. Porém, navegadores diferentes irão lidar com as coisas de um jeito que pode ser um pouco complicado. Mesmo quando uma validação é suportada perfeitamente, haverá, algumas vezes, a necessidade de a personalizar e, portanto, uma solução baseada em Vue poderá ser mais apropriada. Vamos começar com um exemplo simples.

Dado um formulário com três campos, faremos dois obrigatórios. Vamos ver o HTML primeiro:

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post">
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
      name="name">
  </p>

  <p>
    <label for="age">Idade</label>
    <input
      id="age"
      v-model="age"
      type="number"
      name="age"
      min="0">
  </p>

  <p>
    <label for="movie">Filme favorito</label>
    <select
      id="movie"
      v-model="movie"
      name="movie">
      <option>Star Wars</option>
      <option>Vanilla Sky</option>
      <option>Atomic Blonde</option>
    </select>
  </p>

  <p>
    <input
      type="submit"
      value="Enviar">
  </p>
</form>
```

Vamos avaliar do início. A _tag_ `<form>` tem um ID que nós usaremos para o componente Vue. Há um botão de envio, que falaremos sobre em um momento, e o `action` do formulário, uma URL temporária que aponta para algum caminho real em algum lugar (onde haverá uma réplica da validação no lado do servidor, claro).

Abaixo do formulário, há um parágrafo que irá se mostrar ou se esconder baseando-se em um estado de erro. Isso renderizará uma lista de erros no topo do formulário. Além disso, note que colocamos a validação para ser disparada no evento de envio do formulário.

A última coisa a se observar é que cada um dos três campos tem um `v-model` correspondente para conecta-lo com valores que iremos trabalhar no JavaScript. Agora, vamos ao exemplo.

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

Para o segundo exemplo, o segundo campo de texto (_age_) será modificado para _email_, necessitando de uma pequena lógica de validação personalizada. O código foi retirado de uma pergunta do StackOverflow, [How to validate email address in JavaScript?](https://stackoverflow.com/questions/46155/how-to-validate-email-address-in-javascript). Esse é um ótimo exemplo de questão, pois faz com que seu maior embate político/religioso no Facebook pareça um leve desentendimento sobre quem faz a melhor cerveja.

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
  novalidate="true">
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
      name="name">
  </p>

  <p>
    <label for="email">Email</label>
    <input
      id="email"
      v-model="email"
      type="email"
      name="email">
  </p>

  <p>
    <label for="movie">Filme favorito</label>
    <select
      id="movie"
      v-model="movie"
      name="movie">
      <option>Star Wars</option>
      <option>Vanilla Sky</option>
      <option>Atomic Blonde</option>
    </select>
  </p>

  <p>
    <input
      type="submit"
      value="Enviar">
  </p>
</form>
```

Embora a mudança no _template_ seja pequena, perceba o `novalidate="true"` no início. Isso é muito importante, pois o navegador irá se atentar para a validação do endereço de email quando o campo for do tipo `type="email"`. Francamente, faria mais sentido confiar na validação do navegador nesse caso, mas como queremos um exemplo de validação com personalização, iremos desabilitá-la. Aqui está o JavaScript atualizado:

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

Como se pode ver, adicionamos `validEmail` como um novo método, sendo chamado através do `checkForm`. Você pode testar este novo exemplo aqui:

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
  novalidate="true">
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
      name="weapons"> Armas <br/>
    <input
      v-model.number="shields"
      type="number"
      name="shields"> Escudos <br/>
    <input
      v-model.number="coffee"
      type="number"
      name="coffee"> Café <br/>
    <input
      v-model.number="ac"
      type="number"
      name="ac"> Ar condicionado <br/>
    <input
      v-model.number="mousedroids"
      type="number"
      name="mousedroids"> Droides rato <br/>
  </p>

  <p>
    Total atual: {{total}}
  </p>

  <p>
    <input
      type="submit"
      value="Enviar">
  </p>
</form>
```

Note que os conjuntos de entrada cobrem os cinco diferentes recursos possíveis. Observe a adição do modificador `.number` para o atributo `v-model`. Isso diz ao Vue que deve converter automaticamente o valor informado para um Number quando for preenchido. No entanto, existe um problema com este recurso, pois quando o valor é vazio, ele se mantém como String. Você verá a solução abaixo. Para tornar a utilização um pouco mais fácil para o usuário, adicionamos uma exibição do total atual em tempo real. Agora vamos ver o JavaScript:

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

Definimos o valor total como um dado computado, fora do trecho que faz a validação, algo muito simples de se configurar. O método `checkForm`, agora, apenas precisa observar se o total é 100, só isso. Você pode testar isso aqui:

<p data-height="265" data-theme-id="0" data-slug-hash="pxeNyK" data-default-tab="html,result" data-user="ErickPetru" data-embed-version="2" data-pen-title="Validação de Formulários com Vue 3" class="codepen">Veja o Pen <a href="https://codepen.io/ErickPetru/pen/pxeNyK/">Validação de Formulários com Vue 3</a> por Erick Eduardo Petrucelli (<a href="https://codepen.io/ErickPetru">@ErickPetru</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Validação no Lado do Servidor

No último exemplo, construiremos algo que faz uso de Ajax para validar no servidor. O formulário pedirá para nomear um novo produto e, então, checará se o nome é único. Escrevemos uma rápida ação no _serverless_ [OpenWhisk](http://openwhisk.apache.org/) para fazer a validação desejada. Embora não seja muito importante, aqui está a lógica utilizada para fins de exemplo:

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
  method="post">
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
      name="name">
  </p>

  <p>
    <input
      type="submit"
      value="Enviar">
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
            // Poderia redirecionar a uma nova URL ou faça qualquer outra coisa
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

Embora esse tópico do Livro de Receitas tenha se focado em fazer validação de formulários "à mão", existe, claro, algumas ótimas bibliotecas Vue que vão lidar com muitos destes detalhes para você. Trocando para uma biblioteca pré-empacotada, você pode sentir um impacto no tamanho final de sua aplicação, mas o benefício pode ser enorme. Você terá código (muito provavelmente) testado e também atualizado regularmente. Alguns exemplos de bibliotecas de validação de formulários para o Vue incluem:

* [vuelidate](https://github.com/monterail/vuelidate)
* [VeeValidate](http://vee-validate.logaretm.com/)
=======
---
title: Form Validation
type: cookbook
order: 3
---

## Base Example

Form validation is natively supported by the browser, but sometimes different browsers will handle things in a manner which makes relying on it a bit tricky. Even when validation is supported perfectly, there may be times when custom validations are needed and a more manual, Vue-based solution may be more appropriate. Let's begin with a simple example.

Given a form of three fields, make two required. Let's look at the HTML first:

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
>

  <p v-if="errors.length">
    <b>Please correct the following error(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    <label for="name">Name</label>
    <input
      id="name"
      v-model="name"
      type="text"
      name="name"
    >
  </p>

  <p>
    <label for="age">Age</label>
    <input
      id="age"
      v-model="age"
      type="number"
      name="age"
      min="0"
    >
  </p>

  <p>
    <label for="movie">Favorite Movie</label>
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
      value="Submit"
    >
  </p>

</form>
```

Let's cover it from the top. The `<form>` tag has an ID that we'll be using for the Vue component. There's a submit handler that you'll see in a bit, and the `action` is a temporary URL that would point to something real on a server someplace (where you have backup server-side validation of course).

Beneath that there is a paragraph that shows or hides itself based on an error state. This will render a simple list of errors on top of the form. Also note we fire the validation on submit rather than as every field is modified.

The final thing to note is that each of the three fields has a corresponding `v-model` to connect them to values we will work with in the JavaScript. Now let's look at that.

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
        this.errors.push('Name required.');
      }
      if (!this.age) {
        this.errors.push('Age required.');
      }

      e.preventDefault();
    }
  }
})
```

Fairly short and simple. We define an array to hold errors and set `null` values for the three form fields. The `checkForm` logic (which is run on submit remember) checks for name and age only as movie is optional. If they are empty we check each and set a specific error for each. And that's really it. You can run the demo below. Don't forget that on a successful submission it's going to POST to a temporary URL.

<p data-height="265" data-theme-id="0" data-slug-hash="GObpZM" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 1" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/GObpZM/">form validation 1</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Using Custom Validation

For the second example, the second text field (age) was switched to email which will be validated with a bit of custom logic. The code is taken from the StackOverflow question, [How to validate email address in JavaScript?](https://stackoverflow.com/questions/46155/how-to-validate-email-address-in-javascript). This is an awesome question because it makes your most intense Facebook political/religious argument look like a slight disagreement over who makes the best beer. Seriously - it's insane. Here is the HTML, even though it's really close to the first example.

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
  novalidate="true"
>

  <p v-if="errors.length">
    <b>Please correct the following error(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    <label for="name">Name</label>
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
    <label for="movie">Favorite Movie</label>
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
      value="Submit"
    >
  </p>

</form>
```

While the change here is small, note the `novalidate="true"` on top. This is important because the browser will attempt to validate the email address in the field when `type="email"`. Frankly it may make more sense to trust the browser in this case, but as we wanted an example with custom validation, we're disabling it. Here's the updated JavaScript.

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
        this.errors.push("Name required.");
      }
      if (!this.email) {
        this.errors.push('Email required.');
      } else if (!this.validEmail(this.email)) {
        this.errors.push('Valid email required.');
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

As you can see, we've added `validEmail` as a new method and it is simply called from `checkForm`. You can play with this example here:

<p data-height="265" data-theme-id="0" data-slug-hash="vWqNXZ" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 2" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/vWqNXZ/">form validation 2</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Another Example of Custom Validation

For the third example, we've built something you've probably seen in survey apps. The user is asked to spend a "budget" for a set of features for a new Star Destroyer model. The total must equal 100. First, the HTML.

``` html
<form
  id="app"
  @submit="checkForm"
  action="https://vuejs.org/"
  method="post"
  novalidate="true"
>

  <p v-if="errors.length">
    <b>Please correct the following error(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    Given a budget of 100 dollars, indicate how much
    you would spend on the following features for the
    next generation Star Destroyer. Your total must sum up to 100.
  </p>

  <p>
    <input
      v-model.number="weapons"
      type="number"
      name="weapons"
    > Weapons <br/>
    <input
      v-model.number="shields"
      type="number"
      name="shields"
    > Shields <br/>
    <input
      v-model.number="coffee"
      type="number"
      name="coffee"
    > Coffee <br/>
    <input
      v-model.number="ac"
      type="number"
      name="ac"
    > Air Conditioning <br/>
    <input
      v-model.number="mousedroids"
      type="number"
      name="mousedroids"
    > Mouse Droids <br/>
  </p>

  <p>
    Current Total: {{total}}
  </p>

  <p>
    <input
      type="submit"
      value="Submit"
    >
  </p>

</form>
```

Note the set of inputs covering the five different features. Note the addition of `.number` to the `v-model` attribute. This tells Vue to cast the value to a number when you use it. However, there is a bug with this feature such that when the value is blank, it turns back into a string. You'll see the workaround below. To make it a bit easier for the user, we also added a current total right below so they can see, in real time, what their total is. Now let's look at the JavaScript.

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
       // must parse because Vue turns empty value to string
       return Number(this.weapons) +
         Number(this.shields) +
         Number(this.coffee) +
         Number(this.ac+this.mousedroids);
     }
  },
  methods:{
    checkForm: function (e) {
      this.errors = [];

      if (this.total != 100) {
        this.errors.push('Total must be 100!');
      }

      if (!this.errors.length) {
        return true;
      }

      e.preventDefault();
    }
  }
})
```

We set up the total value as a computed value, and outside of that bug I ran into, it was simple enough to setup. My checkForm method now just needs to see if the total is 100 and that's it. You can play with this here:

<p data-height="265" data-theme-id="0" data-slug-hash="vWqGoy" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 3" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/vWqGoy/">form validation 3</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Server-side Validation

In my final example, we built something that makes use of Ajax to validate at the server. The form will ask you to name a new product and will then check to ensure that the name is unique. We wrote a quick [OpenWhisk](http://openwhisk.apache.org/) serverless action to do the validation. While it isn't terribly important, here is the logic:

``` js
function main(args) {
    return new Promise((resolve, reject) => {
        // bad product names: vista, empire, mbp
        const badNames = ['vista', 'empire', 'mbp'];

        if (badNames.includes(args.name)) {
          reject({error: 'Existing product'});
        }

        resolve({status: 'ok'});
    });
}
```

Basically any name but "vista", "empire", and "mbp" are acceptable. Ok, so let's look at the form.

``` html
<form
  id="app"
  @submit="checkForm"
  method="post"
>

  <p v-if="errors.length">
    <b>Please correct the following error(s):</b>
    <ul>
      <li v-for="error in errors">{{ error }}</li>
    </ul>
  </p>

  <p>
    <label for="name">New Product Name: </label>
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
      value="Submit"
    >
  </p>

</form>
```

There isn't anything special here. So let's go on to the JavaScript.

``` js
const apiUrl = 'https://openwhisk.ng.bluemix.net/api/v1/web/rcamden%40us.ibm.com_My%20Space/safeToDelete/productName.json?name=';

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
        this.errors.push('Product name is required.');
      } else {
        fetch(apiUrl + encodeURIComponent(this.name))
        .then(res => res.json())
        .then(res => {
          if (res.error) {
            this.errors.push(res.error);
          } else {
            // redirect to a new URL, or do something on success
            alert('ok!');
          }
        });
      }
    }
  }
})
```

We start off with a variable representing the URL of the API that is running on OpenWhisk. Now look at `checkForm`. In this version, we always prevent the form from submitting (which, by the way, could be done in the HTML with Vue as well). You can see a basic check on `this.name` being empty, and then we hit the API. If it's bad, we add an error as before. If it's good, right now we do nothing (just an alert), but you could navigate the user to a new page with the product name in the URL, or do other actions as well. You can run this demo below:

<p data-height="265" data-theme-id="0" data-slug-hash="BmgzeM" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 4" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/BmgzeM/">form validation 4</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Alternative Patterns

While this cookbook entry focused on doing form validation "by hand", there are, of course, some great Vue libraries that will handle a lot of this for you. Switching to a prepackage library may impact the final size of your application, but the benefits could be tremendous. You have code that is (most likely) heavily tested and also updated on a regular basis. Some examples of form validation libraries for Vue include:

* [vuelidate](https://github.com/monterail/vuelidate)
* [VeeValidate](http://vee-validate.logaretm.com/)
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
