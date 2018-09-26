---
title: Validação de Formulário
type: cookbook
order: 3
---

## Exemplo Base

Validação de formulário é suportado nativamente pelo navegador, porém navegadores diferentes irão lidar com as coisas de um jeito que seja um pouco complicada. Mesmo quando uma validação é suportada perfeitamente, haverá algumas vezes a necessidade de se personalizar a validação e uma solução baseada em Vue poderá ser mais apropriada. Vamos começar com um simples exemplo.

Dado um formulário com três campos, faça dois obrigatórios. Vamos ver o HTML primeiro

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
      min="0">
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
      value="Submit"
    >
  </p>

</form>
```

Vamos avaliar do início. A tag `<form>` tem um ID que nos usaremos para o componente Vue. Há um botão de submit que você verá em um momento, e o `action` é uma URL temporaria que aponta para algo real em algum lugar (onde haverá um backup do lado do servidor, claro).

Abaixo disso, há um parágrafo que irá mostrar ou esconder por si mesmo baseando-se em um estado do erro. isso fará renderizar uma lista de erros no topo do formulário. Além disso, note que colocamos uma validação que será disparada no envio <<<<<>>>>>

A última coisa a se notar é que cada um dos três campos tem um `v-model` correspondente para conecta-lo com um valores que iremos trabalhar com JavaScript. Agora, vamos o exemplo.

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
        this.errors.push('Necessário o nome.');
      }
      if (!this.age) {
        this.errors.push('Necessário a idade.');
      }

      e.preventDefault();
    }
  }
})
```

Fairly short and simple. We define an array to hold errors and set `null` values for the three form fields. The `checkForm` logic (which is run on submit remember) checks for name and age only as movie is optional. If they are empty we check each and set a specific error for each. And that's really it. You can run the demo below. Don't forget that on a successful submission it's going to POST to a temporary URL. 

Bastante curto e simples. Definimos um array para manter erros e fixar valores nulos para os três campos do formulário. A lógica do `checkForm` (lembrando que é executado no envio) valida apenas name e age embora movie seja opcional. Caso esteja vazio, checamos cada um e definimos um erro específico para cada um. E é apenas isso. Você pode testar abaixo. Não se esqueça que em um envio bem-sucedido, isso será _COLOCADO_ em um URL temporária.

<p data-height="265" data-theme-id="0" data-slug-hash="GObpZM" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 1" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/GObpZM/">form validation 1</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Usando Validação personalizada

Para o segundo exemplo, o segundo campo de texto (age), estava alterado para email que será validado com um pequena lógica personalizada. O código é retirado de uma pergunta do StackOverflow, [How to validate email address in JavaScript?](https://stackoverflow.com/questions/46155/how-to-validate-email-address-in-javascript). Essa é uma ótima questão pois faz que que seu argumento político / religioso mais intenso no Facebook pareça um delicado desentendimento sobre quem faz a melhor cerveja. 

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
      value="Submit"
    >
  </p>

</form>
```

While the change here is small, note the `novalidate="true"` on top. This is important because the browser will attempt to validate the email address in the field when `type="email"`. Frankly it may make more sense to trust the browser in this case, but as we wanted an example with custom validation, we're disabling it. Here's the updated JavaScript.

Embora a mudança seja pequena, perceba o `novalidate="true"` no início. Isso é importante pois o navegador irá se atentar para a validação do endereço de email quando o campo `type="email"`. Francamente, fará mais sentido confiar no navegador nesse caso, mas como queremos um exemplo com personalização customizada, iremos desabilitá-la. Aqui está o JavaScript atualizado. 

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
        this.errors.push("Necessário o nome.");
      }
      if (!this.email) {
        this.errors.push('Necessário o email.');
      } else if (!this.validEmail(this.email)) {
        this.errors.push('Preencha com um email valido');
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

Como pode-se ver, nos adicionamos `validEmail` como um novo mêtodo, e uma simples chamada através do `checkForm`. Você pode testar esse exemplo aqui:

<p data-height="265" data-theme-id="0" data-slug-hash="vWqNXZ" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 2" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/vWqNXZ/">form validation 2</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Outro Exemplo de Validação Personalizada

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
