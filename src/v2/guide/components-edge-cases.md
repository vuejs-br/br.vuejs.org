---
title: Lidando com Casos Extremos
type: guide
order: 106
---

> Esta página assume que você já leu o [Básico sobre Componentes](components.html). Leia lá primeiro se você for novo com componentes.

<p class="tip">Todos os recursos desta página documentam o tratamento de casos extremos, ou seja, situações incomuns que, algumas vezes, requerem que as regras do Vue sejam brevemente contornadas. Entretanto, note que todos eles possuem desvantagens ou podem ser perigosos. Estes detalhes são expostos em cada caso, portanto, lembre-se ao decidir usar cada recurso.</p>

## Acesso a Elementos e Componentes

Geralmente, é melhor evitar alcançar outras instâncias de componentes ou manualmente manipular elementos DOM. Entretanto, às vezes pode ser apropriado.

### Acessando Instância Raiz

Em cada subcomponente de uma instância `new Vue`, a instância raiz pode ser acessada com a propriedade `$root`. Por exemplo, nessa instância raiz:

```js
// A instância raiz do Vue
new Vue({
  data: {
    foo: 1
  },
  computed: {
    bar: function () { /* ... */ }
  },
  methods: {
    baz: function () { /* ... */ }
  }
})
```

Todos os subcomponentes são capazes de acessá-la e usá-la como um _store_ global:

```js
// Recupera dados da raiz
this.$root.foo

// Atribui dados na raiz
this.$root.foo = 2

// Acessa dados computados da raiz
this.$root.bar

// Chama métodos da raiz
this.$root.baz()
```

<p class="tip">Isso pode ser conveniente para demonstrações ou aplicações muito pequenas, com apenas um punhado de componentes. Porém, o padrão não se adapta bem a aplicações de média ou larga escala, de modo que nós recomendamos fortemente a utilização do <a href="https://github.com/vuejs/vuex">Vuex</a> para gerenciar o estado, na maioria dos casos.</p>

### Acessando Instância do Componente Pai

Semelhantemente a `$root`, a propriedade `$parent` pode ser utilizada para acessar a instância pai, a partir de um componente filho. Isso é uma alternativa, preguiçosa e tentadora, ao invés de passar os dados via propriedades.

<p class="tip">Na maioria dos casos, alcançar o interior dos componentes pais torna sua aplicação muito difícil de depurar e entender, especiamente se você modifica dados no componente pai. Ao olhar para o componente posteriormente, será muito difícil de compreender onde aquela mutação de dados se originou.</p>

Entretanto, há casos, particulamente em bibliotecas compartilhadas, em que isso _pode_ ser apropriado. Por exemplo, componentes abstratos que interagem com APIs JavaScript em vez de renderizarem HTML, como esse componente hipotético do Google Maps:

```html
<google-map>
  <google-map-markers v-bind:places="iceCreamShops"></google-map-markers>
</google-map>
```

O componente `<google-map>` pode definir uma propriedade `map` que todos os subcomponentes precisam acessar. Neste caso, o componente `<google-map-markers>` pode acessar aquele mapa através de algo como `this.$parent.getMap`, para poder adicionar alguns marcadores à ela. Você pode conferir esse padrão [em ação aqui](https://jsfiddle.net/chrisvfritz/ttzutdxh/).

Tenha em mente, no entanto, que componentes construídos com esse padrão são inerentemente frágeis. Por exemplo, imagine que nós adicionaremos um novo componente `<google-map-region>` e quando `<google-map-markers>` aparecer dentro dele, ele só deve renderizar marcadores que estiverem nessa região:

```html
<google-map>
  <google-map-region v-bind:shape="cityBoundaries">
    <google-map-markers v-bind:places="iceCreamShops"></google-map-markers>
  </google-map-region>
</google-map>
```

Para solucionar, dentro de `<google-map-markers>` você poderá se encontrar confiando em um _hack_ como esse:

```js
var map = this.$parent.map || this.$parent.$parent.map
```

O que rapidamente foge do controle. É por isso que, para fornecer informação de contexto para os componentes descendentes arbitrariamente profundos, nós recomendamos a [injeção de dependência](#Injecao-de-Dependencia).

### Acessando Instâncias de Componentes e Elementos Filhos

Apesar da existência de propriedades e eventos, algumas vezes você ainda pode precisar acessar diretamente um componente filho em JavaScript. Para isso, você pode atribuir um identificador de referência ao componente filho, usando o atributo `ref`. Por exemplo:

```html
<base-input ref="usernameInput"></base-input>
```

Agora, no componente onde você definiu o `ref`, você pode usar:

```js
this.$refs.usernameInput
```

Assim, você acessará a instância `<base-input>`. Isso pode ser útil quando quiser, por exemplo, "focar" o _input_ programaticamente a partir do pai. O `<base-input>` pode, similarmente, usar `ref` para prover acesso a elementos específicos dentro dele, como:

```html
<input ref="input">
```

E até mesmo definir métodos para serem utilizados pelo pai:

```js
methods: {
  // Usado pelo pai para focar o input
  focus: function () {
    this.$refs.input.focus()
  }
}
```

Permitindo, assim, que o componente pai foque o _input_ dentro de `<base-input>` com:

```js
this.$refs.usernameInput.focus()
```

Quando o `ref` for usado junto com `v-for`, a referência que você obterá será um Array contendo os componentes filhos, espelhando a fonte de dados.

<p class="tip"><code>$refs</code> são populados somente depois que o componente foi renderizado, e não são reativos. É somente uma escotilha para manipulação direta dos filhos - evite usar <code>$refs</code> de dentro de _templates_ ou de dados computados.</p>

### Injeção de Dependência

Anteriormente, quando descrevemos [Acessando Instância do Componente Pai](#Acessando-Instancia-do-Componente-Pai), mostramos um exemplo como esse:

```html
<google-map>
  <google-map-region v-bind:shape="cityBoundaries">
    <google-map-markers v-bind:places="iceCreamShops"></google-map-markers>
  </google-map-region>
</google-map>
```

Neste componente, todos os descendentes de `<google-map>` necessitam do acesso ao método `getMap`, para saberem com qual mapa estão interagindo. Infelizmente, usando a propriedade `$parent`, não escalaremos muito bem para o caso de vários componentes profundamente aninhados. É aí que a injeção de dependência pode ser útil, usando duas novas opções da instância: `provide` e `inject`.

A opção `provide` permite especificar quais dados/métodos queremos **prover** para os componentes descendentes. Neste caso, o método `getMap` de `<google-map>`:

```js
provide: function () {
  return {
    getMap: this.getMap
  }
}
```

Então em qualquer descendente, podemos usar a opção `inject` para receber as propriedades específicas que desejamos adicionar à instância:

```js
inject: ['getMap']
```

Você pode ver o [exemplo completo](https://jsfiddle.net/chrisvfritz/tdv8dt3s/). A vantagem neste caso, em contraste com a utilização do `$parent`, é que podemos acessar `getMap` em _qualquer_ componente descendente, sem expor a instância inteira do `<google-map>`. Isso nos permite continuar o desenvolvimento desse componente com mais segurança, sem medo de que acabemos por alterar/remover algo que o componente filho esteja esperando. A interface entre esses componentes permanece claramente definida, assim como quando usamos `props`.

Na verdade, você pode pensar sobre a injeção de dependência como uma espécie de "propriedade de longo alcance", exceto por:

* ancestrais não precisarem saber quais descendentes usam a propriedade
* descendentes não precisarem saber de onde as propriedades injetadas vieram

<p class="tip">Entretanto, existem desvantagens na injeção de dependência. Ela associa os componentes de sua aplicação conforme estão organizados atualmente, tornando a refatoração mais difícil. Propriedades providas pelos componentes pais também não são reativas. Isso ocorre propositalmente, pois usá-las para criar um _store_ de dados central fará sua aplicação escalar tão mal quanto <a href="#Acessando-Instancia-Raiz">usando <code>$root</code></a> para o mesmo propósito. Se as propriedades que você deseja compartilhar são específicas para sua aplicação, em vez de genéricas, ou se você deseja sempre atualizar os dados providos dentro de componentes ancestrais, então é um bom sinal que você provavelmente precisa de uma solução real de gerenciamento de estado, como o <a href="https://github.com/vuejs/vuex">Vuex</a>.</p>

Aprenda mais sobre injeção de dependência na [documentação da API](https://vuejs.org/v2/api/#provide-inject).

## Escuta de Eventos Programática

Até então, você viu usos do `$emit` e escutou eventos com o `v-on`, porém instâncias Vue também oferecem outros métodos em sua interface de eventos. Nós podemos:

- Escutar um evento com `$on(eventName, eventHandler)`
- Escutar um evento, uma única vez, com `$once(eventName, eventHandler)`
- Deixar de escutar um evento com `$off(eventName, eventHandler)`

Normalmente, você não terá que usar nenhum desses métodos, porém eles estão disponíveis para casos em que você precise escutar, manualmente, eventos na instância de um componente. Eles também podem ser úteis como uma ferramenta para organização de código. Por exemplo, você pode notar frequentemente o padrão abaixo para integração com uma biblioteca de terceiros:

```js
// Anexe o datepicker ao input uma única vez
// que ele estiver montado no DOM.
mounted: function () {
  // Pikaday é uma biblioteca datepicker de terceiros
  this.picker = new Pikaday({
    field: this.$refs.input,
    format: 'YYYY-MM-DD'
  })
},
// Imediatamente antes do componente ser destruído,
// garante que o datepicker também o seja.
beforeDestroy: function () {
  this.picker.destroy()
}
```

Essa abordagem traz dois problemas em potencial:

- Demanda que o `picker` seja salvo na instância do componente, enquanto é possível que somente os gatilhos de ciclos de vida precisem acessá-lo. Não se trata de uma abordagem terrível, mas pode ser considerada confusa.
- Nosso código de configuração ou _setup_ é mantido separado do nosso código de limpeza, fazendo com que seja mais difícil de, programaticamente, limpar qualquer coisa que tenhamos configurado.

Pode-se resolver ambos os problemas com uma escuta de eventos programática:

```js
mounted: function () {
  var picker = new Pikaday({
    field: this.$refs.input,
    format: 'YYYY-MM-DD'
  })

  this.$once('hook:beforeDestroy', function () {
    picker.destroy()
  })
}
```

Usando essa estratégia, podemos até mesmo usar o Pickaday com vários elementos _input_, com cada nova instância automaticamente se registrando para limpeza.

```js
mounted: function () {
  this.attachDatepicker('startDateInput')
  this.attachDatepicker('endDateInput')
},
methods: {
  attachDatepicker: function (refName) {
    var picker = new Pikaday({
      field: this.$refs[refName],
      format: 'YYYY-MM-DD'
    })

    this.$once('hook:beforeDestroy', function () {
      picker.destroy()
    })
  }
}
```

Veja [este _fiddle_](https://jsfiddle.net/chrisvfritz/1Leb7up8/) com o código completo. Entretanto, note que, caso esteja precisando fazer muito _setup_ e limpeza em um único componente, a melhor solução será, usualmente, criar componentes mais modulares. Neste caso, recomendaríamos criar um componente `<input-datepicker>` reutilizável.

Para aprender mais sobre escutas de eventos programáticas, dê uma conferida na API de [Métodos de Instância de Eventos](https://vuejs.org/v2/api/#Instance-Methods-Events).

<p class="tip">Observe que o sistema de eventos do Vue é diferente do sistema utilizado pelo navegador, a <a href="https://developer.mozilla.org/pt-BR/docs/Web/API/EventTarget">API EventTarget</a>. Embora eles funcionem de modo semelhante, <code>$emit</code>, <code>$on</code> e <code>$off</code> <strong>não</strong> são apelidos para <code>dispatchEvent</code>, <code>addEventListener</code> e <code>removeEventListener</code>.</p>

## Referências Circulares 

### Componentes Recursivos

Componentes podem invocar a si mesmos recursivamente, em seu próprio `template`. Todavia, eles só podem fazer isso com a opção `name`:

``` js
name: 'unique-name-of-my-component'
```

Quando se registra um componente globalmente com `Vue.component`, o identificador global é automaticamente configurado como a opção `name`.

``` js
Vue.component('unique-name-of-my-component', {
  // ...
})
```

Se você não for cuidadoso, componentes recursivos também podem levar a _loops_ infinitos de renderização:

``` js
name: 'stack-overflow',
template: '<div><stack-overflow></stack-overflow></div>'
```

Um componente como o mostrado acima resultará e um erro _"max stack size exceeded"_, então garanta que a invocação recursiva é condicional (ou seja, sempre tenha um `v-if` que virá a ser, eventualmente, `false`).

### Entre Componentes

Digamos que você está construindo uma árvore de diretórios de arquivos, como em um Finder ou Explorer. Você pode ter um componente `tree-folder` com este _template_:

``` html
<p>
  <span>{{ folder.name }}</span>
  <tree-folder-contents :children="folder.children"/>
</p>
```

Então, um componente `tree-folder-contents`, com este _template_:

``` html
<ul>
  <li v-for="child in children">
    <tree-folder v-if="child.children" :folder="child"/>
    <span v-else>{{ child.name }}</span>
  </li>
</ul>
```

Quando se olha mais de perto, observa-se que tais componentes serão, na verdade, descendente e ancestral um do outro na árvore de renderização - um paradóxo! Quando for registrar esses componentes globalmente com `Vue.component`, tal paradóxo será resolvido automaticamente. Se este é o seu caso, você pode parar sua leitura aqui.

Todavia, se você está usando `require/import` de componentes com um __sistema de módulos__, como Webpack ou Browserify, irá se deparar com um erro:

```
Failed to mount component: template or render function not defined.
```

Para explicar o que ocorre, vamos chamar nossos componentes de A e B. O sistema de módulos observa que precisa de A, mas primeiro precisa de B, mas B precisa de A, mas A precisa de B, etc. Ele fica preso neste _loop_, sem saber como resolver um componente sem antes resolver o outro. Para que isso seja corrigido, precisamos dar ao sistema de módulos um ponto no qual ele possa dizer, "A precisa de B _eventualmente_, mas não há necessidade de resolver B primeiro".

No nosso caso, vamos construir esse ponto no componente `tree-folder`. Sabemos que o componente filho que cria o paradóxo é o `tree-folder-contents`, então vamos esperar até que o gatilho de ciclo de vida `beforeCreate` registre-o:

``` js
beforeCreate: function () {
  this.$options.components.TreeFolderContents = require('./tree-folder-contents.vue').default
}
```

Ou, alternativamente, poderia utilizar o `import` assíncrono do Webpack, quando registra seu componente localmente:

``` js
components: {
  TreeFolderContents: () => import('./tree-folder-contents.vue')
}
```

Problema resolvido!

## Definições de Template Alternativas

### Templates Inline

Quando o atributo especial `inline-template` estiver presente em um componente filho, o componente irá utilizar seu próprio conteúdo interno como _template_, ao invés de tratá-lo como conteúdo distribuído. Isso permite uma autoria de _template_ mais flexível.

``` html
<my-component inline-template>
  <div>
    <p>Estes são compilados como template do próprio componente.</p>
    <p>E não transclusão de conteúdo do pai.</p>
  </div>
</my-component>
```

<p class="tip">Todavia, a utilização do <code>inline-template</code> faz com que o escopo do seu _template_ seja mais difícil de entender. Como uma boa prática, prefira definir _templates_ dentro do componente usando a opção <code>template</code>, ou em um elemento <code>&lt;template&gt;</code> de um arquivo <code>.vue</code></p>

### X-Templates

Uma outra forma de definir _templates_ é dentro de elementos _script_ com tipo `text/x-template`, e então referenciando o _template_ através de um `id`. Exemplo:

``` html
<script type="text/x-template" id="hello-world-template">
  <p>Olá olá olá</p>
</script>
```

``` js
Vue.component('hello-world', {
  template: '#hello-world-template'
})
```

<p class="tip">Eles podem ser úteis para demonstrações com _templates_ maiores ou em aplicações extremamente pequenas, porém devem ser evitados em todos os outros casos, pois separam os _templates_ do resto da definição do componente.</p>

## Controlando Atualizações

Graças ao sistema de reatividade do Vue, ele sempre sabe quando atualizar a interface (se você utilizá-lo corretamente). Há casos incomuns, porém, em que você pode querer forçar uma atualização, apesar do fato de nenhum dado reativo ter mudado. Além disso, há os casos em que você pode querer previnir atualizações desnecessárias.

### Forçando uma Atualização

<p class="tip">Se você se encontrar na situação de precisar forçar uma atualização no Vue, em 99,99% dos casos, você cometeu algum erro em algum lugar.</p>

Você pode não ter levado em consideração as ressalvas na detecção de mudanças [com Arrays](list.html#list.html#Limitacoes) ou [objetos](list.html#Detectando-Mudancas-em-Objetos), ou você pode estar contando com um estado que não é monitorado pelo sistema de reatividade do Vue, por exemplo, com `data`.

Todavia, se você descartou o exposto e ainda se encontra na situação extremamente rara de ter de manualmente forçar atualização, pode fazer isso com [`$forceUpdate`](../api/#vm-forceUpdate).

### Componentes Estáticos Econômicos com `v-once`

Renderizar elementos HTML puros é bem rápido no Vue mas, algumas vezes, você pode ter um componente que possui **uma grande quantidade** de conteúdo estático. Nesses casos, você pode assegurar que ele é avaliado somente uma vez e então colocado em cache, adicionando a diretiva `v-once` ao elemento raiz, como mostrado abaixo:

``` js
Vue.component('terms-of-service', {
  template: `
    <div v-once>
      <h1>Termos de serviço</h1>
      ... uma enorme quantidade de conteúdo estático ...
    </div>
  `
})
```

<p class="tip">Como de costume, tente não abusar deste padrão. Embora conveniente nos raros casos em que você tem de renderizar muito conteúdo estático, ele é simplesmente desnecessário a não ser que você tenha de fato notado uma renderização lenta -- além disso, ele poderia levar a confusões futuras. Por exemplo, imagine um outro desenvolvedor que não é familiarizado com o <code>v-once</code> ou simplesmente não o nota no _template_. Ele pode vir a perder horas tentando entender o porquê de o _template_ não estar sendo atualizado.</p>
