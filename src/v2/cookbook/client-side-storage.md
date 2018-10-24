---
title: Armazenamento no Lado do Cliente
type: cookbook
order: 11
---

## Exemplo Base

O armazenamento no lado do cliente é uma excelente maneira de adicionar rapidamente ganhos de desempenho a um aplicativo. Ao armazenar dados no próprio navegador, você pode ignorar a busca de informações do servidor toda vez que o usuário precisar. Embora seja especialmente útil quando estiver off-line, até os usuários on-line se beneficiarão do uso de dados localmente em comparação a um servidor remoto. O armazenamento no lado do cliente pode ser feito com [cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies), [Armazenamento local](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) (tecnicamente "Armazenamento na Web"), [IndexedDB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) e [WebSQL ](https://www.w3.org/TR/webdatabase/) (um método obsoleto que não deve ser usado em novos projetos).

Nesta parte do livro de receitas, vamos nos concentrar no armazenamento local, o mais simples dos mecanismos de armazenamento. O armazenamento local usa um sistema de chave/valor para armazenar dados. Ele é limitado a armazenar apenas valores simples, mas dados complexos podem ser armazenados se você estiver disposto a codificar e decodificar os valores com JSON. Em geral, o armazenamento local é apropriado para conjuntos menores de dados que você deseja manter, como preferências do usuário ou dados de formulário. Dados maiores com necessidades de armazenamento mais complexas seriam melhor armazenados normalmente no IndexedDB.

Vamos começar com um exemplo simples baseado em formulário:

``` html
<div id="app">
  My name is <input v-model="name">
</div>
```

Este exemplo tem um campo de formulário vinculado a um valor Vue chamado `name`. Aqui está o JavaScript:

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

Concentre-se nas partes `mounted` e `watch`. Usamos `mounted` para manipular o carregamento do valor do localStorage. Para lidar com a escrita da base de dados, observamos o valor do `name` e da mudança, escrevemos imediatamente.

Você pode executar isso sozinho aqui:

<p data-height="265" data-theme-id="0" data-slug-hash="KodaKb" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="testing localstorage" class="codepen">Veja o Pen <a href="https://codepen.io/cfjedimaster/pen/KodaKb/">testando o Armazenamento Local</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>


Digite algo no formulário e, em seguida, recarregue esta página. Você notará que o valor digitado anteriormente será exibido automaticamente. Não se esqueça de que seu navegador oferece excelentes ferramentas de desenvolvedor para inspecionar o armazenamento no lado do cliente. Aqui está um exemplo no

Firefox:

![Storage devtools in Firefox](/images/devtools-storage.png)

E aqui está no Chrome:

![Storage devtools in Chrome](/images/devtools-storage-chrome.png)

E finalmente, um exemplo no Microsoft Edge. Observe que você pode encontrar valores de armazenamento de aplicativos na guia Debugger.

![Storage devtools in Edge](/images/devtools-storage-edge.png)

<p class="tip"> Deixando isso de lado, essas ferramentas de desenvolvimento também oferecem uma maneira de remover os valores de armazenamento. Isso pode ser muito útil ao testar.</p>

Escrever imediatamente o valor pode não ser aconselhável. Vamos considerar um exemplo um pouco mais avançado. Primeiro, o formulário atualizado.

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

Agora temos dois campos (novamente, ligados a uma instância Vue), mas agora há a adição de um botão que executa um método `persist`. Vamos ver o JavaScript.

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

Como antes, `mounted` é usado para carregar dados persistentes, se existirem. Desta vez, porém, os dados só são mantidos quando o botão é clicado. Também poderíamos fazer validações ou transformações antes de armazenar o valor. Você também pode armazenar uma data representando quando os valores foram armazenados. Com esses metadados, o método `mounted` poderia fazer uma chamada lógica para armazenar ou não os valores novamente. Você pode tentar esta versão abaixo.

<p data-height="265" data-theme-id="0" data-slug-hash="rdOjLN" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="testing localstorage 2" class="codepen">Veja  o Pen <a href="https://codepen.io/cfjedimaster/pen/rdOjLN/">testando o Armazenamento Local 2</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Trabalhando com Valores Complexos

Como mencionado acima, o armazenamento local só funciona com valores simples. Para armazenar valores mais complexos, como objetos ou matrizes, você deve serializar e desserializar os valores com JSON. Aqui está um exemplo mais avançado que persiste uma matriz de gatos (o melhor tipo de array possível).

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

Este "aplicativo" consiste em uma lista simples na parte superior (com um botão para remover um gato) e um pequeno formulário na parte inferior para adicionar um novo gato. Agora vamos ver o JavaScript.

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

Neste aplicativo, mudamos para usar as APIs de armazenamento local em vez do acesso "direto". Ambos funcionam, mas o método API é geralmente preferido. `mounted` agora tem que pegar o valor e analisar o valor JSON. Se algo der errado aqui, assumimos que os dados estão corrompidos e os excluímos. (Lembre-se, sempre que seu aplicativo da Web usa o armazenamento do lado do cliente, o usuário tem acesso a ele e pode modificá-lo à vontade.)

Nós temos três métodos agora para lidar com o trabalho com gatos. Ambos `addCat` e `removeCat` tratam de atualizar os dados "vivos" de Vue armazenados em `this.cats`. Em seguida, eles executam o `saveCats`, que manipula a serialização e a persistência dos dados. Você pode brincar com esta versão abaixo:

<p data-height="265" data-theme-id="0" data-slug-hash="qoYbyW" data-default-tab="js,result" data-user="cfjedimaster" data-embed-version="2" data-pen-title="localstorage, complex" class="codepen">Veja o Pen <a href="https://codepen.io/cfjedimaster/pen/qoYbyW/">Armazenamento Local, Complexo</a> by Raymond Camden (<a href="https://codepen.io/cfjedimaster">@cfjedimaster</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Padrões Alternativos

Embora a API de armazenamento local seja relativamente simples, falta alguns recursos básicos que seriam úteis em muitos aplicativos. Os seguintes plug-ins envolvem o acesso ao Armazenamento local e facilitam o uso,além de adicionar funcionalidades como valores padrão.

* [vue-local-storage](https://github.com/pinguinjkeke/vue-local-storage)
* [vue-reactive-storage](https://github.com/ropbla9/vue-reactive-storage)
* [vue2-storage](https://github.com/yarkovaleksei/vue2-storage)

## Concluindo

Embora o navegador nunca substitua um sistema de persistência de servidor, ter várias maneiras de armazenar dados em cache localmente pode ser um grande aumento de desempenho para seu aplicativo, e trabalhar com ele no Vue.js o torna ainda mais poderoso.
