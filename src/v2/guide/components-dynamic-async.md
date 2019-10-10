---
title: Dinâmicos & Assíncronos
type: guide
order: 105
---

> Esta página assume que você já leu o [Básico sobre Componentes](components.html). Leia lá primeiro se você for novo com componentes.

## Componentes Dinâmicos com `keep-alive`

Anteriormente, usamos o atributo `is` para alternar entre componentes em uma interface com guias:

{% codeblock lang:html %}
<component v-bind:is="currentTabComponent"></component>
{% endcodeblock %}

Ao alternar entre esses componentes, às vezes, você desejará manter seu estado ou evitar a nova renderização, por motivos de desempenho. Por exemplo, ao expandir nossa interface com guias um pouco:

{% raw %}
<div id="dynamic-component-demo" class="demo">
  <button
    v-for="tab in tabs"
    v-bind:key="tab"
    v-bind:class="['dynamic-component-demo-tab-button', { 'dynamic-component-demo-active': currentTab === tab }]"
    v-on:click="currentTab = tab"
  >{{ tab }}</button>
  <component
    v-bind:is="currentTabComponent"
    class="dynamic-component-demo-tab"
  ></component>
</div>
<script>
Vue.component('tab-postagens', {
  data: function () {
    return {
      posts: [
        {
          id: 1,
          title: 'Cat Ipsum',
          content: '<p>Dont wait for the storm to pass, dance in the rain kick up litter decide to want nothing to do with my owner today demand to be let outside at once, and expect owner to wait for me as i think about it cat cat moo moo lick ears lick paws so make meme, make cute face but lick the other cats. Kitty poochy chase imaginary bugs, but stand in front of the computer screen. Sweet beast cat dog hate mouse eat string barf pillow no baths hate everything stare at guinea pigs. My left donut is missing, as is my right loved it, hated it, loved it, hated it scoot butt on the rug cat not kitten around</p>'
        },
        {
          id: 2,
          title: 'Hipster Ipsum',
          content: '<p>Bushwick blue bottle scenester helvetica ugh, meh four loko. Put a bird on it lumbersexual franzen shabby chic, street art knausgaard trust fund shaman scenester live-edge mixtape taxidermy viral yuccie succulents. Keytar poke bicycle rights, crucifix street art neutra air plant PBR&B hoodie plaid venmo. Tilde swag art party fanny pack vinyl letterpress venmo jean shorts offal mumblecore. Vice blog gentrify mlkshk tattooed occupy snackwave, hoodie craft beer next level migas 8-bit chartreuse. Trust fund food truck drinking vinegar gochujang.</p>'
        },
        {
          id: 3,
          title: 'Cupcake Ipsum',
          content: '<p>Icing dessert soufflé lollipop chocolate bar sweet tart cake chupa chups. Soufflé marzipan jelly beans croissant toffee marzipan cupcake icing fruitcake. Muffin cake pudding soufflé wafer jelly bear claw sesame snaps marshmallow. Marzipan soufflé croissant lemon drops gingerbread sugar plum lemon drops apple pie gummies. Sweet roll donut oat cake toffee cake. Liquorice candy macaroon toffee cookie marzipan.</p>'
        }
      ],
      selectedPost: null
    }
  },
  template: '\
    <div class="dynamic-component-demo-posts-tab">\
      <ul class="dynamic-component-demo-posts-sidebar">\
        <li\
          v-for="post in posts"\
          v-bind:key="post.id"\
          v-bind:class="{ \'dynamic-component-demo-active\': post === selectedPost }"\
          v-on:click="selectedPost = post"\
        >\
          {{ post.title }}\
        </li>\
      </ul>\
      <div class="dynamic-component-demo-post-container">\
        <div \
          v-if="selectedPost"\
          class="dynamic-component-demo-post"\
        >\
          <h3>{{ selectedPost.title }}</h3>\
          <div v-html="selectedPost.content"></div>\
        </div>\
        <strong v-else>\
          Clique no título de uma postagem do blog, à esquerda, para visualizá-la.\
        </strong>\
      </div>\
    </div>\
  '
})
Vue.component('tab-arquivo', {
  template: '<div>Componente de postagens arquivadas</div>'
})
new Vue({
  el: '#dynamic-component-demo',
  data: {
    currentTab: 'Postagens',
    tabs: ['Postagens', 'Arquivo']
  },
  computed: {
    currentTabComponent: function () {
      return 'tab-' + this.currentTab.toLowerCase()
    }
  }
})
</script>
<style>
.dynamic-component-demo-tab-button {
  padding: 6px 10px;
  border-top-left-radius: 3px;
  border-top-right-radius: 3px;
  border: 1px solid #ccc;
  cursor: pointer;
  background: #f0f0f0;
  margin-bottom: -1px;
  margin-right: -1px;
}
.dynamic-component-demo-tab-button:hover {
  background: #e0e0e0;
}
.dynamic-component-demo-tab-button.dynamic-component-demo-active {
  background: #e0e0e0;
}
.dynamic-component-demo-tab {
  border: 1px solid #ccc;
  padding: 10px;
}
.dynamic-component-demo-posts-tab {
  display: flex;
}
.dynamic-component-demo-posts-sidebar {
  max-width: 40vw;
  margin: 0 !important;
  padding: 0 10px 0 0 !important;
  list-style-type: none;
  border-right: 1px solid #ccc;
}
.dynamic-component-demo-posts-sidebar li {
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
  cursor: pointer;
}
.dynamic-component-demo-posts-sidebar li:hover {
  background: #eee;
}
.dynamic-component-demo-posts-sidebar li.dynamic-component-demo-active {
  background: lightblue;
}
.dynamic-component-demo-post-container {
  padding-left: 10px;
}
.dynamic-component-demo-post > :first-child {
  margin-top: 0 !important;
  padding-top: 0 !important;
}
</style>
{% endraw %}

Você notará que, se selecionar uma postagem, alternar para a guia _Arquivo_ e, em seguida, voltar para _Postagens_, ela não estará mais mostrando a postagem selecionada. Isso acontece pois, cada vez que você muda para uma nova guia, o Vue cria uma nova instância do componente `currentTabComponent`.

Recriar componentes dinâmicos normalmente é um comportamento útil, mas, nesse caso, gostaríamos que essas instâncias de componentes de guias fossem armazenadas em _cache_ assim que fossem criadas pela primeira vez. Para resolver este problema, podemos envolver nosso componente dinâmico com um elemento `<keep-alive>`:

``` html
<!-- Componentes inativos serão armazenados em cache -->
<keep-alive>
  <component v-bind:is="currentTabComponent"></component>
</keep-alive>
```

Confira o resultado abaixo:

{% raw %}
<div id="dynamic-component-keep-alive-demo" class="demo">
  <button
    v-for="tab in tabs"
    v-bind:key="tab"
    v-bind:class="['dynamic-component-demo-tab-button', { 'dynamic-component-demo-active': currentTab === tab }]"
    v-on:click="currentTab = tab"
  >{{ tab }}</button>
  <keep-alive>
    <component
      v-bind:is="currentTabComponent"
      class="dynamic-component-demo-tab"
    ></component>
  </keep-alive>
</div>
<script>
new Vue({
  el: '#dynamic-component-keep-alive-demo',
  data: {
    currentTab: 'Postagens',
    tabs: ['Postagens', 'Arquivo']
  },
  computed: {
    currentTabComponent: function () {
      return 'tab-' + this.currentTab.toLowerCase()
    }
  }
})
</script>
{% endraw %}

Agora, a guia _Postagens_ mantém seu estado (a postagem selecionada) mesmo quando não é renderizada. Veja [este exemplo](https://jsfiddle.net/chrisvfritz/Lp20op9o/) para o código completo.

<p class="tip">Perceba que o `<keep-alive>` requer que todos os componentes que estejam sendo trocados tenham nomes, seja usando a opção `name` do componente, seja através de registro local/global.</p>

Confira mais detalhes sobre o `<keep-alive>` em [Referências de API](../api/#keep-alive).

## Componentes Assíncronos

<div class="vueschool"><a href="https://vueschool.io/lessons/dynamically-load-components?friend=vuejs" target="_blank" rel="sponsored noopener" title="Lição Gratuita sobre Componentes Assíncronos com Vue.js">Assista a uma lição gratuita em vídeo na Vue School</a></div>

Em aplicativos grandes, talvez seja necessário dividí-lo em partes menores e carregar apenas um componente do servidor quando necessário. Para tornar isto mais fácil, o Vue permite que você defina seu componente como uma função de fabricação (_factory function_), que resolve de forma assíncrona sua definição de componente. O Vue só acionará a função quando o componente precisar ser renderizado, e armazenará em _cache_ o resultado para novas renderizações futuras. Por exemplo:

``` js
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    // Passa a definição do componente para resolver o callback
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

Como você pode ver, a função de fabricação recebe um retorno de chamada `resolve`, que deve ser chamado quando você recuperar sua definição de componente do servidor. Você também pode chamar `reject(reason)` para indicar que o carregamento falhou. O `setTimeout` aqui é meramente para demonstração; como recuperar o componente é com você. Uma abordagem recomendada é usar componentes assíncronos junto com o recurso de [divisão de código do Webpack](https://webpack.js.org/guides/code-splitting/):

``` js
Vue.component('async-webpack-example', function (resolve) {
  // Esta sintaxe especial com `require` irá instruir o Webpack
  // a automaticamente dividir seu pacote em pedaços, os quais
  // serão carregados através de requisições Ajax.
  require(['./my-async-component'], resolve)
})
```

Você também pode retornar uma `Promise` na função de fabricação. Portanto, com a sintaxe do Webpack 2+ e ES2015, você pode fazer:

``` js
Vue.component(
  'async-webpack-example',
  // A função `import` retorna uma Promise.
  () => import('./my-async-component')
)
```

Ao usar o [registro local](components-registration.html#Registro-Local) de componentes, você também pode fornecer diretamente uma função que retorna uma `Promise`:

``` js
new Vue({
  // ...
  components: {
    'my-component': () => import('./my-async-component')
  }
})
```

<p class = "tip">Se você é um usuário do <strong>Browserify</strong> e gostaria de usar componentes assíncronos, seu criador infelizmente [deixou claro](https://github.com/substack/node-browserify/issues/58#issuecomment-21978224) que o carregamento assíncrono "não é algo que o Browserify irá suportar". Oficialmente, pelo menos. A comunidade Browserify encontrou [algumas soluções alternativas](https://github.com/vuejs/vuejs.org/issues/620), que podem ser úteis para aplicativos existentes e complexos. Para todos os outros cenários, recomendamos o uso do Webpack para suporte assíncrono integrado de primeira classe. </p>

### Manipulando Estado de Carregamento

> Novidade na versão 2.3.0+

A função de fábrica de componentes assíncronos também pode retornar um objeto no seguinte formato:

``` js
const AsyncComponent = () => ({
  // O componente a carregar (deve ser uma Promise)
  component: import('./MyComponent.vue'),
  // Um componente para usar enquanto o assíncrono é carregado
  loading: LoadingComponent,
  // Um componente para usar se o carregamento falhar
  error: ErrorComponent,
  // Espera antes de exibir o componente de loading. Padrão: 200ms
  delay: 200,
  // O componente de erro será exibido se um timemout for
  // fornecido e excedido. Padrão: Infinity
  timeout: 3000
})
```

> Note que você precisa utilizar o [vue-router](https://github.com/vuejs/vue-router) 2.4.0+ se você quiser usar a sintaxe acima para componentes de rota.
