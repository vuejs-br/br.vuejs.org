---
title: Armazenamento no Lado do Cliente
type: cookbook
order: 11
---

## Exemplo Base

Armazenamento no lado do cliente é uma excelente forma de adicionar melhorias de desempenho na aplicação. Por armazenar dados no próprio navegador, você pode pular a busca de informações no servidor toda vez que o usuário precisar. Embora seja especialmente útil quando se está _offline_, usuários _online_ também se beneficiarão do uso de dados locais em comparação a um servidor remoto. Armazenamento no cliente pode ser feito com [Cookies](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies), [Armazenamento Local](https://developer.mozilla.org/pt-BR/docs/Web/API/Web_Storage_API_pt_br) (tecnicamente, _Web Storage_), [IndexedDB](https://developer.mozilla.org/pt-BR/docs/IndexedDB) e [WebSQL](https://www.w3.org/TR/webdatabase/) (um método obsoleto que não deve ser usado em novos projetos).

Neste capítulo do livro de receitas, vamos focar no Armazenamento Local, o mais simples dos mecanismos de armazenamento. Este armazenamento usa um sistema de chave/valor para organizar os dados. Apesar de ser limitado a armazenar apenas valores simples, dados complexos podem ser armazenados se você estiver disposto a codificar e decodificar os valores com JSON. Em geral, é apropriado para pequenos conjuntos de dados que você deseja manter, coisas como preferências de usuário ou dados de um formulário. Dados maiores, ou com necessidades mais complexas de armazenamento, seriam normalmente melhor armazenados com IndexedDB.

Vamos começar com um exemplo de formulário simples:

``` html
<div id="app">
  Meu nome é: <input v-model="name">
</div>
```

Este exemplo tem um campo ligado a um dado chamado `name`. Aqui está o JavaScript:

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
})
```

Foque no `mounted` e no `watch`. Nós usamos `mounted` para manipular o carregamento dos valores armazenados no `localStorage`. Para lidar com a escrita dos dados, observamos o valor de `name` e, quando alterado, imediatamente o escrevemos no armazenamento local.

Você pode executar isso por conta própria aqui:

<p data-height="265" data-theme-id="0" data-slug-hash="LXeNgK" data-default-tab="js,result" data-user="ErickPetru" data-pen-title="Testando localStorage com Vue" class="codepen">Veja o Pen <a href="https://codepen.io/ErickPetru/pen/LXeNgK/">Testando localStorage com Vue</a> por Erick Eduardo Petrucelli (<a href="https://codepen.io/ErickPetru">@ErickPetru</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Digite algo no formulário e recarregue esta página. Você observará que o valor digitado anteriormente aparecerá automaticamente. Não esqueça que seu navegador fornece ferramentas de desenvolvedor excelentes para inspecionar o armazenamento local. Aqui está um exemplo no Firefox:

![Storage devtools no Firefox](/images/devtools-storage.png)

E aqui no Chrome:

![Storage devtools no Chrome](/images/devtools-storage-chrome.png)

E aqui, finalmente, um exemplo no Microsoft Edge. Observe que você pode encontrar valores de armazenamento de aplicativos na guia Debugger.

![Storage devtools no Edge](/images/devtools-storage-edge.png)

<p class="tip">Como um rápido parênteses, tais ferramentas de desenvolvimento também oferecem uma maneira de remover os valores armazenados. Isto pode ser muito útil durante seus testes.</p>

Gravar imediatamente o valor a cada modificação pode não ser aconselhável. Vamos considerar um exemplo um pouco mais avançado. Primeiro, o formulário atualizado.

``` html
<div id="app">
  <p>
    Meu nome é <input v-model="name">
    e tenho <input v-model="age"> anos de idade.
  </p>
  <p>
    <button @click="persist">Salvar</button>
  </p>
</div>
```

Temos dois campos (novamente ligados à instância Vue), mas agora há a adição de um botão que executa um método `persist`. Vamos ver o JavaScript:

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
      console.log('Agora finja que fiz mais coisas...');
    }
  }
})
```

Como antes, `mounted` é usado para carregar os dados persistidos, se existirem. Desta vez, porém, os dados serão mantidos somente quando o botão for clicado. Também poderíamos fazer quaisquer validações ou transformações antes de armazenar os valores. Poderia até mesmo armazenar uma data representando quando os valores foram armazenados e, no método `mounted`, utilizar uma condição lógica para armazenar ou não os valores novamente. Você pode testar abaixo a versão que acabamos de apresentar.

<p data-height="265" data-theme-id="0" data-slug-hash="NEXrgM" data-default-tab="js,result" data-user="ErickPetru" data-pen-title="Testando localStorage com Vue 2" class="codepen">Veja o Pen <a href="https://codepen.io/ErickPetru/pen/NEXrgM/">Testando localStorage com Vue 2</a> por Erick Eduardo Petrucelli (<a href="https://codepen.io/ErickPetru">@ErickPetru</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Trabalhando com Valores Complexos

Conforme mencionado antes, armazenamento local só funciona com valores simples. Para armazenar valores complexos, como objetos ou Arrays, você deve serializar e desserializar os valores usando JSON. Aqui está um exemplo mais avançado que persiste um Array de gatos (o melhor tipo de Array possível).

``` html
<div id="app">
  <h2>Gatos</h2>
  <div v-for="(cat, n) in cats">
    <p>
      <span class="cat">{{ cat }}</span>
      <button @click="removeCat(n)">Remover</button>
    </p>
  </div>

  <p>
    <input v-model="newCat">
    <button @click="addCat">Adicionar</button>
  </p>
</div>
```

Este exemplo consiste em uma lista simples na parte superior (com um botão para remover um gato, em cada item) e um pequeno formulário na parte inferior, para adicionar um novo gato. Agora vamos ver o JavaScript.

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
      // Garanta que algo foi digitado
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

Neste caso, mudamos a solução para usar as APIs de armazenamento local em vez do acesso "direto". Ambos funcionam, mas usar a API é geralmente o método preferido. Agora, o `mounted` tem que pegar o valor e desserializar o JSON. Se algo der errado aqui, assumimos que os dados estão corrompidos e os excluímos (lembre-se, sempre que seu aplicativo Web usa o armazenamento do lado do cliente, o usuário tem acesso a ele e pode modificá-lo à vontade).

Acrescentamos também três métodos para lidar com gatos. Ambos `addCat` e `removeCat` tratam de atualizar os dados Vue "vivos" armazenados em `this.cats`. Em seguida, eles executam o método `saveCats`, que manipula a serialização e a persistência dos dados. Você pode testar esta versão abaixo:

<p data-height="265" data-theme-id="0" data-slug-hash="jQYMqj" data-default-tab="js,result" data-user="ErickPetru" data-pen-title="Testando localStorage com Vue 3" class="codepen">Veja o Pen <a href="https://codepen.io/ErickPetru/pen/jQYMqj/">Testando localStorage com Vue 3</a> por Erick Eduardo Petrucelli (<a href="https://codepen.io/ErickPetru">@ErickPetru</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Padrões Alternativos

Embora a API de armazenamento local seja relativamente simples, carece de alguns recursos básicos que seriam úteis em muitos aplicativos. Os seguintes _plugins_ Vue englobam o acesso ao armazenamento local e facilitam o seu uso, além de adicionar funcionalidades como valores padrão.

* [vue-local-storage](https://github.com/pinguinjkeke/vue-local-storage)
* [vue-reactive-storage](https://github.com/ropbla9/vue-reactive-storage)
* [vue2-storage](https://github.com/yarkovaleksei/vue2-storage)

## Considerações Finais

Embora o navegador nunca possa substituir um sistema de persistência de dados no servidor, ter várias maneiras de armazenar dados em _cache_ local pode trazer um grande benefício de desempenho para sua aplicação, e trabalhar com isto no Vue.js o torna ainda mais poderoso.
