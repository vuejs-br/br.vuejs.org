---
title: API
type: api
---

## Configuração Global

`Vue.config` é um objeto contendo a configuração global do Vue. Você pode modificar suas propriedades listadas abaixo antes de iniciar sua aplicação:

### silent

- **Tipo:** `boolean`

- **Padrão:** `false`

- **Uso:**

  ``` js
  Vue.config.silent = true
  ```

  Suprime todos os _logs_ e avisos do Vue.

### optionMergeStrategies

- **Tipo:** `{ [key: string]: Function }`

- **Padrão:** `{}`

- **Uso:**

  ``` js
  Vue.config.optionMergeStrategies._my_option = function (parent, child, vm) {
    return child + 1
  }

  const Profile = Vue.extend({
    _my_option: 1
  })

  // Profile.options._my_option = 2
  ```

  Define opções de mesclagem personalizadas.

  A estratégia de mesclagem recebe o valor da opção definida no pai e nas instâncias filhas como o primeiro e segundo argumento respectivamente. O contexto da instância Vue é passado como o terceiro argumento.

- **Ver também:** [Estratégias de Mesclagem de Opções Personalizadas](../guide/mixins.html#Estrategias-de-Mesclagem-de-Opcoes-Personalizadas)

### devtools

- **Tipo:** `boolean`

- **Padrão:** `true` (`false` em builds de produção)

- **Uso:**

  ``` js
  // tenha certeza de definir isso imediatamente síncrono após o carregamento do Vue
  Vue.config.devtools = true
  ```

  Configura se deseja permitir a inspeção do [vue-devtools](https://github.com/vuejs/vue-devtools). O valor padrão dessa opção é `true` em compilações de desenvolvimento e `false` em compilações de produção. Você pode definir `true` para forçar habilitar a inspeção em compilações de produção.

### errorHandler

- **Tipo:** `Function`

- **Padrão:** `undefined`

- **Uso:**

  ``` js
  Vue.config.errorHandler = function (err, vm, info) {
    // manuseia o erro
    // `info` é um erro específico do Vue, ex: cada lifecycle hook
    // que o erro foi encontrado. Disponível apenas em 2.2.0+
  }
  ```

  Atribui um manipulador para erros não detectados durante a renderização do componente e dos observadores. O manipulador é chamado com o erro e a instância Vue.

  > No 2.2.0+, esse gatilho também captura erros em gatilhos de ciclo de vida do componente. Além disso, quando esse gatilho é `undefined`, os erros capturados são registrados com `console.error` em vez de quebrar o aplicativo.

  > No 2.4.0+, esse gatilho também captura erros disparados dentro de manipuladores de eventos personalizados.

  > Serviços de rastreamento de erros [Sentry](https://sentry.io/for/vue/) e [Bugsnag](https://docs.bugsnag.com/platforms/browsers/vue/) fornecem integrações oficiais usando esta opção.

### warnHandler

> Novo em 2.4.0+

- **Tipo:** `Function`

- **Padrão:** `undefined`

- **Uso:**

  ``` js
  Vue.config.warnHandler = function (msg, vm, trace) {
    // `trace` é a pilha da hierarquia do componente
  }
  ```

  Atribui um gatilho personalizado para avisos Vue de tempo de execução. Observe que isto funciona apenas durante o desenvolvimento e será ignorado em produção.

### ignoredElements

- **Tipo:** `Array<string | RegExp>`

- **Padrão:** `[]`

- **Uso:**

  ``` js
  Vue.config.ignoredElements = [
    'my-custom-web-component',
    'another-web-component',
    // Use a `RegExp` to ignore all elements that start with "ion-"
    // 2.5+ only
    /^ion-/
  ]
  ```

  Faz com que o Vue ignore elementos customizados definidos fora do Vue (ex: usando APIs de Web Components). De outra forma, Vue lançaria um aviso sobre `Unknown custom element` (elemento customizado desconhecido), pois assumiria que você esqueceu de registrar algum componente global ou escreveu erroneamente o nome de um componente.

### keyCodes

- **Tipo:** `{ [key: string]: number | Array<number> }`

- **Padrão:** `{}`

- **Uso:**

  ``` js
  Vue.config.keyCodes = {
    v: 86,
    f1: 112,
    // camelCase não funciona
    mediaPlayPause: 179,
    // ao invés disso, pode usar kebab-case com aspas duplas
    "media-play-pause": 179,
    up: [38, 87]
  }
  ```

  ```html
  <input type="text" @keyup.media-play-pause="method">
  ```

  Define atalhos personalizados de teclas para o `v-on`.

### performance

> Novo em 2.2.0+

- **Tipo:** `boolean`

- **Padrão:** `false (desde 2.2.3+)`

- **Uso:**

  Defina `true` para habilitar o rastreamento de desempenho do _init_, _compile_, _render_ e _patch_ do componente na linha-do-tempo de inspeção do navegador. Só funciona no modo de desenvolvimento e em navegadores que suportam a API do [performance.mark](https://developer.mozilla.org/pt-BR/docs/Web/API/Performance/mark).

### productionTip

> Novo em 2.2.0+

- **Tipo:** `boolean`

- **Padrão:** `true`

- **Uso:**

  Defina `false` para prevenir a exibição da dica sobre o modo de produção ao iniciar o Vue.

## Métodos Globais

### Vue.extend( options )

- **Argumentos:**
  - `{Object} options`

- **Uso:**

  Cria uma "subclasse" do construtor base do Vue. O argumento deve ser um objeto contendo opções do componente.

  O caso especial a ser observado aqui é a opção `data` - ela deve ser uma função quando usada com `Vue.extend()`.

  ``` html
  <div id="mount-point"></div>
  ```

  ``` js
  // cria o construtor
  var Profile = Vue.extend({
    template: '<p>{{firstName}} {{lastName}} vulgo {{alias}}</p>',
    data: function () {
      return {
        firstName: 'Walter',
        lastName: 'White',
        alias: 'Heisenberg'
      }
    }
  })
  // cria uma instância de Profile e monta isto em um elemento
  new Profile().$mount('#mount-point')
  ```

  Irá resultar em:

  ``` html
  <p>Walter White vulgo Heisenberg</p>
  ```

- **Ver também:** [Componentes](../guide/components.html)

### Vue.nextTick( [callback, context] )

- **Argumentos:**
  - `{Function} [callback]`
  - `{Object} [context]`

- **Uso:**

  Adia o callback para ser executado depois do próximo ciclo de atualização do DOM. Use imediatamente após modificar algum dado para esperar a atualização do DOM.

  ``` js
  // modifica dados
  vm.msg = 'Hello'
  // DOM ainda não atualizado
  Vue.nextTick(function () {
    // DOM atualizado
  })

  // usage as a promise (2.1.0+, see note below)
  Vue.nextTick()
    .then(function () {
      // DOM updated
    })
  ```

  > Novo em 2.1.0+: retorna uma Promise se nenhum _callback_ é fornecido e Promise tiver suporte no ambiente de execução. Observe que Vue não vem com um _polyfill_ para Promise, portanto, se seu navegador alvo não oferece suporte a Promises (estamos olhando para você, IE), você precisará escolher um _polyfill_ por sua conta.

- **Ver também:** [Fila de Atualização Assíncrona](../guide/reactivity.html#Async-Update-Queue)

### Vue.set( target, key, value )

- **Argumentos:**
  - `{Object | Array} target`
  - `{string | number} key`
  - `{any} value`

- **Returna:** o valor atribuído.

- **Uso:**

  Adiciona uma propriedade em um objeto reativo, garantindo que a nova propriedade também seja reativa, disparando atualizações na interface. Deve ser utilizado para adicionar novas propriedades em objetos reativos, já que o Vue não pode detectar adição de propriedades direta (ex.: `this.myObject.newProperty = 'hi'`).

  <p class="tip">O objeto alvo não pode ser uma instância Vue, ou um objeto de dados raiz de uma instância Vue.</p>

- **Ver também:** [Reatividade em Profundidade](../guide/reactivity.html)

### Vue.delete( target, key )

- **Argumentos:**
  - `{Object | Array} target`
  - `{string | number} key/index`

  > Somente em 2.2.0+: Também funciona com Array + index.

- **Uso:**

  Remove uma propriedade de um objeto. Se o objeto é reativo, certifique-se que a remoção dispara atualizações na view. Isto é usado primariamente para contornar a limitação que o Vue não consegue detectar remoção de propriedades, mas você raramente precisará usar isto.

  <p class="tip">O objeto a ser removido não pode ser uma instância do Vue ou o dado de um objeto root de uma instância do Vue</p>

- **Ver também:** [Reatividade em Profundidade](../guide/reactivity.html)

### Vue.directive( id, [definition] )

- **Argumentos:**
  - `{string} id`
  - `{Function | Object} [definition]`

- **Uso:**

  Registra ou recupera uma diretiva global.

  ``` js
  // registra
  Vue.directive('my-directive', {
    bind: function () {},
    inserted: function () {},
    update: function () {},
    componentUpdated: function () {},
    unbind: function () {}
  })

  // registra (diretiva de função)
  Vue.directive('my-directive', function () {
    // isto será chamado como `bind` e `update`
  })

  // getter, retorna a definição da diretiva se registrada
  var myDirective = Vue.directive('my-directive')
  ```

- **Ver também:** [Diretivas Personalizadas](../guide/custom-directive.html)

### Vue.filter( id, [definition] )

- **Argumentos:**
  - `{string} id`
  - `{Function} [definition]`

- **Uso:**

  Registra ou recupera um filtro global.

  ``` js
  // registra
  Vue.filter('my-filter', function (value) {
    // retorna o valor processado
  })

  // getter, retorna o filtro se registrado
  var myFilter = Vue.filter('my-filter')
  ```

- **See also:** [Filters](../guide/filters.html)

### Vue.component( id, [definition] )

- **Argumentos:**
  - `{string} id`
  - `{Function | Object} [definition]`

- **Uso:**

  Registra ou recupera um componente global. O registro também atribui, automaticamente, o objeto `name` do componente com o `id` passado.

  ``` js
  // registra um construtor extendido
  Vue.component('my-component', Vue.extend({ /* ... */ }))

  // registra um objeto com opções (automaticamente chama Vue.extend)
  Vue.component('my-component', { /* ... */ })

  // recupera um componente registrado (sempre retorna um construtor)
  var MyComponent = Vue.component('my-component')
  ```

- **Ver também:** [Componentes](../guide/components.html)

### Vue.use( plugin )

- **Argumentos:**
  - `{Object | Function} plugin`

- **Uso:**

  Instala um plugin do Vue.js. Se o plugin é um Objeto, ele deve expor um método `install`. Se for uma função, ela será tratada como o método install. O método install será chamado com Vue como argumento.

  Quando este método é chamado no mesmo plugin múltiplas vezes, o plugin só será instalado uma única vez.

- **Ver também:** [Plugins](../guide/plugins.html)

### Vue.mixin( mixin )

- **Argumentos:**
  - `{Object} mixin`

- **Uso:**

  Aplica um mixin globalmente, o qual afeta toda instância do Vue criada posteriormente. Isto pode ser usado por autores de plugins para injetar um comportamento personalizado em componentes. **Não recomendado no código da aplicação**.

- **Ver também:** [Mixin Global](../guide/mixins.html#Global-Mixin)

### Vue.compile( template )

- **Argumentos:**
  - `{string} template`

- **Uso:**

  Compila um template string em uma função de renderização. **Somente disponível no build completo.**

  ``` js
  var res = Vue.compile('<div><span>{{ msg }}</span></div>')

  new Vue({
    data: {
      msg: 'hello'
    },
    render: res.render,
    staticRenderFns: res.staticRenderFns
  })
  ```

- **Ver também:** [Funções de Renderização](../guide/render-function.html)

### Vue.version

- **Detalhes**: Disponibiliza a versão do Vue instalado como string. Isto é especialmente útil para plugins e componentes da comunidade, onde você pode usar diferentes estratégias para diferentes versões.

- **Uso:**

  ```js
  var version = Number(Vue.version.split('.')[0])

  if (version === 2) {
    // Vue v2.x.x
  } else if (version === 1) {
    // Vue v1.x.x
  } else {
    // Versões não suportadas do Vue
  }
  ```

## Opções / Dados

### data

- **Tipo:** `Object | Function`

- **Restrição:** Somente aceita `Function` quando usado na definição de um componente.

- **Detalhes:**

  O objeto `data` para a instância do Vue. Vue irá recursivamente converter esta propriedade em _getter_/_setters_ para tornar "reativo". **O objeto deve ser simples**: objetos nativos como API do navegador e propriedades _prototype_ são ignoradas. A regra geral é: `data` deverá ser somente dados - não é recomendável observar objetos que têm seu próprio comportamento _stateful_.

  Como observado, você não pode adicionar propriedades reativas ao objeto data raiz. É recomendado declarar todas as propriedades 'nível raiz' reativas com antecedência, antes da criação da instância.

  Depois que a instância é criada, o objeto data original pode ser acessado como `vm.$data`. A instância Vue também faz proxy de todas as propriedade achadas no objeto data, então `vm.a` será equivalente a `vm.$data.a`.

  Propriedades que comecem com `_` ou `$` **não** será feito proxy na instância do Vue porque eles podem conflitar com métodos de API ou propriedades internas do Vue. Você terá de acessar elas com `vm.$data._property`.

  Quando definindo um **componente**, `data` deve ser declarado como uma função que retorna o objeto de dados inicial, pois haverá muitas instâncias criadas usando a mesma definição. Se usarmos um objeto simples para `data`, este mesmo objeto será **compartilhado por referência** entre todas as instâncias criadas! Provendo uma função `data`, toda vez que uma nova instância é criada, podemos chamá-la para retornar uma nova cópia do dado inicial.

  Se necessário, uma cópia profunda do objeto original pode ser obtida passando `vm.$data` através de `JSON.parse(JSON.stringify(...))`.

- **Exemplo:**

  ``` js
  var data = { a: 1 }

  // Criação direta de instância
  var vm = new Vue({
    data: data
  })
  vm.a // => 1
  vm.$data === data // => true

  // deve-se usar function quando em uso com Vue.extend()
  var Component = Vue.extend({
    data: function () {
      return { a: 1 }
    }
  })
  ```

  Note que se você usar *arrow function* com a propriedade `data`, `this` não será a instância do componente, mas você poderá continuar tendo acesso a instância através do argumento:

  ```js
  data: vm => ({ a: vm.myProp })
  ```

- **Ver também:** [Reatividade em Profundidade](../guide/reactivity.html)

### props

- **Tipo:** `Array<string> | Object`

- **Detalhes:**

  Uma lista/_hash_ de atributos que são expostos para aceitar dados do componente pai. Possui tanto uma sintaxe baseada em Array como, alternativamente, uma sintaxe baseada em Object, que permite configurações avançadas como checagem de tipos, validações personalizadas e valores padrão.

- **Exemplo:**

  ``` js
  // sintaxe simples
  Vue.component('props-demo-simple', {
    props: ['size', 'myMessage']
  })

  // sintaxe de objeto com validação
  Vue.component('props-demo-advanced', {
    props: {
      // checagem de tipos
      height: Number,
      // checagem de tipos com validações
      age: {
        type: Number,
        default: 0,
        required: true,
        validator: function (value) {
          return value >= 0
        }
      }
    }
  })
  ```
- **Ver também:** [Propriedades](../guide/components.html#Propriedades)

### propsData

- **Tipo:** `{ [key: string]: any }`

- **Restrição:** somente respeitado em criação de instâncias via `new`.

- **Detalhes:**

  Passa props a uma instância durante a sua criação. A intenção disso é primariamente tornar testes unitários mais fáceis.

- **Exemplo:**

  ``` js
  var Comp = Vue.extend({
    props: ['msg'],
    template: '<div>{{ msg }}</div>'
  })

  var vm = new Comp({
    propsData: {
      msg: 'hello'
    }
  })
  ```

### computed

- **Tipo:** `{ [key: string]: Function | { get: Function, set: Function } }`

- **Detalhes:**

  Dados Computados para serem misturadas na instância Vue. Todos os getters e setters tem o seu contexto `this` automaticamente feito bind a instância Vue.

  Note que se você usar *arrow function* com uma propriedade computada, `this` não será a instância do componente, mas você poderá ter acesso a instância através do seguinte argumento:

  ```js
  computed: {
    aDouble: vm => vm.a * 2
  }
  ```

  Dados Computados são cacheadas, e somente re-computados quando dependências reativas mudam. Note que se uma certa dependência está fora do escopo da instância (ex.: não reativa), o dado computado __não__ será atualizado.

- **Exemplo:**

  ```js
  var vm = new Vue({
    data: { a: 1 },
    computed: {
      // somente get
      aDouble: function () {
        return this.a * 2
      },
      // get e set
      aPlus: {
        get: function () {
          return this.a + 1
        },
        set: function (v) {
          this.a = v - 1
        }
      }
    }
  })
  vm.aPlus   // => 2
  vm.aPlus = 3
  vm.a       // => 2
  vm.aDouble // => 4
  ```

- **Ver também:** [Dados Computados](../guide/computed.html)

### methods

- **Tipo:** `{ [key: string]: Function }`

- **Detalhes:**

  Métodos para serem misturadas na instância Vue. Você pode acessar esses métodos diretamente na instância VM ou usar eles em expressões de diretivas. Todos os métodos terão seu contexto `this` feito bind a instância Vue.

  <p class="tip">Note que __você não deve usar arrow function para definir um método__ (ex.: `plus: () => this.a++`). A razão é que arrow functions fazem bind do contexto pai, então `this` não será a instância Vue como você está esperando e `this.a` será undefined.</p>

- **Exemplo:**

  ```js
  var vm = new Vue({
    data: { a: 1 },
    methods: {
      plus: function () {
        this.a++
      }
    }
  })
  vm.plus()
  vm.a // 2
  ```

- **Ver também:** [Manipulação de Eventos](../guide/events.html)

### watch

- **Tipo:** `{ [key: string]: string | Function | Object | Array}`

- **Detalhes:**

  Um objeto onde as chaves são expressões para observar e os valores são os _callbacks_ correspondentes. O valor pode ser uma string do nome do método, ou um Objeto que contém opções adicionais. A instância Vue chamará `$watch()` para cada entrada no objeto na inicialização.

- **Exemplo:**

  ``` js
  var vm = new Vue({
    data: {
      a: 1,
      b: 2,
      c: 3,
      d: 4,
      e: {
        f: {
          g: 5
        }
      }
    },
    watch: {
      a: function (val, oldVal) {
        console.log('novo: %s, antigo: %s', val, oldVal)
      },
      // nome do método como string
      b: 'someMethod',
      // observador profundo
      c: {
        handler: function (val, oldVal) { /* ... */ },
        deep: true
      },
      // the callback will be called immediately after the start of the observation
      d: {
        handler: function (val, oldVal) { /* ... */ },
        immediate: true
      },
      e: [
        function handle1 (val, oldVal) { /* ... */ },
        function handle2 (val, oldVal) { /* ... */ }
      ],
      // watch vm.e.f's value: {g: 5}
      'e.f': function (val, oldVal) { /* ... */ }
    }
  })
  vm.a = 2 // => new: 2, old: 1
  ```

  <p class="tip">Note que __você não deve usar arrow function para definir um observador__ (e.g. `searchQuery: newValue => this.updateAutocomplete(newValue)`). A razão é que arrow functions fazem bind do contexto pai, então `this` não será a instância Vue como você está esperando e `this.updateAutocomplete` será undefined.</p>

- **Ver também:** [Métodos da Instância / Data - vm.$watch](#vm-watch)

## Opções / DOM

### el

- **Tipo:** `string | HTMLElement`

- **Restrição:** respeitado apenas durante a criação da instância com `new`.

- **Detalhes:**

  Fornece um elemento existente no DOM para a instância Vue ser montada. Pode ser um seletor CSS ou um `HTMLElement`.

  Após a instância ser montada, o elemento gerado poderá ser acessado utilizando `vm.$el`.

  Se essa opção estiver disponível durante a instanciação, a instância irá compilar imediatamente; caso contrário, o usuário terá que chamar explicitamente `vm.$mount()` para iniciar manualmente a compilação.

  <p class="tip">O elemento fornecido serve apenas como ponto de montagem. Diferentemente do Vue 1.x, o elemento montado será substituído pelo DOM gerado pelo Vue em todos os casos. Portanto, não é recomendado montar a instância raiz em `<html>` ou `<body>`.</p>

  <p class="tip">Se a função `render` ou a opção `template` não estiverem presentes, o HTML já existente no DOM do elemento sendo montado será extraído como seu _template_. Neste caso, a versão Runtime + Compilador do Vue precisa ser utilizada.</p>

- **Ver também:**
  - [Diagrama do Ciclo de Vida](../guide/instance.html#Diagrama-do-Ciclo-de-Vida)
  - [Runtime + Compiler vs. Runtime](../guide/installation.html#Runtime-Compilador-vs-Runtime)

### template

- **Tipo:** `string`

- **Detalhes:**

  Um _template_ dentro de uma String para ser usado como marcação para a instância Vue. O _template_ irá **substituir** o elemento montado. Qualquer marcação existente dentro do elemento montado será ignorada, a menos que _slots_ de distribuição de conteúdo estejam presentes no _template_.

  Se a String começa com `#`, ela será usada como um `querySelector` e utilizará o `innerHTML` do elemento selecionado como _template_ String. Isto permite o uso do truque comum com `<script type="x-template">` para incluir _templates_.

  <p class="tip">De uma perspective de segurança, você deve usar apenas _templates_ Vue em que possa confiar. Nunca use conteúdo gerado pelo usuário como seu _template_.</p>

  <p class="tip">Se a função `render` estiver presente nas opções Vue, o _template_ será ignorado.</p>

- **Ver também:**
  - [Diagrama do Ciclo de Vida](../guide/instance.html#Diagrama-do-Ciclo-de-Vida)
  - [Distribuição de Conteúdo com Slots](../guide/components.html#Distribuicao-de-Conteudo-com-Slots)

### render

  - **Tipo:** `(createElement: () => VNode) => VNode`

  - **Detalhes:**

    Uma alternativa aos _templates_ baseados em Strings, que permite aproveitar o completo poder pragmático do JavaScript. A função de renderização recebe um método `createElement` como primeiro argumento para criar elementos `VNode`.

    Se o componente é um componente funcional, a função de renderização também recebe um argumento extra `context` que fornece acesso a dados contextuais, uma vez que componentes funcionais não possuem instância própria.

    <p class="tip">A função `render` tem prioridade sobre funções de renderização compiladas através da opção `template` e sobre _template_ extraído do DOM do elemento sendo montado conforme especificado com a opção `el`.</p>

  - **Ver também:** [Funções de Renderização](../guide/render-function.html)

### renderError

> Novo em 2.2.0+

  - **Tipo:** `(createElement: () => VNode, error: Error) => VNode`

  - **Detalhes:**

    **Funciona apenas em Modo de Desenvolvimento.**

    Provê uma saída de renderização alternativa para quando a função `render` padrão encontrar um erro. O objeto de erro será passado ao `renderError` como seu segundo argumento. É particularmente útil quando utilizado em conjunto com _hot-reload_.

  - **Exemplo:**

    ``` js
    new Vue({
      render (h) {
        throw new Error('oops')
      },
      renderError (h, err) {
        return h('pre', { style: { color: 'red' }}, err.stack)
      }
    }).$mount('#app')
    ```

  - **Ver também:** [Funções de Renderização](../guide/render-function.html)

## Opções / Ciclo-de-Vida

<p class="tip">Todos os gatilhos de ciclo de vida automaticamente possuem seus contextos `this` vinculados à instância, assim você pode acessar dados, propriedades computadas e métodos. Isso significa que __você não deve usar Arrow Function para definir este tipo de método__ (por exemplo, `created: () => this.fetchTodos()`). Isto porque _arrow functions_ vinculam o contexto pai, assim `this` não será a instância Vue como você espera e `this.fetchTodos` será `undefined`.</p>

### beforeCreate

- **Tipo:** `Function`

- **Detalhes:**

  Invocado sincronicamente logo após a instância ser inicializada, antes da observação de dados e configuração de eventos e observadores.

- **Ver também:** [Diagrama do Ciclo de Vida](../guide/instance.html#Diagrama-do-Ciclo-de-Vida)

### created

- **Tipo:** `Function`

- **Detalhes:**

  Invocado sincronicamente após a instância ser criada. Neste ponto, a instância finalizou o processamento das opções, o que significa que  foi configurado:  observação de dados, propriedades computadas, métodos, watch/event _callbacks_. Entretanto, a fase de montagem não foi iniciada, e a propriedade `$el` não estará disponível ainda.

- **Ver também:** [Diagrama do Ciclo de Vida](../guide/instance.html#Diagrama-do-Ciclo-de-Vida)

### beforeMount

- **Tipo:** `Function`

- **Detalhes:**

  Invocado logo antes da montagem começar: a função `render` está prestes a ser invocada pela primeira vez.

  **Este gatilho não é invocado em Renderização no Lado do Servidor.**

- **Ver também:** [Diagrama do Ciclo de Vida](../guide/instance.html#Diagrama-do-Ciclo-de-Vida)

### mounted

- **Tipo:** `Function`

- **Detalhes:**

  Invocado logo após a instância ter sido montada, onde `el` é substituído pelo recém criado `vm.$el`. Se a instância raiz é montada em um elemento já presente no documento, `vm.$el` também estará presente no documento quando `mounted` for invocada.

  Observe que o `mounted` **não** garante que todos os componentes filhos também já tenham sido montados. Se você quiser esperar até que toda a camada _view_ do componente em questão tiver sido renderizada, você pode utilizar [vm.$nextTick](#vm-nextTick) dentro do `mounted`:

  ``` js
  mounted: function () {
    this.$nextTick(function () {
      // Código que irá rodar apenas após toda
      // a árvore do componente ter sido renderizada
    })
  }
  ```

  **Este gatilho não é invocado em Renderização no Lado do Servidor.**

- **Ver também:** [Diagrama do Ciclo de Vida](../guide/instance.html#Diagrama-do-Ciclo-de-Vida)

### beforeUpdate

- **Tipo:** `Function`

- **Detalhes:**

  Invocado quando os dados mudam, antes do virtual DOM ser re-renderizado e atualizado. Este é um bom local para acessar DOM existente antes de uma atualização, por exemplo, para remover escutas de eventos adicionadas manualmente.

  **Este gatilho não é invocado em Renderização no Lado do Servidor, pois apenas a renderização inicial é realizada no servidor.**

- **Ver também:** [Diagrama do Ciclo de Vida](../guide/instance.html#Diagrama-do-Ciclo-de-Vida)

### updated

- **Tipo:** `Function`

- **Detalhes:**

  Invocado após a mudança de dados fazer o virtual DOM ser re-renderizado e atualizado.

  O DOM do componente estará no estado `updated` quando este gatilho for invocado, assim você pode realizar operações dependentes de DOM neste gatilho.
  Entretanto, na maioria dos casos você deveria evitar mudar o estado deste gatilho, melhor utilizar [propriedades computadas](#computed) ou [observadores](#watch).

  Observe que o `updated` **não** garante que todos os componentes filhos também já tenham sido montados. Se você quiser esperar até que toda a camada _view_ do componente em questão tiver sido renderizada, você pode utilizar [vm.$nextTick](#vm-nextTick) dentro do `updated`:

  ``` js
  updated: function () {
    this.$nextTick(function () {
      // Código que irá rodar apenas após toda
      // a árvore do componente ter sido re-renderizada
    })
  }
  ```

  **Este gatilho não é invocado em Renderização no Lado do Servidor.**

- **Ver também:** [Diagrama do Ciclo de Vida](../guide/instance.html#Diagrama-do-Ciclo-de-Vida)

### activated

- **Tipo:** `Function`

- **Detalhes:**

  Invocado quando um componente kept-alive é ativado.

  **Este gatilho não é invocado em Renderização no Lado do Servidor.**

- **Ver também:**
  - [Built-in Components - keep-alive](#keep-alive)
  - [Dynamic Components - keep-alive](../guide/components.html#keep-alive)

### deactivated

- **Tipo:** `Function`

- **Detalhes:**

  Invocado quando um componente kept-alive é desativado.

  **Este gatilho não é invocado em Renderização no Lado do Servidor.**

- **Ver também:**
  - [Built-in Components - keep-alive](#keep-alive)
  - [Dynamic Components - keep-alive](../guide/components.html#keep-alive)

### beforeDestroy

- **Tipo:** `Function`

- **Detalhes:**

  Invocado logo antes da instância Vue ser destruída. Neste ponto a instância ainda é completamente funcional.

  **Este gatilho não é invocado em Renderização no Lado do Servidor.**

- **Ver também:** [Diagrama do Ciclo de Vida](../guide/instance.html#Diagrama-do-Ciclo-de-Vida)

### destroyed

- **Tipo:** `Function`

- **Detalhes:**

  Invocado após a instância Vue ter sido destruída. Quando este gatilho é invocado, todas as diretivas da instância Vue já foram desvinculadas, todos os event listeners foram removidos, e todas as instâncias Vue filhas também foram destruídas.


  **Este gatilho não é invocado em Renderização no Lado do Servidor.**

- **Ver também:** [Diagrama do Ciclo de Vida](../guide/instance.html#Diagrama-do-Ciclo-de-Vida)

### errorCaptured

> Novo em 2.5.0+

- **Tipo:** `(err: Error, vm: Component, info: string) => ?boolean`

- **Detalhes:**

  Invocado quando um erro emitido por qualquer componente descendente é capturado. O gatilho recebe três argumentos: o erro, a instância do componente que disparou o erro, e uma String contendo informações sobre onde o erro foi capturado. O gatilho pode retornar `false` para impedir o erro de continuar se propagando.

  <p class="tip">Você pode modificar o estado do componente neste gatilho. Entretanto, é importante possuir condicionais em seu _template_ ou função de renderização que alterne para outro conteúdo quando um erro tiver sido capturado; caso contrário, o componente cairá em um _loop_ infinito de renderização.</p>

  **Regras de Propagação de Erros**

  - Por padrão, todos os erros ainda são enviados para o global `config.errorHandler` se estiver definido, desta forma estes erros ainda podem ser reportados a algum serviço de análise em um local único.

  - Se múltiplos gatilhos `errorCaptured` existirem em uma cadeia de heranças ou em uma cadeia de componentes pai, todos serão invocados pelo mesmo erro.

  - Se o próprio gatilho `errorCaptured` dispara um erro, tanto este erro quanto o original são capturados e enviados ao gatilho global `config.errorHandler`.

  - Um gatilho `errorCaptured` pode retornar `false` para prevenir o erro de continuar se propagando. Isto essencialmente diria "este erro já foi interceptado e deve ser ignorado." Irá prevenir quaisquer gatilhos `errorCaptured` adicionais e o global `config.errorHandler` de serem invocados por este erro.

## Opções / Recursos

### directives

- **Tipo:** `Object`

- **Detalhes:**

  Um conjunto de diretivas que será disponibilizado para a instância Vue.

- **Ver também:** [Diretivas Personalizadas](../guide/custom-directive.html)

### filters

- **Tipo:** `Object`

- **Detalhes:**

  Um conjunto de filtros que será disponibilizado para a instância Vue.

- **Ver também:** [`Vue.filter`](#Vue-filter)

### components

- **Tipo:** `Object`

- **Detalhes:**

  Um conjunto de componentes que será disponibilizado para a instância Vue.

- **Ver também:** [Componentes](../guide/components.html)

## Opções / Composição

### parent

- **Tipo:** `Vue instance`

- **Detalhes:**

  Especifica a instância pai para instância que será criada. Estabelece a relação pai-filho entre as duas. O pai será acessível como `this.$parent` para o filho, e o filho será inserido no vetor `$children` do pai.

  <p class="tip">Use `$parent` e `$children` com moderação - eles servem, na maioria das vezes, como saída de emergência. Use, preferencialmente, propriedades e eventos para comunicação pai-filho.</p>

### mixins

- **Tipo:** `Array<Object>`

- **Detalhes:**

  A opção `mixins` aceita um Array de objetos _mixin_. Esses objetos _mixin_ podem conter `options` da instância, assim como, as instâncias de objetos normais. Eles serão combinados com as eventuais opções usando a mesma lógica de combinação no `Vue.extend()`. Por exemplo, se o seu _mixin_ contém um gatilho _created_ e o componente por si só também contém um, as duas funções serão chamadas.

  Os gatilhos de _mixin_ são chamados na ordem em que são fornecidos e chamados antes dos hooks do próprio componente.

- **Exemplo:**

  ``` js
  var mixin = {
    created: function () { console.log(1) }
  }
  var vm = new Vue({
    created: function () { console.log(2) },
    mixins: [mixin]
  })
  // => 1
  // => 2
  ```

- **Ver também:** [Mixins](../guide/mixins.html)

### extends

- **Tipo:** `Object | Function`

- **Detalhes:**

  Possibilita estender outro componente declarativamente (pode ser tanto um objeto plano com opções ou um construtor) sem ter que usar `Vue.extend`. Isso visa primeiramente tornar mais fácil extensão entre componentes _single_ _file_.

  Isso é parecido com `mixins`. 

- **Exemplo:**

  ``` js
  var CompA = { ... }

  // estende CompA sem ter que invocar Vue.extend
  var CompB = {
    extends: CompA,
    ...
  }
  ```

### provide / inject

> Novo em 2.2.0+

- **Tipo:**
  - **provide:** `Object | () => Object`
  - **inject:** `Array<string> | { [key: string]: string | Symbol | Object }`

- **Detalhes:**

  <p class="tip">`provide` e `inject` são primeiramente fornecidos para casos de uso avançados de _plugins_ / componentes. NÃO é aconselhável seu uso em código de aplicação genérica.</p>

  Esse par de opções é usado para possibilitar que um componente pai sirva de injetor de dependência para todos os filhos, independente de quão funda é a hierarquia do componente, desde que eles sejam usados no mesmo escopo pai. Se você é familiar com o React, isso é muito similar ao recurso _React's_ _context_.

  A opção `provide` deve ser um objeto ou uma função que retorna um objeto. Esse objeto contém as propriedades que estão disponíveis para serem injetadas em seus descendentes. Você pode usar ES2015 _Symbols_ como chaves nesse objeto, mas só em ambientes que suportam nativamente `Symbol` e `Reflect.ownKeys`.

  A opção `inject` deve ser uma das opções:
  - um array de _Strings_, ou
  - um objeto onde as chaves referenciam os nomes de _bind_ local, e os valores sendo:
    - uma chave (_String_ ou _Symbol_) para buscar nas inserções disponíveis, ou
    - um objeto onde:
      - a propriedade `from` é a chave (_String_ ou _Symbol_) para buscar possíveis inserções, e
      - a propriedade `default` é usada como valor padrão

  > Nota: os binds `provide` e `inject` não são reativos. Isso é intencional. Porém, se você passar um objeto observed, a propriedades desse objeto se mantém reativas.

- **Exemplo:**

  ``` js
  // parent component providing 'foo'
  var Provider = {
    provide: {
      foo: 'bar'
    },
    // ...
  }

  // child component injecting 'foo'
  var Child = {
    inject: ['foo'],
    created () {
      console.log(this.foo) // => "bar"
    }
    // ...
  }
  ```

  Com ES2015 _Symbols_, função `provide` e objeto `inject`:
  ``` js
  const s = Symbol()

  const Provider = {
    provide () {
      return {
        [s]: 'foo'
      }
    }
  }

  const Child = {
    inject: { s },
    // ...
  }
  ```

  > Os próximos 2 exemplos funcionam com Vue 2.2.1+. Anteriormente a essa versão, os valores injetados eram resolvidos depois da inicialização de `propriedades` e `data`

  Usando um valor injetado como padrão para uma propriedade:
  ```js
  const Child = {
    inject: ['foo'],
    props: {
      bar: {
        default () {
          return this.foo
        }
      }
    }
  }
  ```

  Usando um valor injetado como valor de entrada para _data_:
  ```js
  const Child = {
    inject: ['foo'],
    data () {
      return {
        bar: this.foo
      }
    }
  }
  ```

  > Em 2.5.0+ injeções podem ser opcionais com um valor padrão:

  ``` js
  const Child = {
    inject: {
      foo: { default: 'foo' }
    }
  }
  ```

  Se precisar ser injetado a partir de uma propriedade com um nome diferente, use `from` para identificar a propriedade de origem:

  ``` js
  const Child = {
    inject: {
      foo: {
        from: 'bar',
        default: 'foo'
      }
    }
  }
  ```

  Similar aos valores padrões em `props`, você precisa usar uma função de fábrica para valores não primitivos:

  ``` js
  const Child = {
    inject: {
      foo: {
        from: 'bar',
        default: () => [1, 2, 3]
      }
    }
  }
  ```

## Opções / Diversos

### name

- **Tipo:** `string`

- **Restrição:** somente é respeitada quando usada como uma opção do componente.

- **Detalhes:**

  Permite que o componente invoque a si mesmo recursivamente no seu *template*. Note que quando um componente é registrado globalmente com `Vue.component()` e seu ID global é automaticamente especificado com seu nome.

  Outro benefício de se especificar a opção `name` é depuração. Componentes nomeados resultarão em mensagens de aviso mais prestativas. Além disso, ao se inspecionar uma aplicação em [vue-devtools](https://github.com/vuejs/vue-devtools), componentes não nomeados aparecerão como `<AnonymousComponent>`, o que não é muito informativo. Ao fornecer a opção `name`, você terá uma árvore de componentes muito mais informativa.

### delimiters

- **Tipo:** `Array<string>`

- **Padrão:** `{% raw %}["{{", "}}"]{% endraw %}`

- **Restrições:** Esta opção só está disponível na *build* completa, com compilação no navegador.

- **Detalhes:**

  Altera os delimitadores de interpolação de texto simples.

- **Exemplo:**

  ``` js
  new Vue({
    delimiters: ['${', '}']
  })

  // Mudando os delimitadores para o estilo do ES6 template string
  ```

### functional

- **Tipo:** `boolean`

- **Detalhes:**

  Faz com que um componente seja _stateless_ (ou seja, não possui `data`) e _instanceless_ (ou seja, não possui o contexto `this`). Componentes com esta opção habilitada serão apenas uma função `render` que retorna nós virtuais, fazendo com que sejam muito menos custosos para renderizar.

- **Ver também:** [Componentes Funcionais](../guide/render-function.html#Componentes-Funcionais)

### model

> Novo em 2.2.0

- **Tipo:** `{ prop: string, event: string }`

- **Detalhes:**

  Permite que um componente personalize a propriedade e o evento usados quando `v-model` é utilizado. Por padrão, `v-model` em um componente usa `value` como a propriedade e `input` como o evento, mas alguns tipos de *inputs* (como *checkbox* e *radio buttons*) podem querer usar a propriedade `value` com outro propósito. Usar a opção `model` pode evitar conflitos nesses casos.

- **Exemplo:**

  ``` js
  Vue.component('my-checkbox', {
    model: {
      prop: 'checked',
      event: 'change'
    },
    props: {
      // Isto permite usar a propriedade `value` com um propósito diferente
      value: String,
      // Usando `checked` como a propriedade, ao invés de `value`
      checked: {
        type: Number,
        default: 0
      }
    },
    // ...
  })
  ```

  ``` html
  <my-checkbox v-model="foo" value="some value"></my-checkbox>
  ```

  O código acima equivale a:

  ``` html
  <my-checkbox
    :checked="foo"
    @change="val => { foo = val }"
    value="some value">
  </my-checkbox>
  ```

### inheritAttrs

> Novo em 2.4.0+

- **Tipo:** `boolean`

- **Padrão:** `true`

- **Detalhes:**

  Por padrão, atributos *binded* ao escopo do componente pai que não são reconhecidos como *props* serão aplicados ao elemento raíz do componente filho como atributos normais de HTML. Quando um componente é implementado com o objetivo de envolver um elemento-alvo ou outro componente, este comportamento nem sempre é o mais desejado. Ao especificar `inheritAttrs` para `false`, este comportamento padrão pode ser desabilitado. Os atributos estarão disponíveis pela propriedade de instância `$attrs` (também nova na versão 2.4) e podem ser explicitamente vinculados a um elemento que não seja a raíz utilizando `v-bind`.

  Nota: esta opção **não** afeta *bindings* de `class` e `style`.

### comments

> Novo em 2.4.0+

- **Tipo:** `boolean`

- **Padrão:** `false`

- **Restrições:** Esta opção só está disponível na *build* completa, com compilação no navegador.

- **Detalhes:**

  Quando especificada para `true`, esta opção irá preservar e renderizar comentários HTML encontrados nos _templates_. O comportamento padrão é descartá-los.

## Propriedades de Instância

### vm.$data

- **Tipo:** `Object`

- **Detalhes:**

  O objeto _data_ que a instância do Vue está observando. A instância do Vue provê acesso a estas propriedades no seu objeto *data*.

- **Ver também:** [Opções / Dados - data](#data)

### vm.$props

> Novo em 2.2.0+

- **Tipo:** `Object`

- **Detalhes:**

  Um objeto que representa as *props* atuais que um componente recebeu. A instância do Vue provê acesso a estas propriedades no seu objeto *props*.

### vm.$el

- **Tipo:** `HTMLElement`

- **Somente leitura**

- **Detalhes:**

  O elemento DOM raíz que a instância do Vue está gerenciando.

### vm.$options

- **Tipo:** `Object`

- **Somente leitura**

- **Detalhes:**

  As opções de instanciação usadas para a instância atual do Vue. Isto é útil quando se deseja incluir propriedades personalizadas nas opções:

  ``` js
  new Vue({
    customOption: 'foo',
    created: function () {
      console.log(this.$options.customOption) // => 'foo'
    }
  })
  ```

### vm.$parent

- **Tipo:** `Instância do Vue`

- **Somente leitura**

- **Detalhes:**

  A instância pai, se a instância atual contém uma.

### vm.$root

- **Tipo:** `Instância do Vue`

- **Somente leitura**

- **Detalhes:**

  A instância do Vue raíz da árvore do componente atual. Se a instância atual não tem uma instância pai, este valor vai ser ele mesmo.

### vm.$children

- **Tipo:** `Array<Instância do Vue>`

- **Somente leitura**

- **Detalhes:**

  Os componentes filhos diretos da instância atual. **Note que não há ordem garantida para `$children` e esta propriedade não é reativa.** Se você está tentando usar `$children`para *binding* de dados, considere usar um Array e `v-for` para gerar os componentes filhos e use o Array como a fonte da verdade.

### vm.$slots

- **Tipo:** `{ [name: string]: Array<VNode> }`

- **Read only**

- **Detalhes:**

  Usado para acessar programaticamente o conteúdo [distribuído pelos *slots*](../guide/components.html#Distribuicao-de-Conteudo-com-Slots). Cada [*slot* nomeado](../guide/components.html#Slots-Nomeados) tem sua propriedade correspondente (por exemplo, o conteúdo do `slot="foo"` poderá ser acessado via `vm.$slots.foo`). A propriedade `default` contém todo nó que não está inserido em nenhum *slot* nomeado.

  É bastante útil acessar `vm.$slots` quando se está escrevendo um componente com uma [função de renderização](../guide/render-function.html).

- **Exemplo:**

  ```html
  <blog-post>
    <h1 slot="header">
      Sobre mim
    </h1>

    <p>Aqui há conteúdo da página que será incluído em vm.$slots.default, pois não está dentro de nenhum slot nomeado.</p>

    <p slot="footer">
      Copyright 2016 Evan You
    </p>

    <p>Se eu tenho algum conteúdo aqui embaixo, este também será incluído em vm.$slots.default.</p>.
  </blog-post>
  ```

  ```js
  Vue.component('blog-post', {
    render: function (createElement) {
      var header = this.$slots.header
      var body   = this.$slots.default
      var footer = this.$slots.footer
      return createElement('div', [
        createElement('header', header),
        createElement('main', body),
        createElement('footer', footer)
      ])
    }
  })
  ```

- **Ver também:**
  - [Componente `<slot>`](#slot-1)
  - [Distribuição de conteúdo com Slots](../guide/components.html#Distribuicao-de-Conteudo-com-Slots)
  - [Funções de Renderização - Slots](../guide/render-function.html#Slots)

### vm.$scopedSlots

> Novo em 2.1.0+

- **Tipo:** `{ [name: string]: props => VNode | Array<VNode> }`

- **Somente leitura**

- **Detalhes:**

  Usado para acessar programaticamente [*scoped slots*](../guide/components.html#Scoped-Slots). Para cada *slot*, incluindo o *slot* `default`, o objeto contém uma função correspondente que retorna *VNodes*.

  É bastante útil acessar `vm.$scopedSlots` quando se está escrevendo um componente com uma [função de renderização](../guide/render-function.html).

- **Ver também:**
  - [Componente `<slot>`](#slot-1)
  - [Slot com Escopo](../guide/components.html#Slot-com-Escopo)
  - [Funções de Renderização - Slots](../guide/render-function.html#Slots)

### vm.$refs

- **Tipo:** `Object`

- **Somente leitura**

- **Detalhes:**

  Um objeto do elemento DOM que mantém componentes filhos que têm [ atributos `ref`](#ref) registrados.

- **Ver também:**
  - [Referências a Componentes Filhos](../guide/components.html#Referencias-a-Componentes-Filhos)
  - [Atributos Especiais - ref](#ref)

### vm.$isServer

- **Tipo:** `boolean`

- **Somente leitura**

- **Detalhes:**

  Retorna se a instância atual do Vue está sendo executada no servidor.

- **Ver também:** [Renderização do lado do servidor](../guide/ssr.html)

### vm.$attrs

- **Tipo:** `{ [key: string]: string }`

- **Somente leitura**

- **Detalhes:**

  Contém *bindings* de atributos do escopo do componente pai (exceto para `class` e `style`) que não são reconhecido como *props*. Quando um componente não tem nenhum *props* declarado, esta propriedade essencialmente contém todos os *bindings* do escopo do componente pai (exceto para `class` e `style`), e pode ser passada para os componentes internos via `v-bind="$attrs"` - útil quando se quer criar componentes de ordem superior.

### vm.$listeners

- **Tipo:** `{ [key: string]: Function | Array<Function> }`

- **Somente leitura**

- **Detalhes:**

  Contém ouvintes de eventos (*event listeners*) advindos de `v-on` do escopo do componente pai. Esta propriedade pode ser para os componentes internos via `v-on="$listeners"` - útil quando se quer criar componentes de ordem superior.


  Contém listeners de eventos `v-on` (sem os modificadores`.native`). Isso pode ser passado para um componente interno via `v-on="$ listeners"` - útil ao criar wrapper transparentes de componentes.

## Métodos da Instância / Dados

### vm.$watch( expOrFn, callback, [options] )

- **Argumentos:**
  - `{string | Function} expOrFn`
  - `{Function | Object} callback`
  - `{Object} [options]`
    - `{boolean} deep`
    - `{boolean} immediate`

- **Returns:** `{Function} unwatch`

- **Uso:**

  Observa por mudanças uma expressão ou uma função computada na instância do Vue. A função _callback_ é chamada com o novo valor e o antigo. A expressão somente aceita caminhos delimitados por pontos. Para expressões mais complexas, utilize uma função.

  <p class="tip">Nota: quando se está alterando (ao invés de substituindo) um objeto ou um Array, o valor antigo será igual ao novo valor, pois eles referenciam o mesmo objeto/Array. O Vue não mantém uma cópia dos valores antes de serem mutados.</p>

- **Exemplo:**

  ``` js
  // caminho da chave
  vm.$watch('a.b.c', function (newVal, oldVal) {
    // faz alguma coisa
  })

  // função
  vm.$watch(
    function () {
      return this.a + this.b
    },
    function (newVal, oldVal) {
      // faz alguma coisa
    }
  )
  ```

  `vm.$watch` retorna uma função *unwatch* que para de lançar o _callback_:

  ``` js
  var unwatch = vm.$watch('a', cb)
  // depois, destruirá o watcher
  unwatch()
  ```

- **Opção: deep**

  Para também detectar valores aninhados dentro de objetos, você deve passar `deep: true` nas opções. Note que você não precisa fazer isso para mutações em Array.

  ``` js
  vm.$watch('someObject', callback, {
    deep: true
  })
  vm.someObject.nestedValue = 123
  // callback é acionado
  ```

- **Opção: immediate**

  Passar `immediate: true` nas opções irá acionar a função de _callback_ imediatamente com o novo valor da expressão:

  ``` js
  vm.$watch('a', callback, {
    immediate: true
  })
  // `callback` é acionada imediatamente com o novo valor de 'a'
  ```

### vm.$set( target, key, value )

- **Argumentos:**
  - `{Object | Array} target`
  - `{string | number} key`
  - `{any} value`

- **Retorno:** o valor determinado.

- **Uso:**

  Esta método é um ***alias*** para a função global `Vue.set`.

- **Ver também:** [Vue.set](#Vue-set)

### vm.$delete( target, key )

- **Argumentos:**
  - `{Object | Array} target`
  - `{string | number} key`

- **Uso:**

  Este método é um ***alias*** para a função global `Vue.delete`.

- **Ver também:** [Vue.delete](#Vue-delete)

## Métodos da Instância / Eventos

### vm.$on( event, callback )

- **Argumentos:**
  - `{string | Array<string>} event` (array somente é suportado em 2.2.0+)
  - `{Function} callback`

- **Uso:**

  Escuta um evento personalizado na *vm* atual. Eventos podem ser lançados utilizando `vm.$emit`. A função de _callback_ irá receber todos os argumentos adicionais passados nos métodos que lançam eventos.

- **Exemplo:**

  ``` js
  vm.$on('test', function (msg) {
    console.log(msg)
  })
  vm.$emit('test', 'hi')
  // => "hi"
  ```

### vm.$once( event, callback )

- **Argumentos:**
  - `{string} event`
  - `{Function} callback`

- **Uso:**

  Escuta um evento personalizado, porém somente uma vez. A escuta será removida assim que for acionada pela primeira vez.

### vm.$off( [event, callback] )

- **Argumentos:**
  - `{string | Array<string>} event` (array somente é suportado em 2.2.2+)
  - `{Function} [callback]`

- **Uso:**

  Remove escutas de eventos personalizados.

  - Se nenhum argumento é passado, esta função irá remover todas as escutas;

  - Se apenas o evento é passado, esta função irá remover todas as escutas para este evento;

  - Se tanto o evento quanto o _callback_ forem passados, esta função somente irá remover a escuta para este _callback_ específico.

### vm.$emit( eventName, [...args] )

- **Argumentos:**
  - `{string} eventName`
  - `[...args]`

  Lança um evento na instância atual. Qualquer argumento adicional será passado para a função de _callback_ da escuta.

- **Exemplos:**

  Usando `$emit` com apenas um nome de evento:

  ```js
  Vue.component('welcome-button', {
    template: `
      <button v-on:click="$emit('welcome')">
        Me clique para receber um olá
      </button>
    `
  })
  ```
  ```html
  <div id="emit-example-simple">
    <welcome-button v-on:welcome="sayHi"></welcome-button>
  </div>
  ```
  ```js
  new Vue({
    el: '#emit-example-simple',
    methods: {
      sayHi: function () {
        alert('Olá!')
      }
    }
  })
  ```
  {% raw %}
  <div id="emit-example-simple" class="demo">
    <welcome-button v-on:welcome="sayHi"></welcome-button>
  </div>
  <script>
    Vue.component('welcome-button', {
      template: `
        <button v-on:click="$emit('welcome')">
          Me clique para receber um olá
        </button>
      `
    })
    new Vue({
      el: '#emit-example-simple',
      methods: {
        sayHi: function () {
          alert('Olá!')
        }
      }
    })
  </script>
  {% endraw %}

  Usando `$emit` com argumentos adicionais:

  ```js
  Vue.component('magic-eight-ball', {
    data: function () {
      return {
        possibleAdvice: ['Sim', 'Não', 'Talvez']
      }
    },
    methods: {
      giveAdvice: function () {
        var randomAdviceIndex = Math.floor(Math.random() * this.possibleAdvice.length)
        this.$emit('give-advice', this.possibleAdvice[randomAdviceIndex])
      }
    },
    template: `
      <button v-on:click="giveAdvice">
        Me clique para aconselhamento
      </button>
    `
  })
  ```

  ```html
  <div id="emit-example-argument">
    <magic-eight-ball v-on:give-advice="showAdvice"></magic-eight-ball>
  </div>
  ```

  ```js
  new Vue({
    el: '#emit-example-argument',
    methods: {
      showAdvice: function (advice) {
        alert(advice)
      }
    }
  })
  ```

  {% raw %}
  <div id="emit-example-argument" class="demo">
    <magic-eight-ball v-on:give-advice="showAdvice"></magic-eight-ball>
  </div>
  <script>
    Vue.component('magic-eight-ball', {
      data: function () {
        return {
          possibleAdvice: ['Sim', 'Não', 'Talvez']
        }
      },
      methods: {
        giveAdvice: function () {
          var randomAdviceIndex = Math.floor(Math.random() * this.possibleAdvice.length)
          this.$emit('give-advice', this.possibleAdvice[randomAdviceIndex])
        }
      },
      template: `
        <button v-on:click="giveAdvice">
          Me clique para aconselhamento
        </button>
      `
    })
    new Vue({
      el: '#emit-example-argument',
      methods: {
        showAdvice: function (advice) {
          alert(advice)
        }
      }
    })
  </script>
  {% endraw %}

## Métodos da Instância / Ciclo-de-Vida

### vm.$mount( [elementOrSelector] )

- **Argumentos:**
  - `{Element | string} [elementOrSelector]`
  - `{boolean} [hydrating]`

- **Returns:** `vm` - a própria instância

- **Uso:**

  Se a instância do Vue não recebeu a opção `el` durante a instanciação, ela estará em um estado "não montado" sem um elemento DOM associado. `vm.$mount()` pode ser usada para começar a montagem de uma instância não montada do Vue.

  Se o argumento `elementOrSelector` não for passado, o *template* será renderizado como um elemento "fora do documento" e você terá que usar a API nativa do DOM para inseri-lo no documento.

  O método retorna a própria instância para que você possa encadear outros métodos de instâncias depois dele.

- **Exemplo:**

  ``` js
  var MyComponent = Vue.extend({
    template: '<div>Hello!</div>'
  })

  // cria e monta em #app (irá substituir #app)
  new MyComponent().$mount('#app')

  // a linha acima é o mesmo que:
  new MyComponent({ el: '#app' })

  // ou renderiza como "fora do documento" e anexa depois:
  var component = new MyComponent().$mount()
  document.getElementById('app').appendChild(component.$el)
  ```

- **Ver também:**
  - [Diagrama do Ciclo de Vida](../guide/instance.html#Diagrama-do-Ciclo-de-Vida)
  - [Renderização no Lado do Servidor](../guide/ssr.html)

### vm.$forceUpdate()

- **Uso:**

  Força a instância do Vue a renderizar novamente. Note que isso não afeta todos os componentes filhos, apenas a própria instância e os componentes filhos com conteúdo em inseridos em seus respectivos *slots*.

### vm.$nextTick( [callback] )

- **Argumentos:**
  - `{Function} [callback]`

- **Uso:**

  Adia a execução do _callback_ para o próximo ciclo de atualização. Use esta função imediatamente após você alterar algum dado para esperar pela atualização do DOM. Este método é o mesmo que a função global `Vue.nextTick` exceto que o contexto do `this` no _callback_ é automaticamente vinculado (*bound*) à instância que chama este método.

  > Novo em 2.1.0+: retorna uma Promise se nenhum _callback_ é fornecido e Promise tiver suporte no ambiente de execução. Observe que Vue não vem com um _polyfill_ para Promise, portanto, se seu navegador alvo não oferece suporte a Promises (estamos olhando para você, IE), você precisará escolher um _polyfill_ por sua conta.

- **Exemplo:**

  ``` js
  new Vue({
    // ...
    methods: {
      // ...
      example: function () {
        // modifica um dado
        this.message = 'changed'
        // DOM ainda não está atualizada
        this.$nextTick(function () {
          // DOM agora está atualizada
          // `this` é vinculado (bound) à instância atual
          this.doSomethingElse()
        })
      }
    }
  })
  ```

- **Ver também:**
  - [Vue.nextTick](#Vue-nextTick)
  - [Fila de Atualização Assíncrona](../guide/reactivity.html#Fila-de-Atualizacao-Assincrona)

### vm.$destroy()

- **Uso:**

  Destrói completamente a *vm*. Limpa todas as suas conexões com outras *vms* e desvincula (*unbind*) todas as suas diretivas e desliga todas as escutas de eventos.

  Aciona os *hooks* `beforeDestroy` e `destroyed`.

  <p class="tip">Em casos normais você não deve chamar este método. Prefira controlar o ciclo de vida dos componentes filhos em um modelo direcionado a dados utilizando `v-if` e `v-for`.</p>

- **Ver também:** [Diagrama do Ciclo de Vida](../guide/instance.html#Diagrama-do-Ciclo-de-Vida)

## Diretivas

### v-text

- **Espera:** `string`

- **Detalhes:**

  Atualiza o `textContent` do elemento. Se você precisa atualizar a parte de `textContent`, você deve usar interpolações `{% raw %}{{ Mustache }}{% endraw %}`.

- **Exemplo:**

  ```html
  <span v-text="msg"></span>
  <!-- o mesmo que -->
  <span>{{msg}}</span>
  ```

- **Ver também:** [Sintaxe de Templates - Interpolações](../guide/syntax.html#Texto)

### v-html

- **Espera:** `string`

- **Detalhes:**

  Atualiza o `innerHTML` do elemento. **Perceba que os conteúdos são inseridos como HTML puro - eles não serão compilados como templates Vue**. Se você se pegar tentando compor templates usando `v-html`, tente repensar a solução usando componentes.

  <p class="tip">Renderizar dinamicamente HTML arbitrário em seu website pode ser muito perigoso porque isso pode facilmente levar a [ataques XSS](https://pt.wikipedia.org/wiki/Cross-site_scripting). Use `v-html` somente em conteúdo confiável e **nunca** em conteúdo fornecido por usuário.</p>

  <p class="tip">Em [Componentes Single-File](../guide/single-file-components.html), estilos `scoped` não serão aplicados a conteúdos `v-html`, pois tal HTML não é processado pelo compilador de _templates_ do Vue. Se você quer atingir conteúdo `v-html` com CSS com escopo, é possível utilizar [CSS Modules](https://vue-loader.vuejs.org/en/features/css-modules.html) ou um elemento global adicional `<style>`, com uma estratégia manual de escopo como BEM.</p>

- **Exemplo:**

  ```html
  <div v-html="html"></div>
  ```

- **Ver também:** [Sintaxe de Templates - Interpolações](../guide/syntax.html#HTML)

### v-show

- **Espera:** `any`

- **Uso:**

  Alterna a propriedade CSS `display` do elemento baseado na condição de verdade do valor da expressão.

  Esta diretiva dispara transições quando sua condição muda.

- **Ver também:** [Renderização Condicional - v-show](../guide/conditional.html#v-show)

### v-if

- **Espera:** `any`

- **Uso:**

  Renderiza condicionalmente o elemento baseado na condição de verdade do valor da expressão. O elemento e suas diretivas/componentes contidos são destruídos e reconstruídos durante alternâncias (_toggles_). Se o elemento é um `<template>`, seu conteúdo será extraído como o bloco condicional.

  Esta diretiva dispara transições quando sua condição muda.

  <p class="tip">Quando usada em conjunto com v-if, v-for tem uma prioridade maior. Veja o <a href="../guide/list.html#v-for-with-v-if">guia de Renderização de Listas</a> para detalhes.</p>

- **Ver também:** [Renderização Condicional - v-if](../guide/conditional.html)

### v-else

- **Não espera expressão**

- **Restrição:** o elemento-irmão anterior deve ter `v-if` ou `v-else-if`.

- **Uso:**

  Denota o "bloco senão" de uma cadeia `v-if` ou `v-if`/`v-else-if`.

  ```html
  <div v-if="Math.random() > 0.5">
    Agora você me vê
  </div>
  <div v-else>
    Agora não vê
  </div>
  ```

- **Ver também:** [Renderização Condicional - v-else](../guide/conditional.html#v-else)

### v-else-if

> Novo em 2.1.0+

- **Espera:** `any`

- **Restrição:** o elemento-irmão anterior deve ter `v-if` ou `v-else-if`.

- **Uso:**

  Denota o "bloco senão se" para `v-if`. Pode ser encadeado.

  ```html
  <div v-if="type === 'A'">
    A
  </div>
  <div v-else-if="type === 'B'">
    B
  </div>
  <div v-else-if="type === 'C'">
    C
  </div>
  <div v-else>
    Não A/B/C
  </div>
  ```

- **Ver também:** [Renderização Condicional - v-else-if](../guide/conditional.html#v-else-if)

### v-for

- **Espera:** `Array | Object | number | string`

- **Uso:**

  Renderiza o elemento ou bloco de template múltiplas vezes baseado nos dados de origem (_source data_). O valor da diretiva deve usar a sintaxe especial `alias in expression` para fornecer um alias para o elemento atual ser iterado em:

  ``` html
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```

  Alternativamente, você também pode especificar um alias para o índice (ou a chave, se usada em um Objeto):

  ``` html
  <div v-for="(item, index) in items"></div>
  <div v-for="(val, key) in object"></div>
  <div v-for="(val, key, index) in object"></div>
  ```

  O comportamento padrão de `v-for` tentará corrigir os elementos in-place sem os mover. Para forçá-lo a reordenar elementos, você precisa fornecer uma sugestão de ordenação com o atributo especial `key`:

  ``` html
  <div v-for="item in items" :key="item.id">
    {{ item.text }}
  </div>
  ```

  <p class="tip">Quando usada em conjunto com v-if, v-for tem uma prioridade maior. Veja o <a href="../guide/list.html#Utilizando-v-if-com-v-for">guia de Renderização de Listas</a> para detalhes.</p>

  O uso detalhado de `v-for` é explicado na seção de Guia com link abaixo.

- **Ver também:**
  - [Renderização de Listas](../guide/list.html)
  - [key](../guide/list.html#key)

### v-on

- **Forma abreviada:** `@`

- **Espera:** `Function | Inline Statement | Object`

- **Argumento:** `event`

- **Modificadores:**
  - `.stop` - chama `event.stopPropagation()`.
  - `.prevent` - chama `event.preventDefault()`.
  - `.capture` - adiciona escuta de eventos em modo de captura.
  - `.self` - aciona o manipulador somente se o evento foi disparado a partir deste elemento.
  - `.{keyCode | keyAlias}` - aciona o manipulador apenas em certas chaves.
  - `.native` - escuta por um evento nativo no elemento-raiz do componente.
  - `.once` - aciona o manipulador somente uma vez.
  - `.left` - (2.2.0+) aciona o manipulador somente para eventos do botão esquerdo do mouse.
  - `.right` - (2.2.0+) aciona o manipulador somente para eventos do botão direito do mouse.
  - `.middle` - (2.2.0+) aciona o manipulador somente para eventos do botão do meio do mouse.
  - `.passive` - (2.3.0+) atribui um evento ao DOM com `{ passive: true }`.

- **Uso:**

  Atribui uma escuta de evento ao elemento. O tipo de evento é denotado pelo argumento. A expressão pode ser um nome de método, uma declaração em linha ou omitida quando há modificadores presentes.

  Quando usada em um elemento normal, escuta somente [**eventos nativos de DOM**](https://developer.mozilla.org/en-US/docs/Web/Events). Quando usada em um componente de elemento personalizado, também escuta a **eventos personalizados** emitidos naquele componente-filho.

  Quando escutando a eventos nativos de DOM, o método recebe o evento nativo como argumento único. Se usando declaração em linha, a declaração tem acesso à propriedade especial `$event`: `v-on:click="handle('ok', $event)"`.

  A partir do 2.4.0+, `v-on` também suporte bind para um objeto de um event/listener sem argumentos. Note que quando usar a sintaxe de objetos, este não suporta nenhum modificador.

- **Examplo:**

  ```html
  <!-- manipulador de método -->
  <button v-on:click="doThis"></button>

  <!-- sintaxe de objeto (2.4.0+) -->
  <button v-on="{ mousedown: doThis, mouseup: doThat }"></button>

  <!-- declaração em linha -->
  <button v-on:click="doThat('hello', $event)"></button>

  <!-- forma abreviada -->
  <button @click="doThis"></button>

  <!-- para a propagação -->
  <button @click.stop="doThis"></button>

  <!-- previne padrão -->
  <button @click.prevent="doThis"></button>

  <!-- previne padrão sem expressão -->
  <form @submit.prevent></form>

  <!-- modificadores em cadeia -->
  <button @click.stop.prevent="doThis"></button>

  <!-- modificador de chave usando keyAlias -->
  <input @keyup.enter="onEnter">

  <!-- modificador de chave usando keyCode -->
  <input @keyup.13="onEnter">

  <!-- o evento de clique será acionado somente uma vez -->
  <button v-on:click.once="doThis"></button>

  <!-- object syntax (2.4.0+) -->
  <button v-on="{ mousedown: doThis, mouseup: doThat }"></button>
  ```

  Escutando a eventos personalizados em um componente-filho (o manipulador é chamado quando "my-event" é emitido no filho):

  ```html
  <my-component @my-event="handleThis"></my-component>

  <!-- declaração em linha -->
  <my-component @my-event="handleThis(123, $event)"></my-component>

  <!-- evento nativo no componente -->
  <my-component @click.native="onClick"></my-component>
  ```

- **Ver também:**
  - [Manipulação de Eventos](../guide/events.html)
  - [Componentes - Eventos Personalizados](../guide/components.html#Eventos-Personalizados)

### v-bind

- **Forma abreviada:** `:`

- **Espera:** `any (sem argumento) | Object (sem argumento)`

- **Argumento:** `attrOrProp (opcional)`

- **Modificadores:**
  - `.prop` - Interliga como uma propriedade DOM ao invés de um atributo ([qual a diferença?](https://stackoverflow.com/questions/6003819/properties-and-attributes-in-html#answer-6004028)). Se a _tag_ é um componente, então `.prop` irá definir a propriedade no `$el` do componente.
  - `.camel` - (2.1.0+) transforma o nome do atributo de kebab-case para camelCase.
  - `.sync` - (2.3.0+) um "açúcar sintático" que se expande em um `v-on` para atualizar o valor vinculado.

- **Uso:**

  Dinamicamente faz a interligação de um ou mais atributos ou propriedades de um componente a uma expressão.

  Quando usado para o vínculo de atributos `class` ou `style`, suporta tipos de valores adicionais como Array ou Objects. Veja na seção do Guia abaixo para mais detalhes.

  Quando usado para vincular uma propriedade, a propriedade deve ser devidamente declarada no elemento-filho.

  Quando usado sem argumentos, pode ser utilizado para vincular em um objeto contendo pares nome-valor. Perceba que, neste modo, `class` e `style` não suportam Array ou Objects.

- **Exemplo:**

  ```html
  <!-- interligando a um atributo -->
  <img v-bind:src="imageSrc">

  <!-- forma abreviada -->
  <img :src="imageSrc">

  <!-- com concatenação de String no local -->
  <img :src="'/path/to/images/' + fileName">

  <!-- interligando classes -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]">

  <!-- interligando estilos -->
  <div :style="{ fontSize: size + 'px' }"></div>
  <div :style="[styleObjectA, styleObjectB]"></div>

  <!-- interligando um objeto com atributos -->
  <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

  <!-- interligando atributo DOM com modificador de propriedade -->
  <div v-bind:text-content.prop="text"></div>

  <!-- interligando propriedade. "prop" deve existir em my-component -->
  <my-component :prop="someThing"></my-component>

  <!-- transmite todas as props do pai em comum com o componente-filho -->
  <child-component v-bind="$props"></child-component>

  <!-- XLink -->
  <svg><a :xlink:special="foo"></a></svg>
  ```

  O modificador `.camel` permite colocar na notação _camelCase_ (_camelizing_) um nome do atributo `v-bind` quando usando em _templates_ no DOM, por exemplo, o atributo `viewBox` de um SVG:

  ``` html
  <svg :view-box.camel="viewBox"></svg>
  ```

  `.camel` não é necessário se você está usando _templates_ baseados em String ou compilando com `vue-loader`/`vueify`.

- **Ver também:**
  - [Interligações em Classes e Estilos](../guide/class-and-style.html)
  - [Componentes - Propriedades](../guide/components.html#Propriedades)
  - [Componentes - O Modificador `.sync`](../guide/components.html#O-Modificador-sync)

### v-model

- **Espera:** varia baseado no valor do elemento de input de formulário ou saída de componentes

- **Limitado a:**
  - `<input>`
  - `<select>`
  - `<textarea>`
  - componentes

- **Modificadores:**
  - [`.lazy`](../guide/forms.html#lazy) - escuta por eventos `change` ao invés de `input`
  - [`.number`](../guide/forms.html#number) - faz a conversão da String informada para números
  - [`.trim`](../guide/forms.html#trim) - faz _trim_ dos dados informados

- **Uso:**

  Cria uma interligação de mão dupla (_two-way binding_) em um elemento de entrada (_input_) de formulário ou componente. Para uso detalhado e outras observações, veja o link abaixo para a seção do Guia.

- **Ver também:**
  - [Interligações em Formulários](../guide/forms.html)
  - [Componentes - Componentes de Formulários usando Eventos Personalizados](../guide/components.html#Componentes-de-Formularios-usando-Eventos-Personalizados)

### v-pre

- **Não espera expressão**

- **Uso:**

  Pula a compilação para esse elemento e todos seus filhos. Você pode usar isso para mostrar tags mustache sem conversão (_raw_). Pular uma grande quantidade de nós sem diretivas pode acelerar a compilação.

- **Exemplo:**

  ```html
  <span v-pre>{{ isso não será compilado }}</span>
   ```

### v-cloak

- **Não espera expressão**

- **Uso:**

  Essa diretiva permanecerá no elemento até que a instância associada de Vue termine de compilar. Ao ser combinada com regras CSS como `[v-cloak] { display: none }`, essa diretiva pode ser usada para esconder interligações mustache não-compiladas até que a instância de Vue esteja pronta.

- **Exemplo:**

  ```css
  [v-cloak] {
    display: none;
  }
  ```

  ```html
  <div v-cloak>
    {{ mensagem }}
  </div>
  ```

  A `<div>` não ficará visível até que a compilação tenha terminado.

### v-once

- **Não espera expressão**

- **Detalhes:**

  Renderiza o elemento e componente **somente uma vez**. Em re-renderizações subsequentes, o elemento/componente e todos seus filhos serão tratados como conteúdo estático e pulados. Isso pode ser usado para otimizar o desempenho da atualização.

  ```html
  <!-- elemento único -->
  <span v-once>Isso nunca vai mudar: {{msg}}</span>
  <!-- o elemento tem filhos -->
  <div v-once>
    <h1>comentário</h1>
    <p>{{msg}}</p>
  </div>
  <!-- componente -->
  <my-component v-once :comment="msg"></my-component>
  <!-- diretiva v-for -->
  <ul>
    <li v-for="i in list" v-once>{{i}}</li>
  </ul>
  ```

- **Ver também:**
  - [Sintaxe de Templates - Interpolações](../guide/syntax.html#Texto)
  - [Componentes - Componentes Estáticos Leves com v-once](../guide/components.html#Componentes-Estaticos-Leves-com-v-once)

## Atributos Especiais

### key

- **Espera:** `number | string`

  O atributo especial `key` é usado principalmente como um aviso para o algoritmo do virtual DOM do Vue identificar VNodes quando comparar a nova lista de nós com a anterior. Sem `keys`, o Vue usa um algoritmo que minimiza a movimentação de elementos e tenta corrigir/reusar elementos do mesmo tipo no local tanto quanto possível. Com `keys`, ele irá reordenar os elementos baseado na ordem de mudança das `keys` e elementos com `keys`, que não estão mais presentes, serão sempre removidos/destruídos.

  Componentes filhos com pai em comum precisam ter **keys únicas**. `keys` duplicadas causarão erros de renderização.

  O caso mais comum é combinado com o `v-for`:

  ``` html
  <ul>
    <li v-for="item in items" :key="item.id">...</li>
  </ul>
  ```

  Também pode ser usado para forçar a substituição de um elemento/componente ao invés de reusa-lo. Isso pode ser útil quando você quiser:

  - Ativar corretamente os hooks do ciclo de vida de um componente.
  - Ativar transições.

  Por exemplo:

  ``` html
  <transition>
    <span :key="text">{{ text }}</span>
  </transition>
  ```

  Quanto `text` mudar, o `<span>` será substituído em vez de alterado e então a transição será ativada.

### ref

- **Espera:** `string`

  `ref` é usada para registrar a referência para um elemento ou um componente filho. A referência será registrada no objeto `$refs` do componente pai. Se usada em um elemento normal do DOM, a referência será aquele elemento; Se usada em um componente filho, a referência será a instância do componente:

  ``` html
  <!-- vm.$refs.p será o nó do DOM-->
  <p ref="p">Olá</p>

  <!-- vm.$refs.child será a instância do componente filho -->
  <child-component ref="child"></child-component>
  ```

  Quando usada em elementos/componentes com `v-for`, a referência registrada será um Array contendo nós do DOM ou instâncias de componentes.

  Uma observação importante sobre o tempo do registro de `ref`: como as `refs` são criadas como resultados da função de renderização, você não pode acessá-las na renderização inicial - elas ainda não existem! `$refs` são não-reativas, portanto você não deve tentar usá-las em templates para *data-binding*.

- **Ver também:** [Referencias a Componentes Filhos](../guide/components.html#Referencias-a-Componentes-Filhos)

### slot

- **Espera:** `string`

  Usado dentro de conteúdos inseridos no componente filho para indicar a qual *named slot* o conteúdo pertence.

  Para maiores detalhes de uso, veja a sessão no guia do link abaixo:

- **Ver também:** [Slots Nomeados](../guide/components.html#Slots-Nomeados)

### slot-scope

> Novo em 2.5.0+

- **Espera:** `function argument expression`

- **Uso:**

Usado para denotar um elemento ou componente como um slot de escopo. O valor do atributo deve ser uma expressão de JavaScript válida que pode aparecer na posição do argumento de uma assinatura de função. Isso significa que, em ambientes suportados, você também pode usar a desestruturação ES2015 na expressão. Serve como um substituto para [`scope`](#scope-replaced) em 2.5.0+.

  Este atributo não suporta ligação dinâmica.

- **Ver também:** [Slot com Escopo](../guide/components.html#Slot-com-Escopo)

### scope <sup>replaced</sup>

Usado para denotar um elemento `<template>` como um slot de escopo que foi substituído por [`slot-scope`](#slot-scope) em 2.5.0+.

- **Uso:**

  O mesmo que [`slot-scope`](#slot-scope) exceto que `scope` somente pode ser utilizado em elementos `<template>`.

### is

- **Espera:** `string | Object (de opções do componente)`

  Usado para [Componentes Dinâmicos](../guide/components.html#Componentes-Dinamicos) e para trabalhar [limitações dos templates do DOM](../guide/components.html#Cuidados-com-o-Uso-no-DOM).

  Por exemplo:

  ``` html
  <!-- component é alterado quando o currentView é alterado -->
  <component v-bind:is="currentView"></component>

  <!-- necessário, porque <my-row> seria inválido dentro -->
  <!-- do elemento <table> e seria içado para fora (hoisted out) -->
  <table>
    <tr is="my-row"></tr>
  </table>
  ```

  Para maiores detalhes de uso, veja os links na descrição abaixo.

- **Ver também:**
  - [Componentes Dinâmicos](../guide/components.html#Componentes-Dinamicos)
  - [Cuidados com o Uso no DOM](../guide/components.html#Cuidados-com-o-Uso-no-DOM)

## Componentes Integrados

### component

- **Props:**
  - `is` - string | ComponentDefinition | ComponentConstructor
  - `inline-template` - boolean

- **Uso:**

  Um "componente meta" para renderizar componentes dinâmicos. O componente real a renderizar é determinado pelo propriedade `is`:

  ```html
  <!-- um componente dinâmico controlado -->
  <!-- pela propriedade `componentId` na vm -->
  <component :is="componentId"></component>

  <!-- pode também renderizar um componente registrado ou componente passado como propriedade -->
  <component :is="$options.components.child"></component>
  ```

- **Ver também:** [Componentes Dinâmicos](../guide/components.html#Componentes-Dinamicos)

### transition

- **Propriedades:**
  - `name` - string. Usado para gerar automaticamente nomes de classes CSS de transição. Por exemplo, `name: 'fade'` expandirá automaticamente para `.fade-enter`, `.fade-enter-active` e etc. Por padrão, `"v"` (`v-enter`, `v-enter-active`).
  - `appear` - boolean. Usado para aplicar a transição na renderização inicial. Por padrão, `false`.
  - `css` - boolean. Usado para aplicar classes de transição CSS. Padrão para  `true`. Se configurado para `false`, apenas irá desencadear gatilhos registrado no JavaScript por meio de eventos de componentes.
  - `type` - string. Especifica os tipos de eventos de transição à aguardar a especificação do tempo final da transição. Valores disponíveis são `"transition"` e `"animation"`. Por padrão, será detectado automaticamente o tipo que tenha uma duração mais longa.
  - `mode` - string. Controla a sequência de temporização das transições de saída / entrada. Modos disponíveis são `"out-in"` e `"in-out"`. Por padrão, simultaneamente.
  - `enter-class` - string
  - `leave-class` - string
  - `appear-class` - string
  - `enter-to-class` - string
  - `leave-to-class` - string
  - `appear-to-class` - string
  - `enter-active-class` - string
  - `leave-active-class` - string
  - `appear-active-class` - string

- **Eventos:**
  - `before-enter`
  - `before-leave`
  - `before-appear`
  - `enter`
  - `leave`
  - `appear`
  - `after-enter`
  - `after-leave`
  - `after-appear`
  - `enter-cancelled`
  - `leave-cancelled` (`v-show` somente)
  - `appear-cancelled`

- **Uso:**

  `<transition>` servem como efeitos de transição para elemento/componente **único**. O `<transition>` aplica apenas o comportamento de transição para o conteúdo envolvido dentro; Ele não processa um elemento DOM extra ou aparece na hierarquia dos componentes inspecionados.

  ```html
  <!-- simple element -->
  <transition>
    <div v-if="ok">conteúdo alternado</div>
  </transition>

  <!-- dynamic component -->
  <transition name="fade" mode="out-in" appear>
    <component :is="view"></component>
  </transition>

  <!-- event hooking -->
  <div id="transition-demo">
    <transition @after-enter="transitionComplete">
      <div v-show="ok">conteúdo alternado</div>
    </transition>
  </div>
  ```

  ``` js
  new Vue({
    ...
    methods: {
      transitionComplete: function (el) {
        // para o 'el' passado que é um elemento do DOM como um argumento
      }
    }
    ...
  }).$mount('#transition-demo')
  ```

- **Ver também:** [Transições: Transicao-de-Entrada-Saida-de-Lista](../guide/transitions.html#Transicao-de-Entrada-Saida-de-Lista)

### transition-group

- **Propriedades:**
  - `tag` - string. Padrão para `span`.
  - `move-class` - Substituí a classe CSS aplicada durante a transição em movimento.
  - Expõe as mesmas propriedades que `<transition>` exceto `mode`.

- **Eventos:**
  - Expõe os mesmos eventos que `<transition>`.

- **Uso:**

  `<transition-group>` serve como efeito de transição para **múltiplos** elementos/componentes. O `<transition-group>` renderiza um elemento DOM real. Por padrão, renderiza-se um `<span>`, e você pode configurar qual elemento deverá ser renderizado através do atributo `tag`.

  Note que cada filho em um `<transition-group>` deve ser **identificado unicamente** para as animações funcionarem corretamente.

  `<transition-group>` suporta transições em movimento via transformações CSS. Quando a posição de um filho na tela muda após uma atualização, ele aplicará uma classe CSS de movimento (gerada automaticamente a partir do atributo `name` ou configurada com o atributo `move-class`). Se a propriedade CSS `transform` for passível de transição quando a classe de movimento é aplicada, o elemento será animado de forma suave para seu destino usando a [técnica FLIP](https://aerotwist.com/blog/flip-your-animations/).

  ```html
  <transition-group tag="ul" name="slide">
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
    </li>
  </transition-group>
  ```

- **Ver também:** [Transições: Transicao-de-Entrada-Saida-de-Lista](../guide/transitions.html#Transicao-de-Entrada-Saida-de-Lista)

### keep-alive

- **Propriedades:**
  - `include` - string ou RegExp ou Array. Somente os componentes correspondentes serão armazenados em cache.
  - `exclude` - string ou RegExp ou Array. Qualquer componente compatível com isso não será armazenado em cache.

- **Uso:**

  Quando enrolado em torno de um componente dinâmico, `<keep-alive>` armazena as instâncias do componente inativo sem destruí-las. Semelhante a `<transition>`, `<keep-alive>` é um componente abstrato: ele não processa um próprio elemento DOM e não aparece na cadeia principal do componente.

  Quando um componente é alternado dentro de `<keep-alive>`, os hooks de ciclo de vida 'ativados' e 'desativados' serão invocados de acordo.

  > Em 2.2.0+, "activated" e "deactivated" dispararão para todos os componentes aninhados dentro de uma árvore `<keep-alive>`.

 Principalmente usado com preservar o estado do componente ou evitar re-renderização.

  ```html
  <!-- básico -->
  <keep-alive>
    <component :is="view"></component>
  </keep-alive>

  <!-- filhos com multi condicional -->
  <keep-alive>
    <comp-a v-if="a > 1"></comp-a>
    <comp-b v-else></comp-b>
  </keep-alive>

  <!-- usado junto com `<transition>` -->
  <transition>
    <keep-alive>
      <component :is="view"></component>
    </keep-alive>
  </transition>
  ```

  Nota: `<keep-alive>` é projetado para o caso em que ele possui um componente filho direto que está sendo alternado. Isso não funciona se você tiver "v-for" dentro dele. Quando há múltiplos filhos condicionais, como acima, `<keep-alive>` exige que apenas uma criança seja renderizada de cada vez.

- **`include` e `exclude`**

  > Novo em 2.1.0+

Os acessórios `include` e `exclude` permitem que os componentes sejam armazenados em cache condicionalmente. Ambas as propriedades podem ser uma string delimitada por vírgulas, um RegExp ou uma Array:

  ``` html
  <!-- string delimitada por vírgulas -->
  <keep-alive include="a,b">
    <component :is="view"></component>
  </keep-alive>

  <!-- regex (usando `v-bind`) -->
  <keep-alive :include="/a|b/">
    <component :is="view"></component>
  </keep-alive>

  <!-- Array (usando `v-bind`) -->
  <keep-alive :include="['a', 'b']">
    <component :is="view"></component>
  </keep-alive>
  ```

A correspondência é verificada pela primeira vez na opção `name` do componente, em seguida, pelo nome no registro local (a chave dentro das opções do `components` pai) se a opção `name` não estiver disponível. Os componentes anônimos não podem ser comparados.

  <p class="tip">`<keep-alive>` não funciona com componentes funcionais porque eles não têm instâncias a serem armazenadas em cache.</p>

- **Ver também:** [Componentes Dinâmicos - keep-alive](../guide/components.html#keep-alive)

### slot

- **Propriedades:**
  - `name` - string. Usado para o slot nomeado.

- **Uso:**

  `<slot>` servem como pontos de distribuição de conteúdo em modelos de componentes. O `<slot>` em si será substituído.

 Para um uso detalhado, veja a seção do guia ligada abaixo.

- **Ver também:** [Distribuição de Conteúdo com Slots](../guide/components.html#Distribuicao-de-Conteudo-com-Slots)

## Interface VNode

- Por favor, consulte a [declaração da classe VNode](https://github.com/vuejs/vue/blob/dev/src/core/vdom/vnode.js).

## Renderização no Lado do Servidor

- Por favor, consulte a [documentação do pacote vue-server-renderer](https://ssr.vuejs.org/en/api.html).
