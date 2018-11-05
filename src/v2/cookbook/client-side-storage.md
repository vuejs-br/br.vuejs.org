---
title: Armazenamento no Lado do Cliente
type: cookbook
order: 11
---

<!-- <p class="tip">**Nota da Equipe de Tradução**
Este arquivo ainda não foi traduzido! Leia a versão original em inglês a seguir e, se puder, colabore com sua tradução: acesse [nosso projeto no GitHub](https://github.com/vuejs-br/br.vuejs.org/issues), avise que irá contribuir e inicie a tradução. Sua participação é muito importante!</p> -->

## Exemplo Base

Armazenamento no lado do cliente é um excelente forma de adicionar rapidamente ganhos de performance na aplicação. Por armazenar dados no próprio browser, você pode pular a busca de informações no servidor toda vez que o usuário precisar. Embora seja especialmente útil quando se estiver offline, até mesmo usuários online se beneficiarão do uso de dados locais em comparação a um servidor remoto. Armazenamento do lado do cliente pode ser feito com [Cookies](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Cookies), [Armazenamento Local](https://developer.mozilla.org/pt-BR/docs/Web/API/Web_Storage_API_pt_br) (Tecnicamente "Web Storage"), [IndexedDB](https://developer.mozilla.org/pt-BR/docs/IndexedDB), and [WebSQL](https://www.w3.org/TR/webdatabase/) (a deprecated method that should not be used in new projects).

Neste livro de receitas vamos focar no Armazenamento Local, o mais simples dos mecanismos de armazenamento. Armazenamento local usa um sistema de chave/valor para armazenar dados. Ele é limitado a armazenar apenas valores simples, mas dados complexos podem ser armazenados se você estiver disposto a codificar e decodificar os valores com JSON. Em geral, Armazenamento Local é apropriado para pequenos conjuntos de dados que você deseja manter, coisaas como preferências de usuário ou dados de formulário. Dados maiores, com necessidades mais complexas de armazenamento, seriam normalmente melhor armazenados no IndexedDB.

Vamos começar com um exemplo de formulário simples:

``` html
<div id="app">
  Meu nome é: <input v-model="nome">
</div>
```

Este exemplo tem um campo de formulário ligado a um valor do Vue chamado `nome`. Aqui está o JavaScript:

``` js
const app = new Vue({
  el:'#app',
  data:{
    nome:''
  },
  mounted() {
    if(localStorage.nome) this.nome = localStorage.nome;
  },
  watch:{
    nome(novoNome) {
      localStorage.nome = novoNome;
    }
  }
})
```

Foque nas partes `mounted` e `watch`. Nós usamos `mounted` para manipular o carregamento dos valores do localStorage. Para lidar com a escrita da base de dados, observamos o valor de `nome` e, quando alterado, imediatamente o escrevemos.

Você pode executar isso por conta própria aqui:

<p data-height="265" data-theme-id="0" data-slug-hash="vQOGmW" data-default-tab="js,result" data-user="brunohgv" data-pen-title="testando localstorage" class="codepen">See the Pen <a href="https://codepen.io/brunohgv/pen/vQOGmW/">testando localstorage</a> by Bruno Henrique Gusmão Vasconcelos (<a href="https://codepen.io/brunohgv">@brunohgv</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Digite algo no formulário e recarregue essa página. Você notará que o valor que você digitou anteriormente aparecerá automaticamente. Não esqueça que seu navegador fornece ferramentas de desenvolvedor excelentes para inspecionar o armazenamento do lado do cliente. Aqui está um exemplo no Firefox: 

![Storage devtools no Firefox](/images/devtools-storage.png)

E aqui no Chrome:

![Storage devtools no Chrome](/images/devtools-storage-chrome.png)

E aqui, finalmente, um exemplo no Microsoft Edge. Observe que você pode encontrar valores de armazenamento de aplicativos na guia Debugger.

![Storage devtools no Edge](/images/devtools-storage-edge.png)

<p class="tip">Deixando isso de lado, essas ferramentas de desenvolvimento também oferecem uma maneira de remover os valores de armazenamento. Isso pode ser muito útil durante os testes.</p>

Escrever imediatamente o valor pode não ser aconselhável. Vamos considerar um exemplo um pouco mais avançado. Primeiro, o formulário atualizado.

``` html
<div id="app">
  Meu nome é <input v-model="nome">
  e eu tenho <input v-model="idade"> anos de idade.
  <br />
  <button @click="persistir">Salvar</button>
</div>
```

Agora temos dois campos (novamente ligados a uma instância Vue), mas agora há a adição de um botão que executa um método `persistir`. Vamos ver o JavaScript.

``` js
const app = new Vue({
  el:'#app',
  data: {
    nome:'',
    idade:0
  },
  mounted() {
    if(localStorage.nome) this.nome = localStorage.nome;
    if(localStorage.idade) this.idade = localStorage.idade;
  },
  methods: {
    persistir() {
      localStorage.nome = this.nome;
      localStorage.idade = this.idade;
      console.log('Agora finja que fiz mais coisas');
    }
  }
})
```

Como antes, `mounted` é usado para carregar os dados persistentes, se eles existirem. Desta vez, porém, os dados só são mantidos quando o botão é clicado. Também poderíamos fazer quaisquer validações ou transformações antes de armazenar o valor. Você também pode armazenar uma data representando quando os valores foram armazenados. Com esses metadados, o método `mounted` poderia fazer uma chamada lógica para armazenar, ou não, os valores novamente. Você pode tentar esta versão abaixo.

<p data-height="265" data-theme-id="0" data-slug-hash="PxqNZR" data-default-tab="js,result" data-user="brunohgv" data-pen-title="testing localstorage 2" class="codepen">See the Pen <a href="https://codepen.io/brunohgv/pen/PxqNZR/">testando localstorage 2</a> by Bruno Henrique Gusmão Vasconcelos (<a href="https://codepen.io/brunohgv">@brunohgv</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Trabalhando com valores complexos

Como mencionado acima, o armazenamento local só funciona com valores simples. Para armazenar valores mais complexos, como objetos ou arrays, você deve serializar e desserializar os valores com JSON. Aqui está um exemplo mais avançado que persiste um array de gatos (o melhor tipo de array possível).

``` html
<div id="app">
  <h2>Gatos</h2>
  <div v-for="(gato,n) in gatos">
    <p>
    <span class="gato">{{gato}}</span> <button @click="removerGato(n)">Remover</button>
    </p>
  </div>
  
  <p>
    <input v-model="novoGato"> 
    <button @click="adicionarGato">Adicionar Gato</button>
  </p>
  
</div>
```

Este "aplicativo" consiste em uma lista simples na parte superior (com um botão para remover um gato) e um pequeno formulário na parte inferior para adicionar um novo gato. Agora vamos ver o JavaScript.

``` js
const app = new Vue({
  el:'#app',
  data: {
    gatos:[],
    novoGato: null
  },
  mounted() {
    
    if(localStorage.getItem('gatos')) {
      try {
        this.gatos = JSON.parse(localStorage.getItem('gatos'));
      } catch(e) {
        localStorage.removeItem('gatos');
      }
    }
  },
  methods: {
    adicionarGato() {
      // Assegura de que foi digitado algo
      if(!this.novoGato) return;
      this.gatos.push(this.novoGato);
      this.novoGato = '';
      this.salvarGatos();
    },
    removerGato(x) {
      this.gatos.splice(x,1);
      this.salvarGatos();
    },
    salvarGatos() {
      let parsed = JSON.stringify(this.gatos);
      localStorage.setItem('gatos', parsed);
    }
  }
})
```

Nesta aplicação nós mudamos para usar as APIs de armazenamento local em vez do acesso "direto". Ambos funcionam, mas o método API é geralmente preferido. `mounted` agora tem que pegar o valor e analisar o valor do JSON. Se algo der errado aqui, assumimos que os dados estão corrompidos e os excluímos. (Lembre-se, sempre que seu aplicativo Web usa o armazenamento do lado do cliente, o usuário tem acesso a ele e pode modificá-lo à vontade.)

Nós temos três métodos agora para lidar trabalhando com gatos. Ambos `adicionarGato` e `removerGato` tratam de atualizar os dados Vue "vivos" armazenados em `this.gatos`. Em seguida, eles executam o `salvarGatos`, que manipula a serialização e a persistência dos dados. Você pode testar com esta versão abaixo:

<p data-height="265" data-theme-id="0" data-slug-hash="jQPWRe" data-default-tab="js,result" data-user="brunohgv" data-pen-title="localStorage, complexo" class="codepen">See the Pen <a href="https://codepen.io/brunohgv/pen/jQPWRe/">localStorage, complexo</a> by Bruno Henrique Gusmão Vasconcelos (<a href="https://codepen.io/brunohgv">@brunohgv</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Padrões Alternativos

Embora a API de armazenamento local seja relativamente simples, falta alguns recursos básicos que seriam úteis em muitos aplicativos. Os seguintes plug-ins englobam o acesso ao Armazenamento local e facilitam o uso, além de adicionar funcionalidades como valores padrão.

* [vue-local-storage](https://github.com/pinguinjkeke/vue-local-storage)
* [vue-reactive-storage](https://github.com/ropbla9/vue-reactive-storage)
* [vue2-storage](https://github.com/yarkovaleksei/vue2-storage)

## Empacotando

Embora o navegador nunca substitua um sistema de persistência de servidor, ter várias maneiras de armazenar dados em cache localmente pode ser um grande aumento de desempenho para sua aplicação, e trabalhar com isso no Vue.js o torna ainda mais poderoso.
