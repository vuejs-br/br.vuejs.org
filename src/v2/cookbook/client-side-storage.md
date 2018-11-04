---
title: Armazenamento no Lado do Cliente
type: cookbook
order: 11
---

<!-- <p class="tip">**Nota da Equipe de Tradução**
Este arquivo ainda não foi traduzido! Leia a versão original em inglês a seguir e, se puder, colabore com sua tradução: acesse [nosso projeto no GitHub](https://github.com/vuejs-br/br.vuejs.org/issues), avise que irá contribuir e inicie a tradução. Sua participação é muito importante!</p> -->

## Exemplo Base

Armazenamento do lado do cliente é um excelente forma de adicionar rapidamente ganhos de performance na aplicação. Por armazenar dados no próprio browser, você pode pular a busca de informações no servidor toda vez que o usuário precisar. Embora seja especialmente útil quando se estiver offline, até mesmo usuários online se beneficiarão do uso de dados locais em comparação a um servidor remoto. Armazenamento do lado do cliente pode ser feito com [Cookies](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies), [Armazenamento Local](https://developer.mozilla.org/pt-BR/docs/Web/API/Web_Storage_API_pt_br) (Tecnicamente "Web Storage"), [IndexedDB](https://developer.mozilla.org/pt-BR/docs/IndexedDB), and [WebSQL](https://www.w3.org/TR/webdatabase/) (a deprecated method that should not be used in new projects).

Neste livro de receitas vamos focar no Armazenamento Local, o mais simples dos mecanismos de armazenamento. Armazenamento local usa um sistema de chave/valor para armazenar dados. Ele é limitado a armazenar apenas valores simples, mas dados complexos podem ser armazenados se você estiver disposto a codificar e decodificar os valores com JSON. Em geral, Armazenamento Local é apropriado para pequenos conjuntos de dados que você deseja manter, coisaas como preferências de usuário ou dados de formulário. Dados maiores, com necessidades mais complexas de armazenamento, seriam normalmente melhor armazenados no IndexedDB.

Vamos começar com um exemplo de formulário simples:

``` html
<div id="app">
  Meu nome é <input v-model="name">
</div>
```

Este exemplo tem um campo de formulário ligado a um valor do Vue chamado `name`. Aqui está o JavaScript:

``` js
const app = new Vue({
  el: '#app',
  data: {
    name: ''
  },
  mounted() {
    if (localStorage.name) {
      this.name = localStorage.name;
    }
  },
  watch: {
    name(newName) {
      localStorage.name = newName;
    }
  }
});
```

Foque nas partes `mounted` e `watch`. Nós usamos `mounted` para manipular o carregamento dos valores do localStorage. Para lidar com a escrita da base de dados, observamos o valor do `name` e quando alterado imediatamente o escrevemos.

Você pode executar isso por conta própria aqui:

<p data-height="265" data-theme-id="0" data-slug-hash="KodaKb" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="testing localstorage" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/KodaKb/">testing localstorage</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

<!-- Continuar daqui -->

Type something in the form and then reload this page. You'll note that the value you typed previously will show up automatically. Don't forget that your browser provides excellent developer tools for inspecting client-side storage. Here's an example in Firefox:

![Storage devtools in Firefox](/images/devtools-storage.png)

And here it is in Chrome:

![Storage devtools in Chrome](/images/devtools-storage-chrome.png)

And then finally, an example in Microsoft Edge. Note that you can find application storage values under the Debugger tab.

![Storage devtools in Edge](/images/devtools-storage-edge.png)

<p class="tip">As a quick aside, these dev tools also offer you a way to remove storage values. This can be very useful when testing.</p>

Immediately writing the value may not advisable. Let's consider a slightly more advanced example. First, the updated form.

``` html
<div id="app">
  <p>
    My name is <input v-model="name">
    and I am <input v-model="age"> years old.
  </p>
  <p>
    <button @click="persist">Save</button>
  </p>
</div>
```

Now we've got two fields (again, bound to a Vue instance) but now there is the addition of a button that runs a `persist` method. Let's look at the JavaScript.

``` js
const app = new Vue({
  el: '#app',
  data: {
    name: '',
    age: 0
  },
  mounted() {
    if (localStorage.name) {
      this.name = localStorage.name;
    }
    if (localStorage.age) {
      this.age = localStorage.age;
    }
  },
  methods: {
    persist() {
      localStorage.name = this.name;
      localStorage.age = this.age;
      console.log('now pretend I did more stuff...');
    }
  }
})
```

As before, `mounted` is used to load persisted data, if it exists. This time, though, data is only persisted when the button is clicked. We could also do any validations or transformations here before storing the value. You could also store a date representing when the values were stored. With that metadata, the `mounted` method could make a logical call on whether or not to store the values again. You can try this version below.

<p data-height="265" data-theme-id="0" data-slug-hash="rdOjLN" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="testing localstorage 2" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/rdOjLN/">testing localstorage 2</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Working with Complex Values

As mentioned above, Local Storage only works with simple values. To store more complex values, like objects or arrays, you must serialize and deserialize the values with JSON. Here is a more advanced example that persists an array of cats (the best kind of array possible).

``` html
<div id="app">
  <h2>Cats</h2>
  <div v-for="(cat, n) in cats">
    <p>
      <span class="cat">{{ cat }}</span>
      <button @click="removeCat(n)">Remove</button>
    </p>
  </div>

  <p>
    <input v-model="newCat">
    <button @click="addCat">Add Cat</button>
  </p>
</div>
```

This "app" consists of a simple list on top (with a button to remove a cat) and a small form at the bottom to add a new cat. Now let's look at the JavaScript.

``` js
const app = new Vue({
  el: '#app',
  data: {
    cats: [],
    newCat: null
  },
  mounted() {
    if (localStorage.getItem('cats')) {
      try {
        this.cats = JSON.parse(localStorage.getItem('cats'));
      } catch(e) {
        localStorage.removeItem('cats');
      }
    }
  },
  methods: {
    addCat() {
      // ensure they actually typed something
      if (!this.newCat) {
        return;
      }

      this.cats.push(this.newCat);
      this.newCat = '';
      this.saveCats();
    },
    removeCat(x) {
      this.cats.splice(x, 1);
      this.saveCats();
    },
    saveCats() {
      const parsed = JSON.stringify(this.cats);
      localStorage.setItem('cats', parsed);
    }
  }
})
```

In this application, we've switched to use the Local Storage APIs versus "direct" access. Both work but the API method is generally preferred. `mounted` now has to grab the value and parse the JSON value. If anything goes wrong here we assume the data is corrupt and delete it. (Remember, any time your web application uses client-side storage, the user has access to it and can modify it at will.)

We have three methods now to handle working with cat. Both `addCat` and `removeCat` handle updating the "live" Vue data stored in `this.cats`. They then run `saveCats` which handles serializing and persisting the data. You can play with this version below:

<p data-height="265" data-theme-id="0" data-slug-hash="qoYbyW" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="localstorage, complex" class="codepen">See the Pen <a href="https://codepen.io/cfjedimaster/pen/qoYbyW/">localstorage, complex</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Alternative Patterns

While the Local Storage API is relatively simple, it is missing some basic features that would be useful in many applications. The following plugins wrap Local Storage access and make it easier to use, while also adding functionality like default values.

* [vue-local-storage](https://github.com/pinguinjkeke/vue-local-storage)
* [vue-reactive-storage](https://github.com/ropbla9/vue-reactive-storage)
* [vue2-storage](https://github.com/yarkovaleksei/vue2-storage)

## Wrapping Up

While the browser will never replace a server persistence system, having multiple ways to cache data locally can be a huge performance boost for your application, and working with it in Vue.js makes it even more powerful.
