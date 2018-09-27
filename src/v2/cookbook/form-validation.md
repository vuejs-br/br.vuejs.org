---
title: Validação de Formulários
type: cookbook
order: 3
---

## Exemplo Base

Validação de formulários é algo suportado nativamente pelo navegadore. Porém, navegadores diferentes irão lidar com as coisas de um jeito que pode ser um pouco complicado. Mesmo quando uma validação é suportada perfeitamente, haverá, algumas vezes, a necessidade de se personalizar e, portanto, uma solução baseada em Vue poderá ser mais apropriada. Vamos começar com um exemplo simples.

Dado um formulário com três campos, faça dois obrigatórios. Vamos ver o HTML primeiro:

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

Vamos avaliar do início. A tag `<form>` tem um ID que nós usaremos para o componente Vue. Há um botão de envio que você verá em um momento, e o `action` é uma URL temporária que aponta para algo real em algum lugar (onde haverá um backup do lado do servidor, claro).

Abaixo disso, há um parágrafo que irá mostrar ou esconder por si mesmo baseando-se em um estado do erro. isso fará renderizar uma lista de erros no topo do formulário. Além disso, note que colocamos uma validação que será disparada no envio a cada vez que o campo for modificado

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

Bastante curto e simples. Definimos um Array para manter erros e fixar valores nulos para os três campos do formulário. A lógica do `checkForm` (lembrando que é executado no envio) valida apenas nome e idade, embora filme seja opcional. Caso esteja vazio, checamos cada um e definimos um erro específico para cada um. E é apenas isso. Você pode testar abaixo. Não se esqueça que em um envio bem-sucedido, isso será _COLOCADO_ em um URL temporária.

<p data-height="265" data-theme-id="0" data-slug-hash="GObpZM" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 1" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/GObpZM/">form validation 1</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Usando Validação Personalizada

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

Como pode-se ver, nos adicionamos `validEmail` como um novo método, e uma simples chamada através do `checkForm`. Você pode testar esse exemplo aqui:

<p data-height="265" data-theme-id="0" data-slug-hash="vWqNXZ" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 2" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/vWqNXZ/">form validation 2</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Outro Exemplo de Validação Personalizada

Para o terceiro exemplo, construiremos algo que provavelmente você já viu em questionários de aplicativos. O usuário é questionado a gastar o “orçamento” para um conjunto de recursos para um novo modelo de Estrela da morte. O total deve ser igual a 100. Veja primeiro o HTML.

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
    Dado um orçamento de 100 dolares, indique 
    quanto você gastaria nos seguintes recursos
    para a próxima geração da estrela da morte. 
    Seu total deve totalizar 100 
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
    > Droids rato <br/>
  </p>

  <p>
    Total atual: {{total}}
  </p>

  <p>
    <input
      type="submit"
      value="Submit"
    >
  </p>

</form>
```

Note que os conjuntos de entrada cobrem os cinco diferentes recursos. Perceba a adição do `.number` para o atributo `v-model`. Isso diz ao Vue para transformar o valor para um número quando você usá-lo. No entanto existe um erro com esse recurso, semelhante quando o valor é vazio, que o transforma para string. Você verá a solução abaixo. Para tornar isso um pouco mais fácil para o usuário, adicionamos um total próximo a direita para eles verem em tempo real, qual é o seu total. Agora vamos ver o JavaScript.

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
       // Deve-se analisar pois o Vue transforma em String valores vazios 
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
        this.errors.push('Total deve ser 100!');
      }

      if (!this.errors.length) {
        return true;
      }

      e.preventDefault();
    }
  }
})
```

Definimos o valor total como um valor calculado, e fora daquele erro que eu rodei dentro, e isso foi simples o suficiente de se configurar. Meu método checkForm, agora, apenas precisa ver, se o total é 100 e apenas isso. Você pode testar isso aqui:

<p data-height="265" data-theme-id="0" data-slug-hash="vWqGoy" data-default-tab="html,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 3" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/vWqGoy/">form validation 3</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Validação no Lado do Servidor

No meu último exemplo, construiremos algo que faz uso do Ajax para validar no servidor. O formulário irá pedir para nomear um novo produto então irá checar para ter certeza, que o nome é único. Escrevemos uma rápida ação no *serverless* [OpenWhisk](http://openwhisk.apache.org/) para fazer a validação. Embora não seja muito importante, aqui está a lógica:

``` js
function main(args) {
    return new Promise((resolve, reject) => {
        // Nome de produtos ruins: vista, empire, mbp
        const badNames = ['vista', 'empire', 'mbp'];

        if (badNames.includes(args.name)) {
          reject({error: 'Produto existente'});
        }

        resolve({status: 'ok'});
    });
}
```

Basicamente qualquer nome mas "vista", "empire", e"mbp" são aceitáveis. Vamos continuar olhando o formulário.

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
      value="Submit"
    >
  </p>

</form>
```

Não há nada especial aqui. Então vamos para o JavaScript.

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
        this.errors.push('Nome do produto é requisitado.');
      } else {
        fetch(apiUrl + encodeURIComponent(this.name))
        .then(res => res.json())
        .then(res => {
          if (res.error) {
            this.errors.push(res.error);
          } else {
            // redirecione para uma nova URL ou faça algo em caso de sucesso
            alert('ok!');
          }
        });
      }
    }
  }
})
```

Começamos com uma variável representando a URL da API que está rodando no OpenWhisk. Agora veja o `checkForm`. Nessa versão, nós sempre impedimos que o formulário envie (o que, a propósito, pode ser feito no HTML com Vue também). Você pode ver uma verificação básica no `this.name` vazia, e então nos disparamos a API. Se algo der errado, nós adicionamos um erro como antes. Se estiver certo, até o momento não fazemos nada (apenas um alerta), mas você pode encaminhar o usuário para uma nova página com o nome do produto na URL, ou fazer outras ações por exemplo. Você pode testar esse exemplo abaixo.


<p data-height="265" data-theme-id="0" data-slug-hash="BmgzeM" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="form validation 4" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/BmgzeM/">form validation 4</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

## Padrões Alternativos

Embora esse verbete do livro de receitas é focado em fazer um formulário de validação "à mão", existe, é claro, algumas ótimas bibliotecas Vue que vão lidar com muitas dessas coisas para você. Trocando para uma biblioteca pré-empacotada, pode-se impactar o tamanho final da sua aplicação, mas o benefício pode ser tremendo. Você terá código que é (muito provavelmente) testado e também atualizado regularmente. Alguns exemplos de bibliotecas de validação de formulários para o Vue incluem:

* [vuelidate](https://github.com/monterail/vuelidate)
* [VeeValidate](http://vee-validate.logaretm.com/)
