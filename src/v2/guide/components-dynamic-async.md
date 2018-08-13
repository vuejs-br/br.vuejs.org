---
title: Componentes dinâmicos e assíncronos
type: Guia
order: 105
---

> Esta página assume que você já leu a página de [Components Basics] (components.html). Leia primeiro se você é novo em componentes.

## `keep-alive` com componentes dinâmicos

Anteriormente, usamos o atributo `is` para alternar entre componentes em uma interface com guias:

```html
<component v-bind:is="currentTabComponent"></component>
```

Ao alternar entre esses componentes, às vezes, você desejará manter seu estado ou evitar a nova renderização por motivos de desempenho. Por exemplo, ao expandir nossa interface com guias um pouco:

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
Vue.component('tab-posts', {
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
          Clique no título de um blog à esquerda para visualizá-lo.\
        </strong>\
      </div>\
    </div>\
  '
})
Vue.component('tab-archive', {
  template: '<div>Archive component</div>'
})
new Vue({
  el: '#dynamic-component-demo',
  data: {
    currentTab: 'Posts',
    tabs: ['Posts', 'Archive']
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

Você notará que, se selecionar uma postagem, alterne para a guia _Archive_ e, em seguida, volte para _Posts_, ela não estará mais mostrando a postagem selecionada. Isso porque, cada vez que você muda para uma nova guia, o Vue cria uma nova instância do `currentTabComponent`.
Recriar componentes dinâmicos normalmente é um comportamento útil, mas, nesse caso, gostaríamos que essas instâncias de componentes de guias fossem armazenadas em cache assim que fossem criadas pela primeira vez. Para resolver este problema, podemos envolver nosso componente dinâmico com um elemento `<keep-alive>`:

``` html
<!-- Componentes inativos serão armazenados em cache -->
<keep-alive>
  <component v-bind:is="currentTabComponent"></component>
</keep-alive>
```

Check out the result below:

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
    currentTab: 'Posts',
    tabs: ['Posts', 'Archive']
  },
  computed: {
    currentTabComponent: function () {
      return 'tab-' + this.currentTab.toLowerCase()
    }
  }
})
</script>
{% endraw %}

Agora a guia _Posts_ mantém seu estado (a postagem selecionada) mesmo quando não é renderizada. Veja [este fiddle] (https://jsfiddle.net/chrisvfritz/Lp20op9o/) para o código completo.
<p class="tip">Perceba que o `<keep-alive>` requer que os componentes sejam trocados entre todos com nomes, usando a opção `name` em um componente ou através de registro local / global.</p>

Confira mais detalhes em `<keep-alive>` em [Referências de API] (../ api / # keep-alive).

## Componentes assíncronos

Em aplicativos grandes, talvez seja necessário dividir o aplicativo em partes menores e carregar apenas um componente do servidor quando necessário. Para tornar isso mais fácil, o Vue permite que você defina seu componente como uma função factory que resolve de forma assíncrona sua definição de componente. O Vue só acionará a função factory quando o componente precisar ser renderizado e armazenará em cache o resultado para re-renderizações futuras. Por exemplo:

``` js
Vue.component('async-example', function (resolve, reject) {
  setTimeout(function () {
    // Pass the component definition to the resolve callback
    resolve({
      template: '<div>I am async!</div>'
    })
  }, 1000)
})
```

Como você pode ver, a função factory recebe um retorno de chamada `resolve`, que deve ser chamado quando você recuperar sua definição de componente do servidor. Você também pode chamar `reject (reason)` para indicar que a carga falhou. O `setTimeout` aqui é para demonstração; como recuperar o componente é com você. Uma abordagem recomendada é usar componentes assíncronos junto com o recurso de divisão de código do Webpack (https://webpack.js.org/guides/code-splitting/):

``` js
Vue.component('async-webpack-example', function (resolve) {
  // This special require syntax will instruct Webpack to
  // automatically split your built code into bundles which
  // are loaded over Ajax requests.
  require(['./my-async-component'], resolve)
})
```

Você também pode retornar um Promise na função de factory, portanto, com a sintaxe do Webpack 2 e ES2015 você pode fazer:

``` js
Vue.component(
  'async-webpack-example',
  // The `import` function returns a Promise.
  () => import('./my-async-component')
)
```

Ao usar o [registro local] (components.html # Local-Registration), você também pode fornecer diretamente uma função que retorna um `Promise`:

``` js
new Vue({
  // ...
  components: {
    'my-component': () => import('./my-async-component')
  }
})
```

<p class = "tip"> Se você é um usuário do <strong> Browserify </strong> que gostaria de usar componentes assíncronos, seu criador infelizmente [deixou claro] (https://github.com/substack/ node-browserify / issues / 58 # issuecomment-21978224) que o carregamento assíncrono "não é algo que o Browserify irá suportar". Oficialmente, pelo menos. A comunidade Browserify encontrou [algumas soluções alternativas] (https://github.com/vuejs/vuejs.org/issues/620), que podem ser úteis para aplicativos existentes e complexos. Para todos os outros cenários, recomendamos o uso do Webpack para suporte async integrado de primeira classe. </p>

### Manipulando o Estado de Carregamento

> Novidade na versão 2.3.0+

A factory de componentes assíncronos também pode retornar um objeto do seguinte formato:

``` js
const AsyncComponent = () => ({
  // The component to load (should be a Promise)
  component: import('./MyComponent.vue'),
  // A component to use while the async component is loading
  loading: LoadingComponent,
  // A component to use if the load fails
  error: ErrorComponent,
  // Delay before showing the loading component. Default: 200ms.
  delay: 200,
  // The error component will be displayed if a timeout is
  // provided and exceeded. Default: Infinity.
  timeout: 3000
})
```

>Note que você deve usar o [Vue Rotas] (https://github.com/vuejs/vue-router) 2.4.0+ se você quiser usar a sintaxe acima para componentes de rota.
