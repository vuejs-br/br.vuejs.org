---
title: Guia de Estilo
type: style-guide
---

# Guia de Estilos <sup class="beta">beta</sup>

Este é o guia oficial de estilos para o código Vue. Se você usa Vue em seus projetos, é uma boa referência para evitar erros, perca de tempo, e anti-patterns. Entretanto, nós não acreditamos que qualquer guia de estilos seja o ideal para todos os times ou projetos, então pequenas alterações são encorajadas baseados em sua experiência, na tecnologia envolvida e nos valores pessoais.

Na maior parte, nós também evitamos sugestões sobre JavaScript ou HTML de um modo em geral. Não é importante para o guia se você usa ponto-e-vírgula no final da linha, ou se usa aspas simples ou duplas no seu HTML para valores de atributos.  Entretanto, algumas exceções podem existir, se encontrarmos algum detalhe particular que seja interessante ao contexto do Vue.

> **Em breve, iemos prover dicas para automação.** As vezes temos que ser apenas disciplinados, mas sempre que possível, devemos exibir a vocês como usar o ESLint e outos programas do tipo para tornar este trabalho mais fácil.

Finalmente, vamos dividir as regras de estilos em quatro categorias:

## Categorias

### Prioridade A: Essencial

Estas regras ajudam a prevenir erros, então aprenda e respeite-as a todo custo. Exceções podem existir, mas devem ser raras e somente realizadas por quer possuir um conhecimento avançado tanto em Javascript quanto Vue.

### Prioridade B: Altamente Recomendado

Estas regras são usadas para melhorar a leitura e/ou experiência do desenvolvedor nos seus projetos. Seu código será executado caso viole alguma regra, mas estas costumam ser raras e bem justificadas.

### Prioridade C: Recomendado

Onde múltiplas, igualmente boas opções coexistem,  uma escolha arbitrária pode ser feita para garantir consistência. Nestas regras, descrevemos cada opção aceitável e sugerimos uma opção padrão. Isso significa que você está livre para escolher uma opção diferente no seu código, caso tenha uma boa razão para isso. Mas tenha um bom motivo! Ao se adaptar ao padrão sugerido, você irá: 

1. Tornar o entendimento mais fácil já que o código é o mais usado pela comunidade.
2. Ser capaz de copiar e colar os exemplos mais comuns da comunidade sem a necessidade de modificação. 
3. Encontrar com mais frequência um estilo de código que já está se acostumado, pelo menos em relação ao Vue.

### Prioridade D: Use com cautela

Existem algumas recursos do Vue usados para lidar com casos isolados ou migrações mais suaves de um código legado. No entanto, quando algo está complexo demais, seu código pode ficar mais difícil de manter ou até mesmo se tornar uma fonte de erros. Esta prioridade ilustram características potencialmente arriscadas, descrevendo quando e porque elas devem ser evitadas.

## Regras da Prioridade A: Essencial (Prevenindo Erros)

### Nomes de componentes com multi-palavras <sup data-p="a">Essencial</sup>

**Nomes de componentes devem ser sempre multi-palavras, exceto o componente raiz `App`.**

Isto [previne conflitos](http://w3c.github.io/webcomponents/spec/custom/#valid-custom-element-name) com elementos HTML existentes e futuros, desque que todos os elementos HTML são formados por apenas uma única palavra.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` js
Vue.component('todo', {
  // ...
})
```

``` js
export default {
  name: 'Todo',
  // ...
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` js
Vue.component('todo-item', {
  // ...
})
```

``` js
export default {
  name: 'TodoItem',
  // ...
}
```
{% raw %}</div>{% endraw %}

### Propriedade data no componente <sup data-p="a">Essencial</sup>

**A propriedade `data` do componente deve ser uma funcao**

Quando usamos a propriedade `data` em um componente (ex: qualquer um exceto no `new Vue`), o valor desta propriedade deve ser uma função que retorna um objeto.

{% raw %}
<details>
<summary>
  <h4>Explicação detalhada</h4>
</summary>
{% endraw %}

Quando usamos o valor da propriedade `data` como um objeto, ele é compartilhado em todas as instâncias de um componente. Imagine, por exemplo, o componente `TodoList` com este data:

``` js
data: {
  listTitle: '',
  todos: []
}
```

Podemos querer reutilizar este componente, permitindo que usuários possuam múltiplas listas. (ex: para uma lista de compras, tarefas do dia a dia etc). Ainda existe outro problema. Como cada instância de um componente referencia o mesmo objeto `data`, alterar o título de uma destas listas iria também alterar o título de todas as outras listas. O mesmo aconteceria ao adicionar/editar/deletar uma tarefa.

Em vez disso, queremos que cada instância gerencie os seus próprios dados. Para que isso aconteça, cada instância deve gerar um objeto data único. No Javascript, isso pode ser feito através do retorno de um objeto em uma função:

``` js
data: function () {
  return {
    listTitle: '',
    todos: []
  }
}
```
{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` js
Vue.component('some-comp', {
  data: {
    foo: 'bar'
  }
})
```

``` js
export default {
  data: {
    foo: 'bar'
  }
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom
``` js
Vue.component('some-comp', {
  data: function () {
    return {
      foo: 'bar'
    }
  }
})
```

``` js
// No arquivo .vue
export default {
  data () {
    return {
      foo: 'bar'
    }
  }
}
```

``` js
// Tudo bem usar um objeto diretamente na raiz
// da instância Vue, já que existe uma única
// instância na app toda
new Vue({
  data: {
    foo: 'bar'
  }
})
```
{% raw %}</div>{% endraw %}


### Definido Prop <sup data-p="a">essential</sup>

**A definição de Prop deve ser a mais detalhada possível.**

No código, a definição de propriedades deve ser a mais detalhada possível, especificando pelo menos o seu tipo(s).

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Detalhando as [definições prop](https://vuejs.org/v2/guide/components.html#Prop-Validation) tem-se duas vantagens:

- Eles documentam a API do componente, então fica mais fácil ver como o componente pode ser ser usado.
- Em desenvolvimento, Vue irá notificar se o componente possui um formato inválido no props, ajudando-o a encontrar possíveis erros no seu código. 

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` js
//  Isto está OK enquanto prototipa
props: ['status']
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exempllo Bom

``` js
props: {
  status: String
}
```

``` js
// Muito melhor
props: {
  status: {
    type: String,
    required: true,
    validator: function (value) {
      return [
        'syncing',
        'synced',
        'version-conflict',
        'error'
      ].indexOf(value) !== -1
    }
  }
}
```
{% raw %}</div>{% endraw %}

### Uso do key no `v-for` <sup data-p="a">essencial</sup>

**Sempre use o atributo `key` com `v-for`.**

`key` com `v-for` deve ser sempre usado nos componentes, para manter o estado interno do componente em ordem. Mesmo para elementos, é uma boa prática manter este comportamento, tal como [a constância de um objeto](https://bost.ocks.org/mike/constancy/) em animações.


{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Vamos dizer que você tem uma lista de tarefas:

``` js
data: function () {
  return {
    todos: [
      {
        id: 1,
        text: 'Aprender a usar v-for'
      },
      {
        id: 2,
        text: 'Aprender a usar key'
      }
    ]
  }
}
```

Então você a classifica em ordem alfabética. Ao atualizar a DOM, o Vue otimizará a renderização para realizar o mínimo de mudança possível na DOM. Isso pode significar a exclusão do primeiro elemento da lista de tarefas, então adicioná-lo novamente no fim da lista. 

O problema é que existem casos em que é importante não remover elementos que permanecem na DOM. Por exemplo, talvez você use `<transition-group>` para animar o ordenação da lista, ou queira manter o foco em cada elemento renderizado como um `<input>`. Nestes casos, adicionar uma chave esclusiva para cada item (ex: `:key="todo.id"`) irá dizer ao Vue como se comportar de forma mais previsível.

Em nossa experiência, é melhor _sempre_ adicionar uma chave única, para que você e sua equipe nunca tenham que se preocupar com esses casos isolados. Então, às vezes, em cenários raros que envolvem problemas críticos de performance, você pode fazer uma exceção de forma consciente.  

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` html
<ul>
  <li v-for="todo in todos">
    {{ todo.text }}
  </li>
</ul>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` html
<ul>
  <li
    v-for="todo in todos"
    :key="todo.id"
  >
    {{ todo.text }}
  </li>
</ul>
```
{% raw %}</div>{% endraw %}

### Evite `v-if` com `v-for` <sup data-p="a">essencial</sup>

**Nunca use `v-if` no mesmo elemento `v-for`.**

Existem dois casos em comum que você possa querer fazer isso:

- Para filtar itens em uma lista (ex: `v-for="user in users" v-if="user.isActive"`). neste caso, troque `users` para uma propriedade calculada (computed) que retorne a lista filtrada (ex: `activeUsers`).

- Evite usar uma lista que deveria estar oculta (ex: `v-for="user in users" v-if="shouldShowUsers"`)

- To avoid rendering a list if it should be hidden (e.g. `v-for="user in users" v-if="shouldShowUsers"`, com `shouldShowUsers` sendo `false`). Nestes casos, mova o `v-if` para um container acima do `v-for` (ex: `ul`, `ol`)

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Quando o Vue processa ditetivas, `v-for` tem uma maior prioridade que `v-if`, então neste template:

``` html
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```

Seria traduzido para algo como:

``` js
this.users.map(function (user) {
  if (user.isActive) {
    return user.name
  }
})
```

Portanto, mesmo se renderizarmos estes elementos com uma quantidade pequena de usuarios, teremos que iterar cada item da lista toda vez que ela for renderizada, independente se o conjunto de usuários mudou ou não.

Iterado através de uma propriedade computada, teremos algo como:

``` js
computed: {
  activeUsers: function () {
    return this.users.filter(function (user) {
      return user.isActive
    })
  }
}
```

``` html
<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```

Temos agora os seguintes benefícios:

- A lista filtrada _somente_ será recalculada se houver mudanças relevantes no array `users`, tornando a filtragem muito mais eficiente.
- Usando `v-for="user in activeUsers"`, _somente_ há a iteração sobre os usuários ativos durante a renderização, o que a torna muito mais eficiente.
- A lógica agora está desacoplada da camada de apresentação, tornando a sua manutenção (alteração/extensão da lógica) muito mais fácil.

Temos benefícios semelhantes ao alterar:

``` html
<ul>
  <li
    v-for="user in users"
    v-if="shouldShowUsers"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```

para:

``` html
<ul v-if="shouldShowUsers">
  <li
    v-for="user in users"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```

Movendo o `v-if` para o elemento de container, não estamos mais verificando `shouldShowUsers` para _cada_ usuário na lista. 

Em vez disso, verificamos uma vez e nem avaliamos o `v-for` se `shouldShowUsers` for falso.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo ruim

``` html
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```

``` html
<ul>
  <li
    v-for="user in users"
    v-if="shouldShowUsers"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` html
<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```

``` html
<ul v-if="shouldShowUsers">
  <li
    v-for="user in users"
    :key="user.id"
  >
    {{ user.name }}
  <li>
</ul>
```
{% raw %}</div>{% endraw %}

### Estilos com escopo em componentes <sup data-p="a">essencial</sup>

**Para aplicações, estilos css no componente mais alto nível `App` e componentes de layout global, mas todos os outros componentes devem ser sempre com a diretiva `scope`**

Isto é apenas relevante para [componentes de aquivo úncio .vue](../guide/single-file-components.html). Ele _não_ requer que o [atributo `scope`](https://vue-loader.vuejs.org/en/features/scoped-css.html) seja usado. Escopo pode ser através de [Módulos CSS](https://vue-loader.vuejs.org/en/features/css-modules.html), uma estratégia baseada em classes tais como o [BEM](http://getbem.com/), ou outra biblioeca/convenção.

**As bibliotecas de componentes, no entanto, devem usar uma estratégia baseada em classes, ao invés de usar o atributo `scoped`**

Isso torna os estilos internos mais fáceis, com nomes de classes legíveis, sem muita  especificidade, e ainda improváveis ​​que resultem em um conflito.

{% raw %}
<details>
<summary>
  <h4>Explicação detalhada</h4>
</summary>
{% endraw %}

Se você está desenvolvendo um projeto maior, trabalhando com outros desenvolvedores, ou as vezes incluindo algum elemento de terceiro (ex: do Auth0), usar o scopo irá garantir que o seu estilo se aplica somente aos componentes destinados a ele.

Além do atributo `scoped`, usando nomes de classes únicas, garanta que CSS de terceiros não irão se aplicar ao seu HTML. Por exemplo, muitos projetos usam as classes `button`, `btn` ou `icon`, então mesmo que não use uma estratégia como a BEM, adicione um prefixo específico da aplicação ou do componente (ex: `ButtonClose-icon`) para proteger o seu componente de outras alterações.


{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` html
<template>
  <button class="btn btn-close">X</button>
</template>

<style>
.btn-close {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo bom

``` html
<template>
  <button class="button button-close">X</button>
</template>

<!-- Usando o atributo `scoped` -->
<style scoped>
.button {
  border: none;
  border-radius: 2px;
}

.button-close {
  background-color: red;
}
</style>
```

``` html
<template>
  <button :class="[$style.button, $style.buttonClose]">X</button>
</template>

<!-- usando módulos css -->
<style module>
.button {
  border: none;
  border-radius: 2px;
}

.buttonClose {
  background-color: red;
}
</style>
```

``` html
<template>
  <button class="c-Button c-Button--close">X</button>
</template>

<!-- Usando a convenção BEM -->
<style>
.c-Button {
  border: none;
  border-radius: 2px;
}

.c-Button--close {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}


### Nome de propriedades privadas <sup data-p="a">essencial</sup>

**Use sempre o prefixo `$_` para propriedades privadas em um plugin, mixin, etc. Então, para evitar conflitos com o código de outros autores, também inclua um escopo (por exemplo,`$ _yourPluginName_`).**

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

O Vue usa o prefixo `_` para definir suas próprias propriedades privadas, então, usando o mesmo prefixo (por exemplo,`_update`), uma propriedade de instância pode ser sobrescrita. Mesmo se você verificar e o Vue não estiver usando um nome de propriedade específico, não há garantia de que um conflito não surgirá em uma versão posterior.

Quanto ao prefixo `$`, o propósito dentro do ecosistema Vue são as propriedades especiais da instância que estão expostas ao usuário, portanto, usá-lo como propriedades _private_ não seria apropriado.

Em vez disso, recomendamos combinar os dois prefixos em `$_`, como uma convenção para propriedades privadas, definidas pelo usuário que para não haver conflitos com o Vue.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` js
var myGreatMixin = {
  // ...
  methods: {
    update: function () {
      // ...
    }
  }
}
```

``` js
var myGreatMixin = {
  // ...
  methods: {
    _update: function () {
      // ...
    }
  }
}
```

``` js
var myGreatMixin = {
  // ...
  methods: {
    $update: function () {
      // ...
    }
  }
}
```

``` js
var myGreatMixin = {
  // ...
  methods: {
    $_update: function () {
      // ...
    }
  }
}
```

{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` js
var myGreatMixin = {
  // ...
  methods: {
    $_myGreatMixin_update: function () {
      // ...
    }
  }
}
```
{% raw %}</div>{% endraw %}



## Prioridade B: Altamente recomendadas (Melhora a legibilidade)



### Cada componente é um arquivo <sup data-p="b">Altamente recomendado</sup>

**Sempre que houver um sistema de compilação para concatenar arquivos, cada componente deve estar em seu próprio arquivo.**

Isso ajuda você a encontrar mais rapidamente um componente quando precisar editá-lo ou verificar como usá-lo.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` js
Vue.component('TodoList', {
  // ...
})

Vue.component('TodoItem', {
  // ...
})
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

```
components/
|- TodoList.js
|- TodoItem.js
```

```
components/
|- TodoList.vue
|- TodoItem.vue
```
{% raw %}</div>{% endraw %}

### Casing dos Componentes de arquivo único (Single-File Components) <sup data-p="b">Fortemente recomendado</sup>

**Nomes de arquivos [componentes de arquivo único](../guide/single-file-components.html) devem ser sempres *PascalCase* ou *kebab-case*.

PascalCase funciona melhor com autocomplete nos editores, pois é consistente com a forma de como os componentes JavaScript são referenciados, sempre que possível. No entanto, nomes de arquivos mistos podem às vezes criar problemas em sistemas *case-insensitive*, no qual o *kebal-case* é melhor aceitável.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

```
components/
|- mycomponent.vue
```

```
components/
|- myComponent.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

```
components/
|- MyComponent.vue
```

```
components/
|- my-component.vue
```
{% raw %}</div>{% endraw %}

### Nome de componentes base <sup data-p="b">Fortemente recomendado</sup>

**Componentes base (ex: de apresentação, componentes puros ) que se aplicam a estilos e convenções específicos da aplicação, tais como `Base`, `App`, ou `V`.** 

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Estes componentes são a base para a definição de um estilo e comportamente consistente na aplicação. Eles **somente** devem conter:

- Elementos HTML,
- Outros componntes prefixados com `base` , e
- Elementos de UI de terceiros.

Mas todos eles **nunca** devem conter um um estado global (um Vuex store por exemplo)

Os nomes deles geralmente incluem o nome de um elemento que eles envolvem (por exemplo, "BaseButton", "BaseTable"), a menos que nenhum elemento exista para o propósito específico (por exemplo, 'BaseIcon'). Se você construir componentes semelhantes para um contexto mais específico, eles quase sempre consomem esses componentes (por exemplo, o `BaseButton` pode ser usado em` ButtonSubmit`).

Estes nomes incluem o nome dos elementos em que se relacionam (ex `BaseButton`, `BaseTable`), a menos que nenhum elemento exista para um propósito específico (por exemplo, `BaseIcone`). Se você criar elementos similares para um contexto mais específico, eles na maioria das vezes usam estes componentes (ex `BaseButton` pode ser usado por `ButtonSubmit`).

Algumas vantagens desta convenção:


- Quando organizados em ordem alfabética nos editores, os componentes de base do seu aplicativo estão listados em conjunto, tornando-os mais fáceis de identificar.


- Como os nomes dos componentes sempre devem ter multi palavras, esta convenção evita que você deva escolher um prefixo arbitrário para wrappers de componentes simples (por exemplo, `MyButton`, `VueButton`).

- Como esses componentes são freqüentemente usados, você pode simplesmente torná-los globais em vez de importá-los em todos os lugares. Um prefixo torna isso possível com o Webpack:

  ``` js
  var requireComponent = require.context("./src", true, /^Base[A-Z]/)
  requireComponent.keys().forEach(function (fileName) {
    var baseComponentConfig = requireComponent(fileName)
    baseComponentConfig = baseComponentConfig.default || baseComponentConfig
    var baseComponentName = baseComponentConfig.name || (
      fileName
        .replace(/^.+\//, '')
        .replace(/\.\w+$/, '')
    )
    Vue.component(baseComponentName, baseComponentConfig)
  })
  ```

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

```
components/
|- MyButton.vue
|- VueTable.vue
|- Icon.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

```
components/
|- BaseButton.vue
|- BaseTable.vue
|- BaseIcon.vue
```

```
components/
|- AppButton.vue
|- AppTable.vue
|- AppIcon.vue
```

```
components/
|- VButton.vue
|- VTable.vue
|- VIcon.vue
```
{% raw %}</div>{% endraw %}

### Nomes de componentes de instância única <sup data-p="b">Fortmente recomendado</sup>

**Componentes que devem ter somente uma única instância ativa devem começar com o prefixo `The`, para determinar que eles podem ser somente um**

Isto não quer dizer que o componente é somente utilizado em uma única página, mas somente utilizado uma vez a _cada página_. Este tipo de componente não aceita qualquer propriedade, desde que seja específica da sua aplicação, não do contexto da aplicação. Se você achar que precisa adicionar propriedades (props), pode ser uma boa indicativa que este é um componente reusável que somente é usado uma vez por página, _por enquanto_.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

```
components/
|- Heading.vue
|- MySidebar.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

```
components/
|- TheHeading.vue
|- TheSidebar.vue
```
{% raw %}</div>{% endraw %}


### Nomes de componentes fortemente acoplados <sup data-p="b">Fortemente recomendado</sup>

**Componentes filho que são fortemente acoplados com o componente pai devem incluir como prefixo o nome do componente pai**

Se um componente somente faz sentido de existir no contexto de um componente pai, o relacionamento entre eles deve ser evidente através do seu nome. Desde que os editores tipicamente organizam  os arquivos alfabeticamente, isso  torna próximos um do outro. 

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Você pode tentar resolver este problema através do alinhamento dos componentes filhos em diretórios nomeados abaixo do componente pai. Por exemplo:

```
components/
|- TodoList/
   |- Item/
      |- index.vue
      |- Button.vue
   |- index.vue
```

ou:

```
components/
|- TodoList/
   |- Item/
      |- Button.vue
   |- Item.vue
|- TodoList.vue
```

Isto não é recomendado, e como resultado temos:

- Muitos arquivos com nomes parecidos, tornando a troca de arquivos no editor um pouco mais difícil.
- Muitos sub diretórios aninhados, no qual aumenta o tempo que o editor leva para exibir os arquivos em sua referente área de sidebar

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

```
components/
|- TodoList.vue
|- TodoItem.vue
|- TodoButton.vue
```

```
components/
|- SearchSidebar.vue
|- NavigationForSearchSidebar.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

```
components/
|- TodoList.vue
|- TodoListItem.vue
|- TodoListItemButton.vue
```

```
components/
|- SearchSidebar.vue
|- SearchSidebarNavigation.vue
```
{% raw %}</div>{% endraw %}


### Ordem das palavras no nome de componentes <sup data-p="b">Fortemente recomendado</sup>

**Nomes de componentes devem começar com a palavra de mais alto nível (geralmente a maioria dos casos) e terminar com palavras que descrevem modificações.** 

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Você deve estar perguntando:

> "Porque deve-se forçar o nome dos componentes para usar uma linaguagem menos natural"

Em inglês, adjetivos e outros descritores tipicamente aparecem antes de nomes, enquanto exceções requerem uma palavra para conexão. Por exemplo:

- Café _com_ leite
- Sopa _do_ dia
- Visita _para o_ museu

Você pode definitivamente incluir estes conectores no nome dos componentes se quiser, mas a sua ordem deve ser mais importante.

Também observe que **o que se deve considerar "alto nível" contextualmente na sua aplicação.** Por exemplo, imagine uma aplicação com um formulaŕio de busca. Ele pode possuir componentes como por exemplo: 

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```

Como você pode ver, é um pouco difícil ver qual componentes são específicos para o formulário de busca. Agora, vamos renomear os componentes de acordo com as regras:

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputExcludeGlob.vue
|- SearchInputQuery.vue
|- SettingsCheckboxLaunchOnStartup.vue
|- SettingsCheckboxTerms.vue
```

Já que os editores organizam alfabeticamente os arquivos, todos as componentes que de certa forma se relacionam ficam evidentes pelo seu nome. 

Você pode querer resolver este problema de forma diferente, alinhando todos os componentes de busca sob um diretório chamado "search", e um outro chamado "settings". Somente recomendamos esta abordagem em aplicações maiores (ex: mais de 100 componentes), pelas seguintes razões:

- Geralmente leva-se mais tempo para navegar através de sub diretórios, em relação a navegação através de vários componentes em um mesmo diretório.

- Conflitos em nomes (ex: múltiplos componentes `ButtonDelete.vue`) podem ser mais difíceis de navegar através do editor.

- Refatoração pode ser mais difícil, já que buscar-e-substituir nem sempre é suficiente para alterar as referências relativas aos componentes.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputQuery.vue
|- SearchInputExcludeGlob.vue
|- SettingsCheckboxTerms.vue
|- SettingsCheckboxLaunchOnStartup.vue
```
{% raw %}</div>{% endraw %}

### Componentes self-closing <sup data-p="b">Fortemente recomendado</sup>

**Componentes sem conteúdo devem possuir seu próprio fechamento em [single-file components](../guide/single-file-components.html), templates de string, e [JSX](../guide/render-function.html#JSX) - mas nunca em tenplates da DOM**

Componentes com auto fechamento dizem que eles não somente tem conteúdo, mas **significam** que eles não devem ter conteúdo para exibir. É a diferença entre uma página em branco no livro e uma única expressão dizendo "Esta página intencionamento foi deixada em branco". Seu código inclusive deve ser o mais limpo possível exitando tags desnecessárias. 

Infelizmente, HTML não permite que elementos customizados  tenham um fechamento próprio, somente [para os elementos void](https://www.w3.org/TR/html/syntax.html#void-elements). 

É por isso que esta estratégia somente é possível quando os templates Vue são compilados antes da DOM, então servem a uma específica compilação de spec do HTML. 

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` html
<!-- In single-file components, string templates, and JSX -->
<MyComponent></MyComponent>
```

``` html
<!-- In DOM templates -->
<my-component/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` html
<!-- In single-file components, string templates, and JSX -->
<MyComponent/>
```

``` html
<!-- In DOM templates -->
<my-component></my-component>
```
{% raw %}</div>{% endraw %}

### Case no nome dos componentes <sup data-p="b">Fortemente recomendada</sup>

**Na maioria dos componentes, o nome deles deve ser sempre PascalCase nos [single-file components](../guide/single-file-components.html) e nos templates de string - mas kebab-case nos templates DOM.** 

PascalCase tem poucas vantagens sobre kebab-case:

- Editores podem auto completar nomes de componentes nos templates, já que no Javascript também usa-se PascalCase.

- `<MyComponent>` é mais distinto visualmente do que um simples elemento HTML como `<my-component>`, porque há dois caracteres distintos (as duas maiúsculas), ao invés de apenas uma (hífem). 

- Se você usar qualquer elemento visual sem ser Vue nos seus templates, como por exemplo um componente web, PascalCase reforça que os seus componentes Vue são mais visíveis.

Infelizmente, devido ao HTML ser case insensitivo, os templates DOM continuam a ser kebab-case.

Além disso note que se você já investiu bem no kebab-case, mantendo a consistência com as convenções HTML e usando essa mesma abordagem para outros projetos pode ser mais importante que as vantagens listadas acima. Nestes casos, **use kebab-case sempre que for aceitável**.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` html
<!-- In single-file components and string templates -->
<mycomponent/>
```

``` html
<!-- In single-file components and string templates -->
<myComponent/>
```

``` html
<!-- In DOM templates -->
<MyComponent></MyComponent>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` html
<!-- In single-file components and string templates -->
<MyComponent/>
```

``` html
<!-- In DOM templates -->
<my-component></my-component>
```

OR

``` html
<!-- Everywhere -->
<my-component></my-component>
```
{% raw %}</div>{% endraw %}

### Case de nome de componentes do tipo JS/JSX <sup data-p="b">fortemente recomendado</sup>

**Nomes de componentes em in JS/[JSX](../guide/render-function.html#JSX) devem ser sempre PascalCase, embora eles possam ser kebab-case dentro de strings em aplicações mais simples, que somente usam o registro global de componentes através do `Vue.component`.**

{% raw %}
<details>
<summary>
  <h4>Explicação detalhada</h4>
</summary>
{% endraw %}

Em Javascript, PascarCase é usado como convenção para classes e construtores prototype - essencialmente, qualquer coisa que possa ter instâncias distintas. Os componentes Vue também possuem instâncias, então faz todo o sentido usar PascalCase. Como um benefício extra, usando PascalCase no JSX (e templates) permite a nós distinguirem mais facilmente entre componentes e os elementos HTML

No entanto, aplicações que usam **apenas** a definição global de componentes pelo `Vue.componente`, recomendamos usar kebab-case. As razões são:

- É raro que os componentes globais sejam referenciados no JavaScript,  então seguir a convenção adotada pelo JavaScript faz menos sentido.

- Estas aplicações sempre incluem muitos templates DOM, onde [kebab-case **deve** ser usado](#Component-name-casing-in-templates-strongly-recommended).

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` js
Vue.component('myComponent', {
  // ...
})
```

``` js
import myComponent from './MyComponent.vue'
```

``` js
export default {
  name: 'myComponent',
  // ...
}
```

``` js
export default {
  name: 'my-component',
  // ...
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` js
Vue.component('MyComponent', {
  // ...
})
```

``` js
Vue.component('my-component', {
  // ...
})
```

``` js
import MyComponent from './MyComponent.vue'
```

``` js
export default {
  name: 'MyComponent',
  // ...
}
```
{% raw %}</div>{% endraw %}

### Componentes com nomes completos <sup data-p="b">Fortemente recomendado</sup>

**Nome de componentes devem ter nomes completos ao invés de abreviações**

O preenchimento automático nos editores torna o custo de escrever nomes mais longos muito baixos, enquanto a clareza que eles fornecem é inestimável. As abreviaturas incomuns, em particular, devem ser sempre evitadas.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

```
components/
|- SdSettings.vue
|- UProfOpts.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

```
components/
|- StudentDashboardSettings.vue
|- UserProfileOptions.vue
```
{% raw %}</div>{% endraw %}


### Case no nome de propriedades <sup data-p="b">Fortemente recomendado</sup>

**Nome de propriedades devem sempre usar camelCase na sua declaração, mas kebab-case nos templates e [JSX](../guide/render-function.html#JSX).** 

Estamos apenas seguindo as convensẽs para cada linguagem. No Javascript, camelCase é mais natural. Com HTML, usa-se mais kebab-case. 

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` js
props: {
  'greeting-text': String
}
```

``` html
<WelcomeMessage greetingText="hi"/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` js
props: {
  greetingText: String
}
```

``` html
<WelcomeMessage greeting-text="hi"/>
```
{% raw %}</div>{% endraw %}

### Elementos com multi atributos <sup data-p="b">fortemente recomendado</sup>

**Elementos com vários atributos devem abranger várias linhas, com um atributo por linha.**

No JavaScript, dividir objetos que possui várias propriedades em várias linhas, é considerado uma boa convenção, porque é muito mais fácil de ler. Nossos modelos e [JSX](.../guia/render-function.html#JSX) possuem a mesma consideração.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo ruim

``` html
<img src="https://vuejs.org/images/logo.png" alt="Vue Logo">
```

``` html
<MyComponent foo="a" bar="b" baz="c"/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` html
<img
  src="https://vuejs.org/images/logo.png"
  alt="Vue Logo"
>
```

``` html
<MyComponent
  foo="a"
  bar="b"
  baz="c"
/>
```
{% raw %}</div>{% endraw %}

### Expressões simples em templates <sup data-p="b">fortemente recomendado</sup>

**Os modelos de componentes devem incluir apenas expressões simples, com expressões mais complexas refatoradas em propriedades ou métodos calculados.**

Expressões complexas nos templates passam a se tornar menos declarativos. Devemos nos esforçar em exibir _o que_ está acontecendo, não _como_ o valor está sendo computado. Propriedades computadas e métodos também permitem que o código seja reutilizado.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` html
{{
  fullName.split(' ').map(function (word) {
    return word[0].toUpperCase() + word.slice(1)
  }).join(' ')
}}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Boms

``` html
<!-- In a template -->
{{ normalizedFullName }}
```

``` js
// The complex expression has been moved to a computed property
computed: {
  normalizedFullName: function () {
    return this.fullName.split(' ').map(function (word) {
      return word[0].toUpperCase() + word.slice(1)
    }).join(' ')
  }
}
```
{% raw %}</div>{% endraw %}


### Propriedades computadas mais simples<sup data-p="b">fortemente recomendadas</sup>

**Propriedades computadas complexas devem ser quebradas em várias propriedades mais simples, sempre que possível**

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Boas e simples propriedades computadas são:

- __Fácil de testar__

  Quando cada propriedade calculada contém apenas uma expressão muito simples, com poucas dependências, é muito mais fácil escrever testes confirmando que ele funciona corretamente.

- __Fácil de ler__

  Simplificar as propriedades calculadas lhe obriga a determinar cada valor um nome descritivo, mesmo que não seja reutilizado. Isso facilita a leitura para outros desenvolvedores (e no futuro, você) em se concentrar no código que lhe importam e no que está acontecendo. 

- __Mais fácil para possíveis mudanças__

  Qualquer valor que possui um nome pode ser útil para a view. Por exemplo, podemos decidir exibir uma mensagem informando o usuário quanto dinheiro economizaram. Podemos também decidir calcular o imposto sobre as vendas, mas talvez exibi-lo separadamente, e não como parte do preço final.

  Quando as propriedades calculadas são pequenas e focadas, se faz menos suposições sobre como as informações serão usadas, portanto, exigem menos refatoração à medida que os requisitos mudam.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` js
computed: {
  price: function () {
    var basePrice = this.manufactureCost / (1 - this.profitMargin)
    return (
      basePrice -
      basePrice * (this.discountPercent || 0)
    )
  }
}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` js
computed: {
  basePrice: function () {
    return this.manufactureCost / (1 - this.profitMargin)
  },
  discount: function () {
    return this.basePrice * (this.discountPercent || 0)
  },
  finalPrice: function () {
    return this.basePrice - this.discount
  }
}
```
{% raw %}</div>{% endraw %}



### Aspas em valores de atributos <sup data-p="b">fortemente recomendado</sup>

**Os valores de atributos HTML não vazios devem sempre estar dentro de aspas (simples ou duplas, o que não seja usado em JS).**

Enquanto os valores de atributos sem espaços não são necessários para ter aspas no HTML, esta prática geralmente leva a 'evitar' espaços, tornando os valores de atributos menos legíveis.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` html
<input type=text>
```

``` html
<AppSidebar :style={width:sidebarWidth+'px'}>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo bom

``` html
<input type="text">
```

``` html
<AppSidebar :style="{ width: sidebarWidth + 'px' }">
```
{% raw %}</div>{% endraw %}

### Abreviação de diretivas <sup data-p="b">fortemente recomendado</sup>

**Abreviações de diretiva (`:` for `v-bind:` and `@` for `v-on:`) devem ser sempre usadas, ou nunca usadas.**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` html
<input
  v-bind:value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

``` html
<input
  v-on:input="onInput"
  @focus="onFocus"
>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo bom

``` html
<input
  :value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

``` html
<input
  v-bind:value="newTodoText"
  v-bind:placeholder="newTodoInstructions"
>
```

``` html
<input
  @input="onInput"
  @focus="onFocus"
>
```

``` html
<input
  v-on:input="onInput"
  v-on:focus="onFocus"
>
```
{% raw %}</div>{% endraw %}




## Prioridade C : Recomendadas (Minimizando opções arbitrárias)

### Ordem das propriedades de um Componente/Instância <sup data-p="c">recomendado</sup>

**As propriedades de componente/instância devem ser ordenadas consistentemente.**

Esta é a ordem padrão que recomendamos para as propriedades dos componentes. Eles são divididos em categorias, então você saberá onde adicionar novas propriedades a partir de plugins.

1. **Efeitos externos** (Gatilho para efeitos fora do componente)
  - `el`

2. **Nível Global** (Requer conhecimento além do componente)
  - `name`
  - `parent`

3. **Tipo de componente** (altera o tipo de componente)
  - `functional`

4. **Modificadores de template** (muda a forma como o template é compilado)
  - `delimiters`
  - `comments`

5. **Dependência de template** (assets usados no templae)
  - `components`
  - `directives`
  - `filters`

6. **Composição** (faz o merge das propridades nas opções)
  - `extends`
  - `mixins`

7. **Interface** (A interface do componente)
  - `inheritAttrs`
  - `model`
  - `props`/`propsData`

8. **Estado Local** (propriedades reativas locais)
  - `data`
  - `computed`

9. **Eventos** (Callbacks disparados através de eventos reativos)
  - `watch`
  - Eventos do ciclo de vida (Na ordem que foram chamados)

10. **Propriedades não reativas** (Propriedades independentes instanciadas do sistema reativo)
  - `methods`

11. **Renderização** (A descrição declarativa da saída do componente)
  - `template`/`render`
  - `renderError`



### Ordem dos atributos dos elementos <sup data-p="c">recomendado</sup>

**Os atributos dos elementos (incluindo componentes) devem ser ordenados de forma consistente.**

This is the default order we recommend for component options. They're split into categories, so you'll know where to add custom attributes and directives.

Esta é a ordem padrão que recomendamos para os atributos de componentes. Eles são divididos em categorias, para que você saiba onde adicionar atributos e diretivas personalizadas.


1. **Definição** (provê opções ao componente)
  - `is`

2. **Listas** (Cria múltiplas variações do componente)
  - `v-for`

3. **Condicionais** (Se o elemento deve ser mostrado ou não)
  - `v-if`
  - `v-else-if`
  - `v-else`
  - `v-show`
  - `v-cloak`

4. **Modificadores de renderização** (muda a forma como o elemento renderiza)
  - `v-pre`
  - `v-once`

5. **Identificação global** (A forma como o componente é reconhecido globalmente)
  - `id`

6. **Atributos únicos** (atributos que requerem valores únicos)
  - `ref`
  - `key`
  - `slot`

7. **Two-Way Binding** (Combinação entre binding e eventos)
  - `v-model`

8. **Outros atributos** (todos os atributos vinculados ou não vinculados)

9. **Eventos** (event listeners dos componentes)
  - `v-on`

10. **Conteúdo** (Sobrescreve o conteúdo do componente)
  - `v-html`
  - `v-text`



### Linhas vazias em componentes/instâncias <sup data-p="c">recomendado</sup>

**Você pode adicionar uma linha vazia entre as propriedades de várias linhas, principalmente se as opções não puderem mais caber na tela sem rolagem.**

Quando os componentes começam a parecer apertados ou difíceis de ler, a adição de espaços entre as propriedades de várias linhas pode facilitar o deslizamento na tela. Em alguns editores, como o Vim, opções de formatação como essa também podem facilitar a navegação com o teclado.

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` js
props: {
  value: {
    type: String,
    required: true
  },

  focused: {
    type: Boolean,
    default: false
  },

  label: String,
  icon: String
},

computed: {
  formattedValue: function () {
    // ...
  },

  inputClasses: function () {
    // ...
  }
}
```

``` js
// Sem espaços parece bom, ao longo do componente
// continua fácil de ler e navegar
props: {
  value: {
    type: String,
    required: true
  },
  focused: {
    type: Boolean,
    default: false
  },
  label: String,
  icon: String
},
computed: {
  formattedValue: function () {
    // ...
  },
  inputClasses: function () {
    // ...
  }
}
```
{% raw %}</div>{% endraw %}

### Ordem os elementos de um componente de arquivo único ".vue" <sup data-p="c">recomendado</sup>

**[Componentes de arquivo único](../guide/single-file-components.html) devem sempre ter a ordem `<script>`, `<template>`, e `<style>`, com `<style>` por último, já que um dos dois primeiros sempre é necessário**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` html
<style>/* ... */</style>
<script>/* ... */</script>
<template>...</template>
```

``` html
<!-- ComponentA.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` html
<!-- ComponentA.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>
```

``` html
<!-- ComponentA.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```
{% raw %}</div>{% endraw %}

## Regras com prioridade D: use com cautela (Padrões potencialmente perigosos

### `v-if`/`v-if-else`/`v-else` sem `key` <sup data-p="d">use com cuidado</sup>

**É usualmente melhor usar `key` com `v-if` + `v-else`, se eles são do mesmo tipo de elemento (ex. dois elementos `<div>`).**

Por padrão, o Vue atualiza o DOM da forma mais eficiente possível. Isso significa que, ao alternar entre elementos do mesmo tipo, ele simplesmente corrige o elemento existente, em vez de removê-lo e adicionar um novo em seu lugar. Isso pode ter [efeitos colaterais não intencionais](https://jsfiddle.net/chrisvfritz/bh8fLeds/) se esses elementos não forem realmente considerados os mesmos.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` html
<div v-if="error">
  Error: {{ error }}
</div>
<div v-else>
  {{ results }}
</div>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` html
<div
  v-if="error"
  key="search-status"
>
  Error: {{ error }}
</div>
<div
  v-else
  key="search-results"
>
  {{ results }}
</div>
```

``` html
<p v-if="error">
  Error: {{ error }}
</p>
<div v-else>
  {{ results }}
</div>
```
{% raw %}</div>{% endraw %}

### Seletores de elemento com `scoped` <sup data-p="d">use com cuidado</sup>

**Os seletores de elemento devem ser evitados quando usados com `scoped`.**

Prefira seletores de classe ao invés de seletores de elemento, em estilos com `scoped`, porque grandes números de seletores de elemento são lentos.

{% raw %}
<details>
<summary>
  <h4>Explicação detalhada</h4>
</summary>
{% endraw %}

To scope styles, Vue adds a unique attribute to component elements, such as `data-v-f3f3eg9`. Then selectors are modified so that only matching elements with this attribute are selected (e.g. `button[data-v-f3f3eg9]`).


Para estilos com `scope`, o Vue adiciona um atributo exclusivo aos elementos do componente, como `data-v-f3f3eg9`. Em seguida, os seletores são modificados para que apenas os elementos correspondentes com esse atributo sejam selecionados (por exemplo, `button [data-v-f3f3eg9]`).

O problema é que uma grande quantidade de [seletores de elemento](http://stevesouders.com/efws/css-selectors/csscreate.php?n=1000&sel=a%5Bhref%5D&body=background%3A+%23CFD&ne=1000) (e.g. `button[data-v-f3f3eg9]`) irá consideravelmente deixá-lo mais lento que  [seletores de classe](http://stevesouders.com/efws/css-selectors/csscreate.php?n=1000&sel=.class%5Bhref%5D&body=background%3A+%23CFD&ne=1000) (e.g. `.btn-close[data-v-f3f3eg9]`), então os seletores de classe devem ser usados sempre que possível.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` html
<template>
  <button>X</button>
</template>

<style scoped>
button {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` html
<template>
  <button class="btn btn-close">X</button>
</template>

<style scoped>
.btn-close {
  background-color: red;
}
</style>
```
{% raw %}</div>{% endraw %}

### Comunicação implícita entre componentes pai-filho <sup data-p="d">use com cuidado</sup>

**Props and events should be preferred for parent-child component communication, instead of `this.$parent` or mutating props.**


**Props e eventos devem ser usados preferencialmente para a comunicação do componente pai-filho, em vez de `this.$Parent`.**

Uma aplicação ideal do Vue é usada com props para baixo e eventos para acima. Aderindo a esta convenção torna seus componentes muito mais fáceis de entender. No entanto, há casos extremos em que a pops *mutation* ou `this.$Parent` pode simplificar dois componentes que já estão profundamente acoplados.

The problem is, there are also many _simple_ cases where these patterns may offer convenience. Beware: do not be seduced into trading simplicity (being able to understand the flow of your state) for short-term convenience (writing less code).

O problema é também existem muitos casos simples em que esses padrões podem oferecer conveniência. Cuidado: não se deixe seduzir pela simplicidade de negociação (poder entender o fluxo do seu estado) para conveniência de curto prazo (escrever menos código).

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  template: '<input v-model="todo.text">'
})
```

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  methods: {
    removeTodo () {
      var vm = this
      vm.$parent.todos = vm.$parent.todos.filter(function (todo) {
        return todo.id !== vm.todo.id
      })
    }
  },
  template: `
    <span>
      {{ todo.text }}
      <button @click="removeTodo">
        X
      </button>
    </span>
  `
})
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  template: `
    <input
      :value="todo.text"
      @input="$emit('input', $event.target.value)"
    >
  `
})
```

``` js
Vue.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  template: `
    <span>
      {{ todo.text }}
      <button @click="$emit('delete')">
        X
      </button>
    </span>
  `
})
```
{% raw %}</div>{% endraw %}


### Gerenciamento de estado sem fluxo <sup data-p="d">use com cuidado</sup>

**[Vuex](https://github.com/vuejs/vuex) should be preferred for global state management, instead of `this.$root` or a global event bus.**

** [Vuex](https://github.com/vuejs/vuex) deve ser preferido para o gerenciamento de estado global, em vez de `this.$root` ou um o uso de event bus.**

Gerenciar o estado com `this.$root` e/ou usando um [barramento de eventos global](https://vuejs.org/v2/guide/migration.html#dispatch-and-broadcast-replaced) pode ser conveniente para muito casos simples, mas não são apropriados para a maioria dos aplicativos. O Vuex oferece não apenas um local central para gerenciar o estado, mas também ferramentas para organizar, rastrear e depurar alterações de estado.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Exemplo Ruim

``` js
// main.js
new Vue({
  data: {
    todos: []
  },
  created: function () {
    this.$on('remove-todo', this.removeTodo)
  },
  methods: {
    removeTodo: function (todo) {
      var todoIdToRemove = todo.id
      this.todos = this.todos.filter(function (todo) {
        return todo.id !== todoIdToRemove
      })
    }
  }
})
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Exemplo Bom

``` js
// store/modules/todos.js
export default {
  state: {
    list: []
  },
  mutations: {
    REMOVE_TODO (state, todoId) {
      state.list = state.list.filter(todo => todo.id !== todoId)
    }
  },
  actions: {
    removeTodo ({ commit, state }, todo) {
      commit('REMOVE_TODO', todo.id)
    }
  }
}
```

``` html
<!-- TodoItem.vue -->
<template>
  <span>
    {{ todo.text }}
    <button @click="removeTodo(todo)">
      X
    </button>
  </span>
</template>

<script>
import { mapActions } from 'vuex'

export default {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  methods: mapActions(['removeTodo'])
}
</script>
```
{% raw %}</div>{% endraw %}



{% raw %}
<script>
(function () {
  var enforcementTypes = {
    none: '<span title="There is unfortunately no way to automatically enforce this rule.">self-discipline</span>',
    runtime: 'runtime error',
    linter: '<a href="https://github.com/vuejs/eslint-plugin-vue#eslint-plugin-vue" target="_blank">plugin:vue/recommended</a>'
  }
  Vue.component('sg-enforcement', {
    template: '\
      <span>\
        <strong>Enforcement</strong>:\
        <span class="style-rule-tag" v-html="humanType"/>\
      </span>\
    ',
    props: {
      type: {
        type: String,
        required: true,
        validate: function (value) {
          Object.keys(enforcementTypes).indexOf(value) !== -1
        }
      }
    },
    computed: {
      humanType: function () {
        return enforcementTypes[this.type]
      }
    }
  })

  // new Vue({
  //  el: '#main'
  // })
})()
</script>
{% endraw %}
