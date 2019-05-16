---
title: Introdução
type: style-guide
---

# Guia de Estilos <sup class="beta">beta</sup>

Este é o guia oficial de estilos para o código Vue. Se você usa Vue em seus projetos, é uma boa referência para evitar erros, perda de tempo e adoção de anti-padrões. Entretanto, nós não acreditamos que qualquer guia de estilos seja o ideal para todos os times ou projetos, então pequenas alterações são encorajadas, baseadas em sua experiência, na tecnologia envolvida e nos valores pessoais.

Na maior parte do guia, também evitamos sugestões sobre JavaScript ou HTML de um modo em geral. Não é importante para o guia se você usa ponto-e-vírgula no final da linha, ou se usa aspas simples ou duplas nos valores de atributos de seu HTML. Entretanto, algumas exceções podem existir, se encontrarmos algum detalhe particular que seja interessante no contexto do Vue.

> **Em breve, proveremos dicas para automação.** As vezes temos que ser apenas disciplinados, mas sempre que possível, devemos exibir a vocês como usar o ESLint e outros programas do tipo para tornar este trabalho mais fácil.

Finalmente, vamos dividir as regras de estilos em quatro categorias:

## Categorias

### Prioridade A: Essencial

Estas regras ajudam a prevenir erros, então aprenda e respeite-as a todo custo. Exceções podem existir, mas devem ser raras e somente realizadas por quem possuir um conhecimento avançado tanto em JavaScript quanto em Vue.

### Prioridade B: Altamente Recomendado

Estas regras são usadas para melhorar a leitura e/ou experiência do desenvolvedor nos seus projetos. Seu código ainda funcionará caso viole alguma, mas as violações devem ser raras e bem justificadas.

### Prioridade C: Recomendado

Onde múltiplas, igualmente boas opções coexistem, uma escolha arbitrária pode ser feita para garantir consistência. Nestas regras, descrevemos cada opção aceitável e sugerimos uma opção padrão. Isso significa que você está livre para escolher uma opção diferente no seu código, caso tenha uma boa razão para isso. Por favor, tenha uma boa razão! Ao se adaptar ao padrão sugerido, você irá:

1. Treinar seu cérebro para analisar mais facilmente a maior parte do código da comunidade que encontrar.
2. Ser capaz de copiar e colar os exemplos mais comuns da comunidade sem a necessidade de modificação.
3. Muitas vezes encontrar novos colegas já acostumados ao seu estilo de codificação preferido, pelo menos em relação ao Vue.

### Prioridade D: Use Cautelosamente

Existem alguns recursos do Vue usados para lidar com casos isolados ou migrações mais suaves de um código legado. No entanto, quando usados ​​em excesso, eles podem tornar seu código mais difícil de manter ou até se tornar uma fonte de bugs. Estas regras ilustram os recursos potencialmente arriscados, descrevendo quando e por que eles devem ser evitados.

## Prioridade A: Essencial (Prevenindo Erros)

### Nomes de componentes com multi-palavras <sup data-p="a">Essencial</sup>

**Nomes de componentes devem ser sempre multi-palavras, exceto pelo componente `App` raiz e componentes internos fornecidos pelo Vue, como <transition> ou <component>.**

Isto [previne conflitos](http://w3c.github.io/webcomponents/spec/custom/#valid-custom-element-name) com elementos HTML existentes e futuros, visto que todos os elementos HTML são formados por apenas uma única palavra.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

### Dados em componentes <sup data-p="a">Essencial</sup>

**A opção `data` em componentes deve ser uma função.**

Quando usamos `data` em um componente (ou seja, em qualquer lugar exceto em um `new Vue`), o valor desta propriedade deve ser uma função que retorna um objeto.

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Quando usamos o valor da propriedade `data` como um objeto, ele é compartilhado em todas as instâncias de um componente. Imagine, por exemplo, o componente `TodoList` com estes dados:

``` js
data: {
  listTitle: '',
  todos: []
}
```

Podemos querer reutilizar este componente, permitindo que usuários utilizem múltiplas listas (como lista de compras, lista de tarefas do dia-a-dia, dentre outras). Ainda existe outro problema: como cada instância de um componente referencia o mesmo objeto `data`, alterar o título em uma destas listas alteraria também o título de todas as outras listas. O mesmo aconteceria ao adicionar/editar/excluir uma tarefa do _todo_.

Em vez disso, queremos que cada instância gerencie os seus próprios dados. Para que isso aconteça, cada instância deve gerar um objeto de dados único. No JavaScript, isso pode ser feito através do retorno de um objeto em uma função:

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
#### Mal Exemplo

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
#### Bom Exemplo

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
// instância para toda a aplicação
new Vue({
  data: {
    foo: 'bar'
  }
})
```
{% raw %}</div>{% endraw %}

### Definição de propriedades <sup data-p="a">essential</sup>

**A definição de propriedades deve ser a mais detalhada possível.**

No código, a definição de propriedades deve ser a mais detalhada possível, especificando pelo menos os tipos de dados.

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

[Definições de propriedades](https://vuejs.org/v2/guide/components.html#Prop-Validation) detalhadas nos dá duas vantagens:

- Elas documentam a API do componente, então fica mais fácil ver como o componente deve ser usado.
- Em desenvolvimento, Vue irá avisá-lo se o componente tiver recebido props formatados incorretamente, ajudando-o a encontrar possíveis erros no código.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

``` js
//  Isto está bom apenas enquanto prototipa
props: ['status']
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bom Exemplo

``` js
props: {
  status: String
}
```

``` js
// Ainda melhor!
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

### Chave de identificação no `v-for` <sup data-p="a">Essencial</sup>

**Sempre use o atributo `key` quando usar `v-for`.**

O atributo `key` em `v-for` sempre deve ser usado nos componentes, para manter o estado interno do componente em ordem. Mesmo para elementos, é uma boa prática manter comportamento previsível, tal como a [constância de objetos](https://bost.ocks.org/mike/constancy/) em animações.

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

Então você a classifica em ordem alfabética. Ao atualizar a árvore DOM, o Vue otimizará a renderização para realizar o mínimo de mudança possível no documento. Isso pode significar a exclusão do primeiro elemento da lista de tarefas, para então adicioná-lo novamente no fim da lista.

O problema é que existem casos em que é importante não remover elementos que depois serão reinseridos no DOM. Por exemplo, talvez você use `<transition-group>` para animar a ordenação da lista, ou queira manter o foco em cada elemento renderizado como um `<input>`. Nestes casos, adicionar uma chave exclusiva para cada item (por exemplo, `:key="todo.id"`) dirá ao Vue como se comportar de forma mais previsível.

Em nossa experiência, o melhor é _sempre_ adicionar uma chave única, para que você e sua equipe nunca tenham que se preocupar com esses casos isolados. Então, em raros cenários que envolvem problemas críticos de performance, você pode fazer uma exceção de forma muito consciente.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

``` html
<ul>
  <li v-for="todo in todos">
    {{ todo.text }}
  </li>
</ul>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bom Exemplo

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

### Evite `v-if` com `v-for` <sup data-p="a">Essencial</sup>

**Nunca use `v-if` no mesmo elemento que possui `v-for`.**

Existem dois casos comuns onde isso pode ser tentador:

- Para filtrar itens em uma lista (ex.: `v-for="user in users" v-if="user.isActive"`). Neste caso, troque `users` por um dado computado que retorne a lista já filtrada (ex.: `activeUsers`).

- Para esconder uma lista condicionalmente (ex.: `v-for="user in users" v-if="shouldShowUsers"`), com `shouldShowUsers` sendo `false`. Nestes casos, mova o `v-if` para um elemento pai acima do `v-for` (como um `ul`, `ol`).

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Quando o Vue processa diretivas, `v-for` tem maior prioridade que `v-if`, então este _template_:

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

Portanto, mesmo se renderizarmos uma quantidade pequena de usuários, teremos que iterar cada item da lista toda vez que ela for renderizada, independentemente do conjunto de usuários ter mudado ou não.

Iterando através de uma propriedade computada, teremos algo como:

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

O que nos oferece os benefícios seguintes:

- A lista filtrada _somente_ será recalculada se houver mudanças relevantes no Array `users`, tornando a filtragem muito mais eficiente.
- Usando `v-for="user in activeUsers"`, _somente_ há a iteração sobre os usuários ativos durante a renderização, o que a torna muito mais eficiente.
- A lógica agora está desacoplada da camada de apresentação, tornando a sua manutenção (alteração/extensão da lógica) muito mais fácil.

Temos benefícios semelhantes ao trocar:

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

Por:

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

Movendo o `v-if` para o elemento pai, não estamos mais verificando `shouldShowUsers` para _cada_ usuário na lista.

Em vez disso, verificamos apenas uma vez e nem avaliamos o `v-for` se `shouldShowUsers` for falso.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

### Estilos em componentes com escopo <sup data-p="a">Essencial</sup>

**Para aplicações, estilos CSS no componente raiz `App` e componentes de _layout_ podem ser globais, mas todos os outros componentes devem utilizar estilos com escopo.**

Isto é relevante apenas para [componentes em arquivos .vue](../guide/single-file-components.html). Contudo, _não_ é requerido utilizar o [atributo `scoped`](https://vue-loader.vuejs.org/en/features/scoped-css.html). Escopo pode ser obtido através de [módulos CSS](https://vue-loader.vuejs.org/en/features/css-modules.html), estratégias baseadas em classes tais como o [BEM](http://getbem.com/), ou qualquer outra biblioteca/convenção.

**As bibliotecas de componentes, no entanto, devem usar uma estratégia baseada em classes, ao invés de usar o atributo `scoped`.**

Isso torna os estilos internos mais fáceis, com nomes de classes legíveis, sem muita especificidade, e ainda improváveis de que resultem em algum conflito.

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Se você está desenvolvendo um projeto maior, trabalhando com outros desenvolvedores, ou as vezes incluindo algum HTML/CSS de terceiros (ex.: algo do Auth0), usar escopo de forma consistente garante que seu estilo se aplica somente aos componentes destinados a ele.

Além do atributo `scoped`, usar nomes de classes únicas pode garantir que CSS de terceiros não se aplicará ao seu HTML. Por exemplo, muitos projetos usam classes como `button`, `btn` ou `icon`. Então, mesmo que não use uma estratégia como a BEM, adicionar um prefixo específico da aplicação ou do componente (ex.: `ButtonClose-icon`) pode oferecer alguma proteção.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

<!-- Usando módulos CSS -->
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

### Nomes de propriedades privadas <sup data-p="a">Essencial</sup>

**Use o escopo do módulo para manter as funções privadas inacessíveis do lado de fora. Se isso não for possível, sempre use o prefixo `$_` para propriedades privadas em um _plugin_, _mixin_, etc. Para evitar conflitos com o código de outros autores, também inclua um nome de escopo (por exemplo,`$_yourPluginName_`).**

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

O Vue usa o prefixo `_` para definir suas próprias propriedades privadas, então, usando o mesmo prefixo (por exemplo,`_update`), uma propriedade de instância pode acabar sendo sobrescrita. Mesmo se você verificar e o Vue não estiver usando um nome de propriedade específico, não há garantia de que um conflito não surgirá em uma versão posterior.

Quanto ao prefixo `$`, o propósito dentro do ecossistema Vue é identificar as propriedades especiais da instância que estão expostas ao usuário, portanto, usá-lo em propriedades _privadas_ não seria apropriado.

Em vez disso, recomendamos combinar os dois prefixos em `$_`, como uma convenção para propriedades privadas, definidas pelo usuário que garantidamente não conflitam com o Vue.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

``` js
// Ainda melhor!
var myGreatMixin = {
  // ...
  methods: {
    publicMethod() {
      // ...
      myPrivateFunction()
    }
  }
}

function myPrivateFunction() {
  // ...
}

export default myGreatMixin
```

{% raw %}</div>{% endraw %}

## Prioridade B: Altamente Recomendado (Melhoram a Legibilidade)

### Cada componente em um arquivo <sup data-p="b">Altamente Recomendado</sup>

**Sempre que houver um sistema de compilação para concatenar arquivos, cada componente deve estar em seu próprio arquivo.**

Isso ajuda você a encontrar mais rapidamente um componente quando precisar editá-lo ou verificar como usá-lo.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

### Notação de nomes de componentes single-file <sup data-p="b">Altamente Recomendado</sup>

**Nomes de arquivos de [componentes Single-File](../guide/single-file-components.html) devem ser sempre _PascalCase_ ou _kebab-case_.**

Notação com _PascalCase_ garante melhor auto-completação nos editores de código, pois é consistente com a forma como nós referenciamos os componentes no JS(X) e templates, onde for possível. No entanto, tipos de nomes de arquivos mistos podem, às vezes, criar problemas em sistemas insensíveis a maiúsculas e minúculas, quando o _kebab-case_ é mais aceitável.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

```
components/
|- MyComponent.vue
```

```
components/
|- my-component.vue
```
{% raw %}</div>{% endraw %}

### Nome de componentes base <sup data-p="b">Altamente Recomendado</sup>

**Componentes base (também conhecidos como componentes de apresentação, _dumbs_ ou puros) que aplicam estilos e convenções específicos da aplicação, devem iniciar com um prefixo específico, tal como `Base`, `App`, ou `V`.**

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Estes componentes são a base para a definição de estilo e comportamento consistente na aplicação. Eles **somente** devem conter:

- Elementos HTML,
- Outros componentes `base` , e
- Elementos UI de terceiros.

Mas eles **nunca** conterão estado global (como um _store_ Vuex).

Os nomes deles geralmente incluem o nome de um elemento que eles envolvem (por exemplo, `BaseButton`, `BaseTable`), a menos que nenhum elemento exista para o propósito específico (por exemplo, `BaseIcon`). Se você construir componentes semelhantes para um contexto mais específico, eles quase sempre consumirão tais componentes (por exemplo, o `BaseButton` pode ser consumido por `ButtonSubmit`).

Algumas vantagens desta convenção:

- Quando organizados em ordem alfabética nos editores, os componentes de base serão listados em conjunto, tornando-os mais fáceis de identificar.

- Como os nomes dos componentes sempre devem ser multi-palavras, esta convenção te previne de escolher um prefixo arbitrário para simples wrappers de componentes (por exemplo, `MyButton`, `VueButton`).

- Como esses componentes são frequentemente usados, você pode simplesmente torná-los globais em vez de importá-los em todos os lugares. Um prefixo torna isso possível com Webpack:

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
#### Mal Exemplo

```
components/
|- MyButton.vue
|- VueTable.vue
|- Icon.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bom Exemplo

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

### Nomes de componentes de instância única <sup data-p="b">Altamente Recomendado</sup>

**Componentes que devem ter somente uma única instância ativa devem começar com o prefixo `The`, para determinar que eles podem ser somente um.**

Isto não quer dizer que o componente é somente utilizado em uma única página, mas somente utilizado uma vez a _cada página_. Este tipo de componente não aceita qualquer prop, uma vez que são específicos de sua aplicação, não ao contexto deles dentro do app. Se você achar que precisa adicionar props, pode ser uma boa indicação que este é um componente reusável que somente é usado uma vez por página, _por enquanto_.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

```
components/
|- Heading.vue
|- MySidebar.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bom Exemplo

```
components/
|- TheHeading.vue
|- TheSidebar.vue
```
{% raw %}</div>{% endraw %}

### Nomes de componentes fortemente acoplados <sup data-p="b">Altamente Recomendado</sup>

**Componentes filho que são fortemente acoplados com o componente pai devem incluir, como prefixo, o nome do componente pai.**

Se um componente somente faz sentido se existir no contexto de um componente pai, o relacionamento entre eles deve ser evidente através de seu nome. Uma vez que os editores tipicamente organizam os arquivos alfabeticamente, isso os deixa visualmente próximos uns dos outros.

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Talvez você tente resolver tal problema através do alinhamento dos componentes filhos em diretórios nomeados abaixo do pai. Por exemplo:

```
components/
|- TodoList/
   |- Item/
      |- index.vue
      |- Button.vue
   |- index.vue
```

Ou:

```
components/
|- TodoList/
   |- Item/
      |- Button.vue
   |- Item.vue
|- TodoList.vue
```

Isto não é recomendado, pois como resultado temos:

- Muitos arquivos com nomes parecidos, tornando a rápida alternância entre arquivos no editor um pouco mais difícil.
- Muitos subdiretórios aninhados, que aumenta o tempo necessário para procurar componentes na barra lateral de um editor.
{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

### Ordem das palavras nos nomes de componentes <sup data-p="b">Altamente Recomendado</sup>

**Nomes de componentes devem começar com a palavra de mais alto nível (muitas vezes a mais geral) e terminar com palavras de modificações descritivas.**

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Você deve estar se perguntando:

> "Porque forçar uma linguagem menos natural ao nome dos componentes?"

No inglês natural, adjetivos e outros descritores tipicamente aparecem antes de nomes, enquanto exceções exigem uma palavra para conexão. Por exemplo:

- Café _com_ leite
- Sopa {% raw %}<em>do</em>{% endraw %} dia
- Visita _para o_ museu

Você pode definitivamente incluir estes conectores no nome dos componentes, se quiser, mas a ordem das palavras ainda é importante.

Também observe que **o que é considerado "alto nível" será contextual para seu app.** Por exemplo, imagine uma aplicação com um formulário de busca. Ele pode possuir componentes como:

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```

Como você pode ver, é um pouco difícil ver qual componentes são específicos para o formulário de busca. Agora, vamos renomear os componentes de acordo com a regra:

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputExcludeGlob.vue
|- SearchInputQuery.vue
|- SettingsCheckboxLaunchOnStartup.vue
|- SettingsCheckboxTerms.vue
```

Já que os editores organizam alfabeticamente os arquivos, todos os componentes que se relacionam ficam evidentes pelo próprio nome.

Você pode querer resolver este problema de forma diferente, aninhando todos os componentes de busca sob um diretório chamado "search", e todos os componentes de configuração em outro chamado "settings". Só recomendamos esta abordagem em aplicações maiores (por exemplo, com mais de 100 componentes), pelas seguintes razões:

- Geralmente leva-se mais tempo para navegar através de subdiretórios, do que percorrer um único diretório `components`.

- Conflitos com nomes (como múltiplos componentes `ButtonDelete.vue`) dificulta a navegação rápida para um componente específico em um editor de código.

- Refatoração pode ser mais difícil, já que buscar-e-substituir nem sempre será suficiente para alterar as referências relativas à componentes movidos.
{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

### Componentes com auto-fechamento <sup data-p="b">Altamente Recomendado</sup>

**Componentes sem conteúdo devem ser auto-fechados em [componentes Single-File](../guide/single-file-components.html), _templates_ baseados em Strings e [JSX](../guide/render-function.html#JSX) - mas nunca em _templates_ do DOM.**

Componentes auto-fechados não somente informam que não têm conteúdo, mas **garantem** que não devem ter conteúdo. É a diferença entre uma página em branco em um livro e uma dizendo "Esta página intencionalmente foi deixada em branco". No entanto, seu código também é mais limpo sem a tag de fechamento desnecessária.

Infelizmente, HTML não permite que elementos customizados tenham fechamento próprio - somente nos [elementos "void" oficiais](https://www.w3.org/TR/html/syntax.html#void-elements). É por isso que esta estratégia somente é possível quando os _templates_ Vue são compilados antes de estarem no DOM, para que possam então servir um HTML compatível com a especificação.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

``` html
<!-- Em componentes Single-File, templates String e JSX -->
<MyComponent></MyComponent>
```

``` html
<!-- Em templates DOM -->
<my-component/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bom Exemplo

``` html
<!-- Em componentes Single-File, templates String e JSX -->
<MyComponent/>
```

``` html
<!-- Em templates DOM -->
<my-component></my-component>
```
{% raw %}</div>{% endraw %}

### Notação do nome dos componentes em templates <sup data-p="b">Altamente Recomendado</sup>

**Na maioria dos projetos, nomes de componentes devem ser sempre _PascalCase_ em [componentes Single-File](../guide/single-file-components.html) e  _templates_ String - mas _kebab-case_ nos _templates_ DOM.** 

_PascalCase_ tem algumas vantagens sobre _kebab-case_:

- Editores podem auto-completar nomes de componentes nos _templates_, já que no JavaScript também se usa _PascalCase_.

- `<MyComponent>` é mais distinto visualmente do que um simples elemento HTML como `<my-component>`, porque há dois caracteres distintos (as duas maiúsculas), ao invés de apenas um (o hífem).

- Se você usar qualquer elemento não Vue em seus _templates_, como um componente web, _PascalCase_ reforça que os seus componentes Vue são mais visíveis.

Infelizmente, como o HTML é insensível a maiúsculas e minúsculas, _templates_ DOM continuam a ser _kebab-case_.

Além disso, observe que se você já investiu bastante em `kebab-case`, mantendo a consistência com as convenções HTML e usando essa mesma abordagem para todos os seus projetos, isso pode ser mais importante do que as vantagens listadas acima. Nestes casos, **use _kebab-case_ sempre que for aceitável**.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

``` html
<!-- Em componentes Single-File e templates String -->
<mycomponent/>
```

``` html
<!-- Em componentes Single-File e templates String -->
<myComponent/>
```

``` html
<!-- Em templates DOM -->
<MyComponent></MyComponent>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bom Exemplo

``` html
<!-- Em componentes Single-File e templates String -->
<MyComponent/>
```

``` html
<!-- Em templates DOM -->
<my-component></my-component>
```

Ou:

``` html
<!-- Em qualquer lugar -->
<my-component></my-component>
```
{% raw %}</div>{% endraw %}

### Notação de nome de componentes JS/JSX <sup data-p="b">Altamente Recomendado</sup>

**Nomes de componentes em JS/[JSX](../guide/render-function.html#JSX) devem ser sempre _PascalCase_, embora eles possam ser _kebab-case_ dentro de Strings em aplicações mais simples, que apenas utilizam registro global de componentes através de `Vue.component`.**

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Em JavaScript, _PascalCase_ é usado como convenção para classes e construtores - essencialmente, qualquer coisa que possa ter instâncias distintas. Os componentes Vue também possuem instâncias, então faz todo o sentido usar tal notação. Como um benefício extra, usando _PascalCase_ no JSX (e em _templates_), podemos distinguir mais facilmente os componentes dos elementos HTML.

No entanto, para aplicações que usam **apenas** a definição global de componentes pelo `Vue.component`, recomendamos usar _kebab-case_. As razões são:

- É raro que os componentes globais sejam referenciados no JavaScript, então seguir a convenção adotada pelo JavaScript faz menos sentido.

- Estas aplicações normalmente incluem muitos _templates_ DOM, cenário onde [_kebab-case_ **deve** ser usado](#Component-name-casing-in-templates-strongly-recommended).
{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

### Componentes com nomes completos <sup data-p="b">Altamente Recomendado</sup>

**Nomes de componentes devem ter palavras completas, ao invés de abreviações.**

O preenchimento automático nos editores de código torna o custo de escrever nomes mais longos muito baixo, enquanto a clareza que eles fornecem é inestimável. Abreviações incomuns, em particular, devem ser sempre evitadas.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

```
components/
|- SdSettings.vue
|- UProfOpts.vue
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bom Exemplo

```
components/
|- StudentDashboardSettings.vue
|- UserProfileOptions.vue
```
{% raw %}</div>{% endraw %}

### Notação do nome de propriedades <sup data-p="b">Altamente Recomendado</sup>

**Nome de propriedades devem sempre usar camelCase na sua declaração, mas kebab-case nos templates e [JSX](../guide/render-function.html#JSX).** 

Estamos apenas seguindo as convenções para cada linguagem. No JavaScript, camelCase é mais natural. Com HTML, usa-se mais kebab-case. 

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

``` js
props: {
  greetingText: String
}
```

``` html
<WelcomeMessage greeting-text="hi"/>
```
{% raw %}</div>{% endraw %}

### Elementos multi-atributo <sup data-p="b">Altamente Recomendado</sup>

**Elementos com vários atributos devem abranger várias linhas, com um atributo por linha.**

No JavaScript, dividir objetos que possuem várias propriedades em várias linhas, é considerado uma boa convenção, porque é muito mais fácil de ler. Nossos modelos e [JSX](.../guia/render-function.html#JSX) merecem a mesma consideração.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

``` html
<img src="https://vuejs.org/images/logo.png" alt="Vue Logo">
```

``` html
<MyComponent foo="a" bar="b" baz="c"/>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bom Exemplo

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

### Expressões simples em templates <sup data-p="b">Altamente Recomendado</sup>

**Templates de componentes devem incluir apenas expressões simples, com expressões mais complexas refatoradas em propriedades computadas ou métodos.**

Expressões complexas nos seus templates os tornam menos declarativos. Devemos nos esforçar em exibir _o que_ está acontecendo, não _como_ o valor está sendo computado. Propriedades computadas e métodos também permitem que o código seja reutilizado.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

``` html
{{
  fullName.split(' ').map(function (word) {
    return word[0].toUpperCase() + word.slice(1)
  }).join(' ')
}}
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bom Exemplos

``` html
<!-- Em um template -->
{{ normalizedFullName }}
```

``` js
// A expressão complexa foi movida para uma propriedade computada
computed: {
  normalizedFullName: function () {
    return this.fullName.split(' ').map(function (word) {
      return word[0].toUpperCase() + word.slice(1)
    }).join(' ')
  }
}
```
{% raw %}</div>{% endraw %}

### Propriedades computadas simples <sup data-p="b">Altamente Recomendado</sup>

**Propriedades computadas complexas devem ser quebradas em várias propriedades mais simples, sempre que possível**

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Propriedades computadas simples e bem nomeadas são:

- __Fácil de testar__

  Quando cada propriedade computada contém apenas uma expressão muito simples, com poucas dependências, é muito mais fácil escrever testes confirmando que ela funciona corretamente.

- __Fácil de ler__

  Simplificar as propriedades computadas lhe obriga a determinar cada valor um nome descritivo, mesmo que não seja reutilizado. Isso facilita para outros desenvolvedores (e no futuro, você) focar no código com o qual eles se importam e descobrir o que está acontecendo.

- __Mais adaptável à mudança dos requisitos__

  Qualquer valor que possa ser nomeado pode ser útil para a _view_. Por exemplo, podemos decidir exibir uma mensagem informando ao usuário quanto de dinheiro economizou. Podemos também decidir calcular o imposto sobre as vendas, mas talvez exibi-lo separadamente, e não como parte do preço final.

  Quando as propriedades calculadas são pequenas e focadas, se faz menos suposições sobre como as informações serão usadas, portanto, exigem menos refatoração à medida que os requisitos mudam.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

### Aspas em valores de atributos <sup data-p="b">Altamente Recomendado</sup>

**Os valores de atributos HTML não vazios devem sempre estar dentro de aspas (simples ou duplas, o que não for usado em JS).**

Enquanto os valores de atributos sem espaços não são necessários para ter aspas no HTML, esta prática geralmente leva a 'evitar' espaços, tornando os valores de atributos menos legíveis.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

``` html
<input type=text>
```

``` html
<AppSidebar :style={width:sidebarWidth+'px'}>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bom Exemplo

``` html
<input type="text">
```

``` html
<AppSidebar :style="{ width: sidebarWidth + 'px' }">
```
{% raw %}</div>{% endraw %}

### Abreviação de diretivas <sup data-p="b">Altamente Recomendado</sup>

**Abreviações de diretiva (`:` para `v-bind:` e `@` para `v-on:`) devem ser usadas sempre ou nunca.**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

## Prioridade C: Recomendadas (Minimizando Escolhas Arbitrárias e Sobrecarga Cognitiva)

### Ordem das opções de componente/instância <sup data-p="c">Recomendado</sup>

**Opções de componentes/instâncias devem ser ordenadas consistentemente.**

Esta é a ordem padrão que recomendamos para opções dos componentes. Eles são divididos em categorias, assim você saberá onde adicionar novas propriedades de plug-ins.

1. **Efeitos colaterais** (desencadeia efeitos fora do componente)
  - `el`

2. **Consciência Global** (requer conhecimento além do componente)
  - `name`
  - `parent`

3. **Tipo de componente** (altera o tipo de componente)
  - `functional`

4. **Modificadores de template** (muda a forma como o template é compilado)
  - `delimiters`
  - `comments`

5. **Dependências de template** (assets usados no templae)
  - `components`
  - `directives`
  - `filters`

6. **Composição** (mescla propriedades nas opções)
  - `extends`
  - `mixins`

7. **Interface** (a interface para o componente)
  - `inheritAttrs`
  - `model`
  - `props`/`propsData`

8. **Estado Local** (propriedades reativas locais)
  - `data`
  - `computed`

9. **Eventos** (_callbacks_ disparados por eventos reativos)
  - `watch`
  - Eventos do Ciclo de Vida (na ordem em que são chamados)
    - `beforeCreate`
    - `created`
    - `beforeMount`
    - `mounted`
    - `beforeUpdate`
    - `updated`
    - `activated`
    - `deactivated`
    - `beforeDestroy`
    - `destroyed`

10. **Propriedades não reativas** (propriedades de instância independentes do sistema de reatividade)
  - `methods`

11. **Renderização** (a descrição declarativa da saída do componente)
  - `template`/`render`
  - `renderError`

### Ordem dos atributos de elementos <sup data-p="c">Recomendado</sup>

**Os atributos dos elementos (incluindo componentes) devem ser ordenados de forma consistente.**

Esta é a ordem padrão que recomendamos para os atributos de componentes. Eles são divididos em categorias, para que você saiba onde adicionar atributos e diretivas personalizadas.

1. **Definição** (fornece as opções do componente)
  - `is`

2. **Renderização de Listas** (cria múltiplas variações do mesmo elemento)
  - `v-for`

3. **Condicionais** (se o elemento é renderizado/mostrado)
  - `v-if`
  - `v-else-if`
  - `v-else`
  - `v-show`
  - `v-cloak`

4. **Modificadores de Renderização** (muda a forma como o elemento renderiza)
  - `v-pre`
  - `v-once`

5. Consciência Global (requer conhecimento além do componente)
  - `id`

6. **Atributos Únicos** (atributos que requerem valores únicos)
  - `ref`
  - `key`
  - `slot`

7. **Vinculação Bidirecional** (combinando vinculação e eventos)
  - `v-model`

8. **Outros Atributos** (todos os não especificados atributos limitados ou não)

9. **Eventos** (escutadores de evento dos componentes)
  - `v-on`

10. **Conteúdo** (Sobrescreve o conteúdo do elemento)
  - `v-html`
  - `v-text`

### Linhas vazias em opções de componentes/instâncias <sup data-p="c">Recomendado</sup>

**Você pode adicionar uma linha vazia entre as propriedades de várias linhas, principalmente se as opções não puderem mais caber na tela sem rolagem.**

Quando os componentes começam a parecer apertados ou difíceis de ler, a adição de espaços entre as propriedades de várias linhas pode facilitar o deslizamento na tela. Em alguns editores, como o Vim, opções de formatação como essa também podem facilitar a navegação com o teclado.

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bom Exemplo

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

### Ordem dos elementos de nível superior do componente single-file <sup data-p="c">Recomendado</sup>

**[Componentes single-file](../guide/single-file-components.html) devem sempre ordenar as tags `<script>`, `<template>`, e `<style>`, consistentemente, com `<style>` por último, já que um dos dois primeiros sempre é necessário.**

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

## Prioridade D: Use Cautelosamente (Padrões Potencialmente Perigosos)

### `v-if`/`v-else-if`/`v-else` sem `key` <sup data-p="d">Use Cautelosamente</sup>

**É usualmente melhor usar `key` com `v-if` + `v-else`, se eles são do mesmo tipo de elemento (ex. dois elementos `<div>`).**

Por padrão, o Vue atualiza o DOM da forma mais eficiente possível. Isso significa que, ao alternar entre elementos do mesmo tipo, ele simplesmente atualiza o elemento existente, em vez de removê-lo e adicionar um novo em seu lugar. Isso pode ter [efeitos colaterais não intencionais](https://jsfiddle.net/chrisvfritz/bh8fLeds/) se esses elementos não devessem realmente ser considerados os mesmos.

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

``` html
<div v-if="error">
  Erro: {{ error }}
</div>
<div v-else>
  {{ results }}
</div>
```
{% raw %}</div>{% endraw %}

{% raw %}<div class="style-example example-good">{% endraw %}
#### Bom Exemplo

``` html
<div
  v-if="error"
  key="search-status"
>
  Erro: {{ error }}
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
  Erro: {{ error }}
</p>
<div v-else>
  {{ results }}
</div>
```
{% raw %}</div>{% endraw %}

### Seletores de elemento com `scoped` <sup data-p="d">Use Cautelosamente</sup>

**Os seletores de elemento devem ser evitados quando usados com `scoped`.**

Prefira seletores de classe ao invés de seletores de elemento, em estilos com `scoped`, porque grandes números de seletores de elemento são lentos.

{% raw %}
<details>
<summary>
  <h4>Explicação Detalhada</h4>
</summary>
{% endraw %}

Para estilos com `scope`, o Vue adiciona um atributo exclusivo aos elementos do componente, como `data-v-f3f3eg9`. Em seguida, os seletores são modificados para que apenas os elementos correspondentes com esse atributo sejam selecionados (por exemplo, `button [data-v-f3f3eg9]`).

O problema é que uma grande quantidade de [seletores do atributo elemento](http://stevesouders.com/efws/css-selectors/csscreate.php?n=1000&sel=a%5Bhref%5D&body=background%3A+%23CFD&ne=1000) (ex: `button[data-v-f3f3eg9]`) irá consideravelmente deixá-lo mais lento que  [seletores do atributo classe](http://stevesouders.com/efws/css-selectors/csscreate.php?n=1000&sel=.class%5Bhref%5D&body=background%3A+%23CFD&ne=1000) (ex: `.btn-close[data-v-f3f3eg9]`), então os seletores de classe devem ser usados sempre que possível.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

### Comunicação implícita entre componentes pai-filho <sup data-p="d">Use Cautelosamente</sup>

**Props e eventos devem ser usados preferencialmente para a comunicação entre componentes pai-filho, em vez de `this.$parent` ou mutação de props.**

Uma aplicação ideal do Vue é usada com props para baixo e eventos para acima. Aderindo a esta convenção torna seus componentes muito mais fáceis de entender. No entanto, há casos extremos em que a mutação de prop ou `this.$Parent` pode simplificar dois componentes que já estão profundamente acoplados.

O problema é que também existem muitos casos _simples_ em que esses padrões podem oferecer conveniência. Cuidado: não se deixe seduzir por abrir mão de simplicidade (poder entender o fluxo do seu estado) em detrimento de conveniência de curto prazo (escrever menos código).

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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

### Gerenciamento de estado sem fluxo <sup data-p="d">Use Cautelosamente</sup>

**[Vuex](https://github.com/vuejs/vuex) deve ser preferido para o gerenciamento de estado global, em vez de `this.$root` ou um barramento de eventos global.**

Gerenciar o estado com `this.$root` e/ou usando um [barramento de eventos global](https://vuejs.org/v2/guide/migration.html#dispatch-and-broadcast-replaced) pode ser conveniente para casos muito simples, mas não são apropriados para a maioria dos aplicativos. O Vuex oferece não apenas um local central para gerenciar o estado, mas também ferramentas para organizar, rastrear e depurar alterações de estado.

{% raw %}</details>{% endraw %}

{% raw %}<div class="style-example example-bad">{% endraw %}
#### Mal Exemplo

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
#### Bom Exemplo

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
    none: '<span title="Infelizmente, não há como aplicar automaticamente essa regra.">autodisciplina</span>',
    runtime: 'runtime error',
    linter: '<a href="https://github.com/vuejs/eslint-plugin-vue#eslint-plugin-vue" target="_blank" rel="noopener noreferrer">plugin:vue/recommended</a>'
  }
  Vue.component('sg-enforcement', {
    template: '\
      <span>\
        <strong>Aplicação</strong>:\
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
