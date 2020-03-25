---
title: Renderização de Listas
type: guide
order: 8
---

<div class="vueschool"><a href="https://vueschool.io/lessons/vuejs-loops?friend=vuejs" target="_blank" rel="sponsored noopener" title="Aprenda como renderizar listas na Vue School">Aprenda como renderizar listas com uma aula gratuita na Vue School</a></div>


## Array em Elementos com `v-for`

Podemos utilizar a diretiva `v-for` para renderizar uma lista de elementos com base nos dados de um Array. A diretiva requer uma sintaxe especial na forma de `item in items`, onde `items` é a fonte de dados e `item` é um **apelido** para o elemento que estiver sendo iterado:

``` html
<ul id="example-1">
  <li v-for="item in items" :key="item.message">
    {{ item.message }}
  </li>
</ul>
```

``` js
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Algo' },
      { message: 'Outro' }
    ]
  }
})
```

Resultado:

{% raw %}
<ul id="example-1" class="demo">
  <li v-for="item in items" :key="item.message">
    {{item.message}}
  </li>
</ul>
<script>
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Algo' },
      { message: 'Outro' }
    ]
  }
})
</script>
{% endraw %}

Dentro de blocos `v-for` temos acesso completo às propriedades do escopo pai. Também há suporte a um segundo argumento opcional para o índice do item atual.

``` html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

``` js
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Pai',
    items: [
      { message: 'Algo' },
      { message: 'Outro' }
    ]
  }
})
```

Resultado:

{% raw%}
<ul id="example-2" class="demo">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
<script>
var example2 = new Vue({
  el: '#example-2',
  data: {
    parentMessage: 'Pai',
    items: [
      { message: 'Algo' },
      { message: 'Outro' }
    ]
  }
})
</script>
{% endraw %}

Também é possível utilizar `of` como delimitador, em vez de `in`, de forma que fique mais próximo da sintaxe de iteradores do JavaScript:

``` html
<div v-for="item of items"></div>
```

## Objetos em `v-for`

Você pode utilizar `v-for` para iterar através das propriedades de um objeto.

``` html
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
```

``` js
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'Como fazer listas no Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
```

Resultado:

{% raw %}
<ul id="v-for-object" class="demo">
  <li v-for="value in object">
    {{ value }}
  </li>
</ul>
<script>
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'Como fazer listas no Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
</script>
{% endraw %}

Você também pode oferecer um segundo argumento para o nome da propriedade (também conhecido como chave):

``` html
<div v-for="(value, name) in object">
  {{ name }}: {{ value }}
</div>
```

{% raw %}
<div id="v-for-object-value-name" class="demo">
  <div v-for="(value, name) in object">
    {{ name }}: {{ value }}
  </div>
</div>
<script>
new Vue({
  el: '#v-for-object-value-name',
  data: {
    object: {
      title: 'Como fazer listas no Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
</script>
{% endraw %}

E ainda um terceiro para o índice:

``` html
<div v-for="(value, name, index) in object">
  {{ index }}. {{ name }}: {{ value }}
</div>
```

{% raw %}
<div id="v-for-object-value-name-index" class="demo">
  <div v-for="(value, name, index) in object">
    {{ index }}. {{ name }}: {{ value }}
  </div>
</div>
<script>
new Vue({
  el: '#v-for-object-value-name-index',
  data: {
    object: {
      title: 'Como fazer listas no Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
</script>
{% endraw %}

<p class="tip">Quando estiver iterando sobre um objeto, a ordem é baseada na enumeração do `Object.keys()`, a qual **não** é garantidamente consistente entre implementações distintas de motores JavaScript.</p>

## Manutenção de Estado

Quando Vue está atualizando uma lista de elementos renderizados com `v-for`, por padrão se utiliza de uma estratégia de "remendo local". Se a ordem dos itens de dados tiver mudado, em vez de mover os elementos DOM para combinar com a nova ordem, Vue remendará o conteúdo de cada elemento em seu local atual, garantindo que o resultado reflita o que precisa ser renderizado em cada índice em particular. Isto é similar ao comportamento oferecido por `track-by="$index"` no Vue 1.x.

Este modo padrão é eficiente, mas **adequado apenas quando seu resultado de renderização não se apoiar em estado de componentes filhos ou estado de DOM temporário (como valores de campos de formulário)**.

Para dar uma dica ao Vue e ele poder rastrear a identidade de cada nó e assim reutilizar e reordenar elementos existentes, defina um atributo `key` único para cada item.

``` html
<div v-for="item in items" v-bind:key="item.id">
  <!-- conteúdo -->
</div>
```

De fato, é recomendado oferecer um atributo `key` para `v-for` sempre que possível, a menos que esteja iterando conteúdo DOM simples, ou esteja intencionalmente se apoiando no comportamento padrão para ganho de desempenho.

Por ser um mecanismo genérico do Vue para identificar nós, `key` também tem outras utilidades não especificamente associadas ao `v-for`, como veremos futuramente neste guia.

<p class="tip">Não use valores não primitivos como objetos e arrays como chaves para `v-for`. Use string ou valores numéricos.</p>

Para uso detalhado do atributo `key`, por favor veja a [documentação da API `key`](https://vuejs.org/v2/api/#key).

## Detectando Mudanças em Arrays

### Métodos de Mutação

Vue envolve automaticamente os métodos de mutação de um Array observado, de forma que dispare alterações na interface. Os métodos englobados são:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

Você pode abrir o _console_ e brincar com os `items` dos exemplos anteriores, chamando seus métodos de mutação. Por exemplo: `example1.items.push({ message: 'Novo' })`.

### Substituindo um Array

Métodos de mutação, como o nome sugere, mudam o Array original no qual são chamados. Em comparação, também há métodos sem mutação, como `filter()`, `concat()` e `slice()`, que não modificam o Array original, mas sim **retornam um novo Array**. Ao trabalhar com métodos sem mutação, você pode substituir o Array antigo pelo novo:

``` js
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Algo/)
})
```

Você pode pensar que isto fará o Vue jogar fora todo o DOM existente e "re-renderizar" a lista toda - por sorte, não é o caso. Vue implementa algumas heurísticas inteligentes para maximizar a reutilização dos elementos DOM, assim sobrescrever um Array com outro, contendo elementos subjacentes, é uma operação muito eficiente.

### Limitações

Por limitações no JavaScript, existem tipos de mudanças que o Vue **não pode** detectar em arrays e objetos. Esses são discutidos na seção [reatividade](reactivity.html#Limitacoes-na-Deteccao-de-Alteracoes).

## Mostrando Resultados Filtrados/Ordenados

Às vezes, queremos exibir uma versão filtrada ou ordenada de um Array sem efetivamente mudar ou reiniciar seus dados originais. Neste caso, você pode criar um [dado computado](computed.html#Dados-Computados) que retorna um Array filtrado ou ordenado.

Por exemplo:

``` html
<li v-for="n in evenNumbers">{{ n }}</li>
```

``` js
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function () {
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

Em situações onde dados computados não são factíveis (por exemplo, em repetições `v-for` aninhadas), você pode usar um método:

```html
<ul v-for="set in sets">
  <li v-for="n in even(set)">{{ n }}</li>
</ul>
```

```js
data: {
  sets: [[ 1, 2, 3, 4, 5 ], [6, 7, 8, 9, 10]]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

## Intervalo Numérico no `v-for`

O `v-for` pode aceitar um número inteiro. O _template_ se repetirá este número de vezes.

``` html
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

Resultado:

{% raw %}
<div id="range" class="demo">
  <span v-for="n in 10">{{ n }} </span>
</div>
<script>
  new Vue({ el: '#range' })
</script>
{% endraw %}

## Utilizando `<template>` com `v-for`

Similar ao uso de _template_ com `v-if`, você pode usar `<template>` com `v-for` para renderizar blocos de múltiplos elementos sem um elemento pai repetitivo. Exemplo:

``` html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## Utilizando `v-if` com `v-for`

<p class="tip">Note que **não** é recomendado usar `v-if` e `v-for` juntos. Consulte o [guia de estilos](/v2/style-guide/#Evite-v-if-com-v-for-Essencial) para detalhes.</p>

Quando existentes em um mesmo nó, `v-for` tem maior prioridade que `v-if`. Isto significa que `v-if` será executado separadamente a cada iteração da repetição. Isto pode ser útil se quiser renderizar nós condicionalmente para apenas _alguns_ itens, como abaixo:

``` html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

Este exemplo renderizaria apenas as tarefas não marcadas como completas.

Se, em vez disso, sua intenção for condicionalmente pular toda a execução da repetição, você pode colocar o `v-if` em um elemento envolvendo o bloco (ou [`<template>`](conditional.html#Grupos-Condicionais-com-lt-template-gt)). Por exemplo:

``` html
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>Não há tarefas a fazer!</p>
```

## Componentes com `v-for`

> Caso ainda não tenha lido sobre [Componentes](components.html), sinta-se livre para pular e voltar depois.

Você pode usar `v-for` diretamente em componentes personalizados de maneira comum:

``` html
<my-component v-for="item in items" :key="item.id"></my-component>
```

> Em 2.2.0+, ao usar `v-for` com um componente, um atributo [`key`](list.html#key) é obrigatório.

Observe, entretanto, que isto não passa automaticamente qualquer dado ao componente, pois componentes possuem seus próprios escopos isolados. Para passar dados iterados para dentro do componente, devemos utilizar [Propriedades](components.html#Propriedades):

``` html
<my-component
  v-for="(item, index) in items"
  v-bind:item="item"
  v-bind:index="index"
  v-bind:key="item.id"
></my-component>
```

A razão para não injetarmos automaticamente `item` dentro do componente é evitar que ele fique fortemente acoplado ao funcionamento do `v-for`. Sendo explícito sobre quais dados especificamente são introduzidos, tornamos o componente reutilizável em outras situações.

Aqui temos um exemplo de uma lista de tarefas simples usando componentes:

``` html
<div id="todo-list-example">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Tarefa:</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="Ex.: Alimentar o gato"
    >
    <button>Adicionar</button>
  </form>
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
```

<p class="tip">Observe o atributo `is="todo-item"`. Ele é necessario em templates DOM, porque somente o elemento `<li>` é valido dentro de um `<ul>`. Ele faz o mesmo que `<todo-item>`, mas funciona em torno de um potencial erro do navegador. Veja [DOM Template Parsing Caveats](components.html#DOM-Template-Parsing-Caveats) para saber mais.</p>

``` js
Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">Remove</button>\
    </li>\
  ',
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'Lavar os pratos',
      },
      {
        id: 2,
        title: 'Tirar o lixo',
      },
      {
        id: 3,
        title: 'Cortar a grama'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
```

{% raw %}
<div id="todo-list-example" class="demo">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Tarefa:</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="Ex.: Alimentar o gato"
    >
    <button>Adicionar</button>
  </form>
  <ul>
    <li
      is="todo-item"
      v-for="(todo, index) in todos"
      v-bind:key="todo.id"
      v-bind:title="todo.title"
      v-on:remove="todos.splice(index, 1)"
    ></li>
  </ul>
</div>
<script>
Vue.component('todo-item', {
  template: '\
    <li>\
      {{ title }}\
      <button v-on:click="$emit(\'remove\')">Remove</button>\
    </li>\
  ',
  props: ['title']
})

new Vue({
  el: '#todo-list-example',
  data: {
    newTodoText: '',
    todos: [
      {
        id: 1,
        title: 'Lavar os pratos',
      },
      {
        id: 2,
        title: 'Tirar o lixo',
      },
      {
        id: 3,
        title: 'Cortar a grama'
      }
    ],
    nextTodoId: 4
  },
  methods: {
    addNewTodo: function () {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})
</script>
{% endraw %}
