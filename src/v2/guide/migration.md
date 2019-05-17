---
title: Migração do Vue 1.x
type: guide
order: 701
---

## FAQ

> Wow - essa página é super longa! Isso significa que a versão 2.0 é completamente diferente, eu terei que aprender o básico de novo e a migração será praticamente impossivel?

Fico feliz que você tenha perguntado! A resposta é não. Cerca de 90% da API é a mesma e os conceitos básicos não mudaram. É longo porque queremos oferecer explicações muito detalhadas e incluir muitos exemplos. Tenha certeza, __isso não é algo que você precisa ler de cima para baixo!__

> Por onde devo começar em uma migração?

1. Comece executando o [assistente de migração](https://github.com/vuejs/vue-migration-helper)  em um projeto atual. Nós cuidadosamente minificamos e comprimimos um desenvolvedor sênior do Vue em uma interface de linha de comando simples. Sempre que eles reconhecem um recurso obsoleto, eles irão te informar, oferecer sugestões e fornecer links para mais informações.

2. Depois disso, navegue pela tabela de conteúdos desta página na barra lateral. Se você ver um tópico pelo o qual pode ser afetado, mas o assistente de migração não pegou, confira.

3. Se você tiver testes, execute-os e veja o que ainda falha. Se não tiver, basta abrir o app em seu navegador e ficar atento para avisos ou erros ao navegar.

4. Por enquanto, seu app deve ter migrado totalmente. Se você ainda deseja mais, pode ler o resto desta página - ou mergulhar no novo e melhorado [guia de início](index.html). Muitas partes serão elimináveis, já que você já está familiarizado com os conceitos básicos.

> Quanto demorará para migrar um app Vue da versão 1.x para a 2.0?

Depende de alguns fatores:

- O tamanho do seu app (app pequeno ou médio provavelmente será inferior a um dia)

- Quantas vezes você se distrai e começa a brincar com um recurso novo e legal. 😉&nbsp;Não julgando, também aconteceu conosco enquanto construíamos a versão 2.0!

- Quais recursos obsoletos você está usando. A maioria pode ser atualizada com encontrar-e-substituir, mas outros podem demorar alguns minutos. Se você não estiver seguindo as práticas recomendadas, o Vue 2.0 também tentará forçar você a fazê-lo. Isso é bom no longo prazo, mas também pode significar um refatoramento significativo (embora possivelmente em atraso).

> Se eu atualizar para o Vue 2, também terei que atualizar o Vuex e o Vue Router?

Somente o Vue Router 2 é compatível com o Vue 2, então sim, você também terá que seguir o [caminho de migração do Vue Router](migration-vue-router.html). Felizmente, a maioria dos apps não tem muito código de roteador, então provavelmente não demorará mais de uma hora.

Quanto ao Vuex, até a versão 0.8 é compatível com o Vue 2, portanto, você não está obrigado a atualizar. A única razão pela qual você pode querer atualizar imediatamente é aproveitar os novos recursos do Vuex 2, como módulos e boilerplate reduzido.

## Templates

### Instâncias de Fragmento <sup>removido</sup>

Cada componente deve ter exatamente um elemento raiz. As instâncias de fragmento não são mais permitidas. Se você tem um template como este:

``` html
<p>foo</p>
<p>bar</p>
```

Recomenda-se envolver todo o conteúdo em um novo elemento, como este:

``` html
<div>
  <p>foo</p>
  <p>bar</p>
</div>
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute seu conjunto de testes de ponta a ponta ou app após a atualização e procure <strong>avisos no console</strong> sobre vários elementos raiz em um template.</p>
</div>
{% endraw %}

## Gatilhos do Ciclo de Vida

### `beforeCompile` <sup>removido</sup>

Use o gatilho `created` no lugar.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar todos os exemplos desse gatilho.</p>
</div>
{% endraw %}

### `compiled`  <sup>substituído</sup>

Use o novo gatilho `mounted` no lugar.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar todos os exemplos desse gatilho.</p>
</div>
{% endraw %}

### `attached` <sup>removido</sup>

Use uma verificação personalizada no DOM em outros gatilhos. Por exemplo, para substituir:

``` js
attached: function () {
  doSomething()
}
```

Você pode usar:

``` js
mounted: function () {
  this.$nextTick(function () {
    doSomething()
  })
}
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar todos os exemplos desse gatilho.</p>
</div>
{% endraw %}

### `detached` <sup>removido</sup>

Use uma verificação personalizada no DOM em outros gatilhos. Por exemplo, para substituir:

``` js
detached: function () {
  doSomething()
}
```

Você pode usar:

``` js
destroyed: function () {
  this.$nextTick(function () {
    doSomething()
  })
}
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar todos os exemplos desse gatilho.</p>
</div>
{% endraw %}

### `init` <sup>renomeado</sup>

Use o novo gatilho `beforeCreate` em vez disso, que é essencialmente o mesmo. Foi renomeado para consistência com outros métodos do ciclo de vida.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar todos os exemplos desse gatilho.</p>
</div>
{% endraw %}

### `ready` <sup>substituído</sup>

Use o novo gatilho `mounted` em vez disso. Deve notar-se que, com `mounted`, não há garantia de estar no documento pronto. Para isso, também inclua `Vue.nextTick`/`vm.$nextTick`. Por exemplo:

``` js
mounted: function () {
  this.$nextTick(function () {
    // código que assume que o this.$el está no documento
  })
}
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar todos os exemplos desse gatilho.</p>
</div>
{% endraw %}

## `v-for`

### Ordem dos Argumentos para Arrays no `v-for` <sup>mudado</sup>

Ao incluir um `índice`, a ordem dos argumentos para arrays costumava ser `(índice, valor)`. Agora é `(valor, índice)` para ser mais consistente com os métodos de array nativos do JavaScript, como por exemplo, `forEach` e `map`.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de ordem de argumentos obsoleta. Observe que, se você nomear seus argumentos de índice algo incomum como <code>position</code> ou <code>num</code>, o assistente não irá sinalizá-los.</p>
</div>
{% endraw %}

### Ordem dos Argumentos para Objects no `v-for` <sup>mudado</sup>

<<<<<<< HEAD
Ao incluir uma `chave`, a ordem dos argumentos para objetos costumava ser `(chave, valor)`. Agora é `(valor, chave)` para ser mais consistente com os iteradores de objetos comuns, como o `lodash`.
=======
When including a property name/key, the argument order for objects used to be `(name, value)`. It is now `(value, name)` to be more consistent with common object iterators such as lodash's.
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de ordem de argumentos obsoleta. Observe que, se você nomear seus argumentos de chave como <code>name</code> ou <code>property</code>, o assistente não os sinalizará.</p>
</div>
{% endraw %}

### `$index` e `$key` <sup>removidos</sup>

As variáveis `$index` e `$key` implicitamente atribuídas foram removidas em favor de definí-las explicitamente no `v-for`. Isso torna o código mais fácil de ler para desenvolvedores menos experientes com Vue e também resulta em um comportamento muito mais claro ao lidar com loops aninhados.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos dessas variáveis removidas. Se você perder alguma, também poderá ver <strong>erros no console</strong>, como: <code>Uncaught ReferenceError: $index is not defined</code></p>
</div>
{% endraw %}

### `track-by` <sup>substituído</sup>

`track-by` foi substituído por uma `key`, que funciona como qualquer outro atributo: sem o `v-bind:` ou o prefixo  `:`, é tratado como uma string literal. Na maioria dos casos, você desejará usar uma vinculação dinâmica que espera uma expressão completa ao invés de uma chave. Por exemplo, no lugar de:

{% codeblock lang:html %}
<div v-for="item in items" track-by="id">
{% endcodeblock %}

Agora você irá escrever:

{% codeblock lang:html %}
<div v-for="item in items" v-bind:key="item.id">
{% endcodeblock %}

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>track-by</code>.</p>
</div>
{% endraw %}

### Intervalo de Valores do `v-for` <sup>modificado</sup>

Antes, `v-for="numero in 10"` teria o `numero` começando em 0 e terminando em 9. Agora começa no 1 e termina no 10.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Procure na sua base de código pelo regex <code>/\w+ in \d+/</code>. Onde quer que apareça em um <code>v-for</code>, verifique se você pode ser afetado.</p>
</div>
{% endraw %}

## Props

### Opção `coerce` de Props <sup>removido</sup>

Se você quiser coagir uma prop, configure um valor computado local com base nisso. Por exemplo, em vez de:

``` js
props: {
  username: {
    type: String
    coerce: function (value) {
      return value
        .toLowerCase()
        .replace(/\s+/, '-')
    }
  }
}
```

Você pode escrever:

``` js
props: {
  username: String
},
computed: {
  normalizedUsername: function () {
    return this.username
      .toLowerCase()
      .replace(/\s+/, '-')
  }
}
```

Existem algumas vantagens:

- Você ainda tem acesso ao valor original do prop.
- Você é forçado a ser mais explícito, dando ao valor coagido um nome que o diferencia do valor passado na prop.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos da opção <code>coerce</code>.</p>
</div>
{% endraw %}

### Opção `twoWay` de Props <sup>removido</sup>

Props agora estão sempre em sentido único. Para produzir efeitos colaterais no escopo dos pais, um componente precisa emitir um evento explicitamente em vez de depender da vinculação implícita. Para mais informações, veja:

- [Eventos de componentes customizados](components.html#Custom-Events)
- [Componentes de inputs customizados](components.html#Form-Input-Components-using-Custom-Events) (usando eventos de componente)
- [Gerenciamento de estado global](state-management.html)

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos da opção <code>twoWay</code>.</p>
</div>
{% endraw %}

### Modificadores `.once` e `.sync` no `v-bind` <sup>removido</sup>

Props agora estão sempre em sentido único. Para produzir efeitos colaterais no escopo dos pais, um componente precisa emitir um evento explicitamente em vez de depender da vinculação implícita. Para mais informações, veja:

- [Eventos de componentes customizados](components.html#Custom-Events)
- [Componentes de inputs customizados](components.html#Form-Input-Components-using-Custom-Events) (usando eventos de componente)
- [Gerenciamento de estado global](state-management.html)

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos dos modificadores <code>.once</code> e <code>.sync</code>.</p>
</div>
{% endraw %}

### Mutação de Prop <sup>descontinuado</sup>

Mutar um prop local agora é considerado anti-padrão, por exemplo, declarando um suporte e em seguida, definindo `this.myProp = 'someOtherValue'` no componente. Devido ao novo mecanismo de renderização, sempre que o componente pai voltar a renderizar, as alterações locais do componente filho serão substituídas.

A maioria dos casos de mutar um prop pode ser substituído por uma dessas opções:

- uma propriedade `data`, com o prop usado para definir seu valor padrão
- uma propriedade computada

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute seu teste de ponta a ponta ou app depois da atualização e procure por <strong>avisos no console</strong> sobre mutação de props.</p>
</div>
{% endraw %}

### Props em uma Instância Raiz <sup>substituído</sup>

Nas instâncias raiz do Vue (criadas com `new Vue ({ ... })`), você deve usar `propsData` ao invés de `props`.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute seu teste de ponta a ponta, se você tem um. Os <strong>testes com falha</strong> devem alertar o fato de que props passadas para instâncias raiz não funcionam mais.</p>
</div>
{% endraw %}

## Propriedades Computadas

### `cache: false` <sup>descontinuado</sup>

A invalidação de cache das propriedades computadas será removida nas futuras versões principais do Vue. Substitua quaisquer propriedade computada não cacheada por métodos, que terá o mesmo resultado.

Por exemplo:

``` js
template: '<p>mensagem: {{ timeMessage }}</p>',
computed: {
  timeMessage: {
    cache: false,
    get: function () {
      return Date.now() + this.message
    }
  }
}
```

Ou com métodos de componente:

``` js
template: '<p>mensagem: {{ getTimeMessage() }}</p>',
methods: {
  getTimeMessage: function () {
    return Date.now() + this.message
  }
}
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos da opção <code>cache: false</code>.</p>
</div>
{% endraw %}

## Diretivas Nativas do Vue

### Verdadeiro/Falso com `v-bind` <sup>modificado</sup>

Quando usado com `v-bind`, os únicos valores falsos são agora: `null`, `undefined` e `false`. Isso significa que `0` e arrays vazios renderizarão como verdadeiro. Então, por exemplo, `v-bind: draggable = "''"` renderizará como `draggable = "true"`.

Para atributos enumerados, além dos valores falsos acima, a string `"false"` também será renderizada como `attr = "false"`.

<p class="tip">Observe que para outras diretivas (<code>v-if</code> e <code>v-show</code> por exemplo), o verdadeiro do Javascript normal ainda se aplica.</p>

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute seus testes de ponta a ponta, se você tiver. Os <strong>testes com falha</strong> devem te alertar para qualquer parte do seu app que possa ser afetada por essa alteração.</p>
</div>
{% endraw %}

### Escutando Eventos Nativos nos Componentes com `v-on` <sup>modificado</sup>

Quando usando em um componente, `v-on` agora só escuta eventos customizados `$emit`idos por aquele componente. Para escutar um evento nativo do DOM no elemento raiz, você pode usar o modificador `.native`. Por exemplo:

{% codeblock lang:html %}
<my-component v-on:click.native="doSomething"></my-component>
{% endcodeblock %}

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute seu teste de ponta a ponta, se você tiver um. Os <strong>testes com falha</strong> devem te alertar para qualquer parte do seu app que possa ser afetada por essa alteração.</p>
</div>
{% endraw %}

### Atributo `debounce` de Parâmetro para `v-model` <sup>removido</sup>

Debounce é usado para limitar a frequência com que executamos os pedidos Ajax e outras operações custosas. O atributo `debounce` do Vue para `v-model` tornou isso fácil para casos muito simples, mas na verdade atrasa as __atualizações de estado__ em vez das operações custosas. É uma diferença sutil, mas vem com limitações à medida que uma aplicação cresce.

Essas limitações tornam-se aparentes ao projetar um indicador de pesquisa, como este por exemplo:

{% raw %}
<script src="https://cdn.jsdelivr.net/lodash/4.13.1/lodash.js"></script>
<div id="debounce-search-demo" class="demo">
  <input v-model="searchQuery" placeholder="Digite algo">
  <strong>{{ searchIndicator }}</strong>
</div>
<script>
new Vue({
  el: '#debounce-search-demo',
  data: {
    searchQuery: '',
    searchQueryIsDirty: false,
    isCalculating: false
  },
  computed: {
    searchIndicator: function () {
      if (this.isCalculating) {
        return '⟳ Buscando novos resultados'
      } else if (this.searchQueryIsDirty) {
        return '... Digitando'
      } else {
        return '✓ Pronto'
      }
    }
  },
  watch: {
    searchQuery: function () {
      this.searchQueryIsDirty = true
      this.expensiveOperation()
    }
  },
  methods: {
    expensiveOperation: _.debounce(function () {
      this.isCalculating = true
      setTimeout(function () {
        this.isCalculating = false
        this.searchQueryIsDirty = false
      }.bind(this), 1000)
    }, 500)
  }
})
</script>
{% endraw %}

Usando o atributo `debounce`, não haveria nenhuma maneira de detectar o estado "Digitação", pois perdemos acesso ao estado em tempo real do input. Ao desacoplar a função de atraso do Vue no entanto, podemos atrasar apenas a operação que queremos limitar, eliminando os limites nos recursos que podemos desenvolver.

``` html
<!--
Usando a função `debounce` do lodash ou outra
biblioteca de utilidades dedicada, sabemos que a
implementação de atraso específica que usamos 
será a melhor da sua classe - e podemos usá-la em QUALQUER lugar. 
Não somente no nosso template.
-->
<script src="https://cdn.jsdelivr.net/lodash/4.13.1/lodash.js"></script>
<div id="debounce-search-demo">
  <input v-model="searchQuery" placeholder="Digite algo">
  <strong>{{ searchIndicator }}</strong>
</div>
```

``` js
new Vue({
  el: '#debounce-search-demo',
  data: {
    searchQuery: '',
    searchQueryIsDirty: false,
    isCalculating: false
  },
  computed: {
    searchIndicator: function () {
      if (this.isCalculating) {
        return '⟳ Buscando novos resultados'
      } else if (this.searchQueryIsDirty) {
        return '... Digitando'
      } else {
        return '✓ Pronto'
      }
    }
  },
  watch: {
    searchQuery: function () {
      this.searchQueryIsDirty = true
      this.expensiveOperation()
    }
  },
  methods: {
    // É aqui que o debounce realmente deve ficar.
    expensiveOperation: _.debounce(function () {
      this.isCalculating = true
      setTimeout(function () {
        this.isCalculating = false
        this.searchQueryIsDirty = false
      }.bind(this), 1000)
    }, 500)
  }
})
```

Outra vantagem desta abordagem é que haverá momentos em que o `debounce` não é a função wrapper correta. Por exemplo, ao usar uma API para sugestões de pesquisa, esperar para oferecer sugestões até que o usuário pare de digitar por um período de tempo não é uma experiência ideal. O que você provavelmente quer, em vez disso, é uma função de __limitação__. Agora, já que você está usando uma biblioteca de utilidades como lodash, a refatoração para usar a função `throttle`, leva apenas alguns segundos.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos do atributo <code>debounce</code>.</p>
</div>
{% endraw %}

### Atributos `lazy` ou `number` de Paramêtros para `v-model` <sup>substituído</sup>

Os atributos `lazy` e `number` agora são modificadores, para tornar mais claro o que isso significa, ao invés de:

``` html
<input v-model="name" lazy>
<input v-model="age" type="number" number>
```

Você usará:

``` html
<input v-model.lazy="name">
<input v-model.number="age" type="number">
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos destes atributos.</p>
</div>
{% endraw %}

### Atributo `value` com `v-model` <sup>removido</sup>

Agora o `v-model` não se preocupa mais com o valor inicial do atributo `value`. Em vez disso, tratará os dados da instância Vue como a fonte da verdade.

Isso significa que esse elemento:

``` html
<input v-model="text" value="foo">
```

apoiado por esses dados:

``` js
data: {
  text: 'bar'
}
```

vai renderizar um valor de "bar" ao invés de "foo". O mesmo vale para `<textarea>` com conteúdo existente. Ao invés de:

``` html
<textarea v-model="text">
  hello world
</textarea>
```

Você deve garantir que seu valor inicial para `text` é "hello world".

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute seu teste de ponta a ponta, após atualizar o app e busque por <strong>avisos no console</strong> sobre linhas de atributo <code>value</code> com <code>v-model</code>.</p>
</div>
{% endraw %}

### `v-model` com `v-for` Iterando Valores Primitivos <sup>removido</sup>

Casos como esse não funcionam mais:

``` html
<input v-for="str in strings" v-model="str">
```

A motivo é que este é o equivalente JavaScript para o qual o `<input>` compilaria:

``` js
strings.map(function (str) {
  return createElement('input', ...)
})
```

Como você pode ver, o vínculo bidirecional do `v-model` não faz sentido aqui. Definir `str` para outro valor na função de iteração não fará nada porque é apenas uma variável local no escopo da função.

Em vez disso, você deve usar um array de __objects__  para que `v-model` possa atualizar o campo no objeto. Por exemplo:

{% codeblock lang:html %}
<input v-for="obj in objects" v-model="obj.str">
{% endcodeblock %}

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute seu teste de ponta a ponta, se você tiver um. Os <strong>testes com falha</strong> devem te alertar para qualquer parte do seu app que possa ser afetada por essa alteração.</p>
</div>
{% endraw %}

### `v-bind:style` com Sintaxe de Objeto e `!important` <sup>removido</sup>

Isso não irá mais funcionar:

``` html
<p v-bind:style="{ color: myColor + ' !important' }">Olá</p>
```

Se você realmente precisa substituir outro `!important`, você deve usar a sintaxe de string:

``` html
<p v-bind:style="'color: ' + myColor + ' !important'">Olá</p>
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de vinculação de estilo com <code>!important</code> em objetos.</p>
</div>
{% endraw %}

### `v-el` e `v-ref` <sup>subtituído</sup>

Para simplificar, `v-el` e `v-ref` foram incorporados no atributo `ref`, acessível em uma instância de componente por meio de `$refs`. Isso significa que `v-el:my-element` se tornaria `ref="myElement"` e `v-ref:my-component` se tornaria `ref="myComponent"`. Quando usado em um elemento normal, o `ref` será o elemento DOM, e quando usado em um componente, a `ref` será a instância do componente.

Como o `v-ref` não é mais uma diretiva, mas um atributo especial, ele também pode ser definido dinamicamente. Isto é especialmente útil em combinação com `v-for`. Por exemplo:

``` html
<p v-for="item in items" v-bind:ref="'item' + item.id"></p>
```

Anteriormente, `v-el`/`v-ref` combinado com `v-for` produziria um array de elementos/componentes, pois não havia como dar a cada item um nome único. Você ainda pode obter este comportamento dando a cada item o mesmo `ref`:

``` html
<p v-for="item in items" ref="items"></p>
```

Diferente das versões 1.x, esses `$refs` não são reativos, porque eles são registrados/atualizados durante o próprio processo de renderização. Fazê-los reativos exigiria renderizações duplicadas para cada mudança.

Por outro lado, `$refs` são projetados principalmente para acesso programático em JavaScript - não é recomendado confiar neles em templates, pois isso significaria referir-se ao estado que não pertence à instância em si. Isso violaria o modelo de visão orientado por dados do Vue.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>v-el</code> e <code>v-ref</code>.</p>
</div>
{% endraw %}

### `v-else` com `v-show` <sup>removido</sup>

`v-else` não funciona mais com `v-show`. Use `v-if` com uma expressão de negação ao invés disso. Por exemplo, ao invés de:

``` html
<p v-if="foo">Foo</p>
<p v-else v-show="bar">Não foo, mas bar</p>
```

Você pode usar:

``` html
<p v-if="foo">Foo</p>
<p v-if="!foo && bar">Não foo, mas bar</p>
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>v-else</code> e <code>v-show</code>.</p>
</div>
{% endraw %}

## Diretivas Customizadas <sup>simplificado</sup>

As diretivas têm um escopo de responsabilidade muito reduzido: agora são usadas apenas para a aplicação de manipulações DOM diretas de baixo nível. Na maioria dos casos, você preferirá usar componentes como a principal abstração de reuso de código.

Algumas das diferenças mais notáveis incluem:

- As diretivas não têm mais instâncias. Isso significa que não há mais `this` dentro de diretivas gatilho. Em vez disso, eles recebem tudo o que podem precisar como argumentos. Se você realmente tem de persistir o estado através dos gatilhos, você pode fazer isso no `el`.
- Opções assim como `acceptStatement`, `deep`, `priority`, etc foram todas removidas. Para substituir as diretivas `twoWay`, veja [esse exemplo](#Two-Way-Filters-replaced).
- Alguns dos gatilhos atuais tem um comportamento diferente e também existem novos gatilhos.

Felizmente, desde que as novas diretivas estão mais simples, você pode dominá-las mais facilmente. Leia o novo [Guia de customização de diretivas](custom-directive.html) para aprender mais.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de diretivas definidas. O assistente sinalizará todos eles, é provável que na maioria dos casos você desejará refatorar para um componente.</p>
</div>
{% endraw %}

### Modificador de Diretiva `.literal` <sup>removido</sup>

O modificador `literal` foi removido pois o mesmo pode ser facilmente alcançado fornecendo uma string literal como valor.

Por exemplo, você pode atualizar:

``` html
<p v-my-directive.literal="foo bar baz"></p>
```

para:

``` html
<p v-my-directive="'foo bar baz'"></p>
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos do modificador <code>.literal</code> em diretivas.</p>
</div>
{% endraw %}

## Transições

### Atributo `transition` <sup>substituído</sup>

O sistema de transições do Vue mudou um pouco drasticamente e agora usamos `<transition>` e `<transition-group>` como elementos wrapper, ao invés do atributo `transition`. É recomendado ler o novo [Guia de transições](transitions.html) para aprendar mais.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos do atributo <code>transition</code>.</p>
</div>
{% endraw %}

### `Vue.transition` para Transições Reutilizáveis <sup>substituído</sup>

Com o novo sistema de transições, agora você pode [usar componentes para transições reutilizáveis](transitions.html#Reusable-Transitions).

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos do <code>Vue.transition</code>.</p>
</div>
{% endraw %}

### Atributo de Transição `stagger` <sup>removido</sup>

Se precisa escalonar as transições da lista, você pode controlar o tempo ajustando e acessando um `data-index` (ou atributo similar) em um elemento. Veja [um exemplo aqui](transitions.html#Staggering-List-Transitions).

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos do atributo <code>transition</code>. Durante sua atualização, você pode fazer a transição (trocadilho muito intencional) para a nova estratégia de escalonação também.</p>
</div>
{% endraw %}

## Eventos

### Opção `events` <sup>removido</sup>

Agora os manipuladores de eventos devem ser registrados no gatilho `created` ao invés disso. Veja o [guia de migração para `$dispatch` e `$broadcast`](#dispatch-and-broadcast-replaced) para exemplo detalhado.

### `Vue.directive('on').keyCodes` <sup>substituído</sup>

A nova e mais concisa maneira de configurar `keyCodes` é através de `Vue.config.keyCodes`. Por exemplo:

``` js
// ative v-on:keyup.f1
Vue.config.keyCodes.f1 = 112
```
{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos da antiga sintaxe de configuração <code>keyCode</code>.</p>
</div>
{% endraw %}

### `$dispatch` e `$broadcast` <sup>substituído</sup>

`$dispatch` e `$broadcast` foram removidos em favor de uma comunicação mais explícita entre componentes e soluções de gerenciamento de estado mais sustentáveis, como o [Vuex](https://github.com/vuejs/vuex).

O problema é que os fluxos de eventos que dependem da estrutura de árvore de um componente podem ser difíceis de serem entendidos e muito frágeis quando a árvore se torna grande. Eles não escalam bem e apenas o prepara para dor mais tarde. `$dispatch` e `$broadcast` também não resolvem a comunicação entre componentes irmãos.

Um dos usos mais comuns para esses métodos é se comunicar entre um pai e seus filhos diretos. Nesses casos, você pode [escutar um `$emit` de um filho com `v-on`](components.html#Form-Input-Components-using-Custom-Events). Isso permite que você mantenha a conveniência dos eventos com uma explicitação adicional.

No entanto, quando se comunica entre descendentes distantes e ancestrais, `$emit` não irá ajudá-lo. Em vez disso, a atualização mais simples possível seria usar um _hub_ de eventos centralizado. Isso tem o benefício adicional de permitir que você se comunique entre componentes, independentemente de onde eles estão na árvore de componentes - mesmo entre irmãos! Como as instâncias do Vue implementam uma interface de emissor de eventos, você pode usar uma instância do Vue vazia para esse propósito.

Por exemplo, digamos que temos um app _ToDo_ estruturado assim:

```
Todos
├─ NewTodoInput
└─ Todo
   └─ DeleteTodoButton
```

Podemos gerenciar a comunicação entre componentes com este único _hub_ de eventos:

``` js
// Este é o hub de eventos que usaremos em cada
// componente para se comunicar entre eles
var eventHub = new Vue()
```

Então, em nossos componentes podemos usar `$emit`, `$on`, `$off` para emitir eventos, escutar eventos e limpar os escutadores de eventos, respectivamente:

``` js
// NewTodoInput
// ...
methods: {
  addTodo: function () {
    eventHub.$emit('add-todo', { text: this.newTodoText })
    this.newTodoText = ''
  }
}
```

``` js
// DeleteTodoButton
// ...
methods: {
  deleteTodo: function (id) {
    eventHub.$emit('delete-todo', id)
  }
}
```

``` js
// Todos
// ...
created: function () {
  eventHub.$on('add-todo', this.addTodo)
  eventHub.$on('delete-todo', this.deleteTodo)
},
// É bom limpar os escutadores de evento
// antes de um componente ser destruído.
beforeDestroy: function () {
  eventHub.$off('add-todo', this.addTodo)
  eventHub.$off('delete-todo', this.deleteTodo)
},
methods: {
  addTodo: function (newTodo) {
    this.todos.push(newTodo)
  },
  deleteTodo: function (todoId) {
    this.todos = this.todos.filter(function (todo) {
      return todo.id !== todoId
    })
  }
}
```

Esse padrão pode servir como um substituto para `$dispatch` e `$broadcast` em cenários simples, mas para casos mais complexos, é recomendável usar uma camada de gerenciamento de estado dedicada, como o [Vuex](https://github.com/vuejs/vuex).

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>$dispatch</code> e <code>$broadcast</code>.</p>
</div>
{% endraw %}

## Filtros

### Filtros Fora da Interpolação de Texto <sup>removido</sup>

Os filtros agora só podem ser usados dentro das interpolações de texto (tags `{% raw %}{{ }}{% endraw %}`). No passado, descobrimos que usar filtros dentro de diretivas, como `v-model`, `v-on`, etc levaram a mais complexidade do que o conveniente. Para a filtragem de lista no `v-for`, também é melhor mover essa lógica para JavaScript como propriedades computadas, para que também possa ser reutilizado em todo o seu componente.

Em geral, sempre que algo puder ser alcançado em JavaScript simples, queremos evitar a introdução de uma sintaxe especial como filtros para cuidar da mesma preocupação. Veja como você pode substituir as diretivas internas de filtros do Vue:

#### Substituíndo o Filtro `debounce`

Ao invés de:

``` html
<input v-on:keyup="doStuff | debounce 500">
```

``` js
methods: {
  doStuff: function () {
    // ...
  }
}
```

Use o [`debounce` do lodash](https://lodash.com/docs/4.15.0#debounce) (ou possivelmente o [`throttle`](https://lodash.com/docs/4.15.0#throttle)) para limitar diretamente a chamada do método custoso. Você pode alcançar o mesmo que acima, com isso:

``` html
<input v-on:keyup="doStuff">
```

``` js
methods: {
  doStuff: _.debounce(function () {
    // ...
  }, 500)
}
```

Para obter mais informações sobre as vantagens desta estratégia, veja [o exemplo com `v-model`](#debounce-Param-Attribute-for-v-model-removed).

#### Substituíndo o Filtro `limitBy`

Ao invés de:

``` html
<p v-for="item in items | limitBy 10">{{ item }}</p>
```

Use o método do Javascript [`.slice`](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Array/slice#Examples) em uma propriedade computada:

``` html
<p v-for="item in filteredItems">{{ item }}</p>
```

``` js
computed: {
  filteredItems: function () {
    return this.items.slice(0, 10)
  }
}
```

#### Substituíndo o Filtro `filterBy`

Ao invés de:

``` html
<p v-for="user in users | filterBy searchQuery in 'name'">{{ user.name }}</p>
```

Use o método do Javascript [`.filter`](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Array/filter#Examples) em uma propriedade computada:

``` html
<p v-for="user in filteredUsers">{{ user.name }}</p>
```

``` js
computed: {
  filteredUsers: function () {
    var self = this
    return self.users.filter(function (user) {
      return user.name.indexOf(self.searchQuery) !== -1
    })
  }
}
```

O método nativo `filter` também pode gerenciar operações de filtragem muito mais complexas, porque você tem acesso ao poder total do JavaScript dentro das propriedades computadas. Por exemplo, se você quisesse encontrar todos os usuários ativos e buscar de forma insensível a maiúsculas e minúsculas ambos nome e email:

``` js
var self = this
self.users.filter(function (user) {
  var searchRegex = new RegExp(self.searchQuery, 'i')
  return user.isActive && (
    searchRegex.test(user.name) ||
    searchRegex.test(user.email)
  )
})
```

#### Substituíndo o Filtro `orderBy`

Ao invés de:

``` html
<p v-for="user in users | orderBy 'name'">{{ user.name }}</p>
```

Use o [`orderBy` do lodash](https://lodash.com/docs/4.15.0#orderBy) (ou possivelmente o [`sortBy`](https://lodash.com/docs/4.15.0#sortBy)) em uma propriedade computada:

``` html
<p v-for="user in orderedUsers">{{ user.name }}</p>
```

``` js
computed: {
  orderedUsers: function () {
    return _.orderBy(this.users, 'name')
  }
}
```

Você pode até ordenar por múltiplas colunas:

{% codeblock lang:js %}
_.orderBy(this.users, ['name', 'last_login'], ['asc', 'desc'])
{% endcodeblock %}

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de filtros sendo usados dentro de diretivas. Se você perder algum, também poderá ver <strong>erros no console</strong>.</p>
</div>
{% endraw %}

### Sintaxe de Argumentos para Filtros <sup>modificado</sup>

A sintaxe para argumentos agora se alinha melhor com a invocação de função Javascript. Então ao invés de usar argumentos delimitados por espaço:

``` html
<p>{{ date | formatDate 'YY-MM-DD' timeZone }}</p>
```

Nós cercamos os argumentos com parênteses e os delimitamos com vírgulas:

``` html
<p>{{ date | formatDate('YY-MM-DD', timeZone) }}</p>
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de filtros com a sintaxe antiga. Se você perder algum, também poderá ver <strong>erros no console</strong>.</p>
</div>
{% endraw %}

### Filtros de Texto Incorporados <sup>removido</sup>

Embora os filtros nas interpolações de texto ainda são permitidos, todos os filtros foram removidos. Em vez deles, é recomendável usar bibliotecas mais especializadas para resolver problemas em cada domínio (exemplos: [`date-fns`](https://date-fns.org/) para formatar datas e [`accounting`](http://openexchangerates.github.io/accounting.js/) para moedas.

Para cada um dos filtros de texto internos do Vue, mostraremos como você pode substituí-los abaixo. O código de exemplo poderá estar em funções auxiliares, métodos ou propriedades computadas.

#### Substituíndo o Filtro `json`

Você não precisa mais depurar, já que o Vue formatará bem a saída para você automaticamente, seja uma string, número, array ou objeto comum. Porém, se você quiser exatamente a mesma funcionalidade que o `JSON.stringify` do JavaScript, você pode usá-lo em um método ou propriedade computada.

#### Substituíndo o Filtro `capitalize`

``` js
text[0].toUpperCase() + text.slice(1)
```

#### Substituíndo o Filtro `uppercase`

``` js
text.toUpperCase()
```

#### Substituíndo o Filtro `lowercase`

``` js
text.toLowerCase()
```

#### Substituíndo o Filtro `pluralize`

O pacote [pluralize](https://www.npmjs.com/package/pluralize) no NPM atende bem este propósito, mas se você quer apenas pluralizar uma palavra específica ou ter uma saída especial para casos como `0`, então também pode definir facilmente suas próprias funções de `pluralize`. Por exemplo:

``` js
function pluralizeKnife (count) {
  if (count === 0) {
    return 'sem facas'
  } else if (count === 1) {
    return '1 faca'
  } else {
    return count + 'facas'
  }
}
```

#### Substituíndo o Filtro `currency`

Para uma implementação muito ingênua, você poderia fazer algo como isto:

<<<<<<< HEAD
``` js
'R$' + price.toFixed(2)
```
=======
{% codeblock lang:js %}
'$' + price.toFixed(2)
{% endcodeblock %}
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

Em muitos casos, porém, você ainda terá um comportamento estranho (exemplo: `0.035.toFixed(2)` arredonda para `0.04`, mas `0.045` arredonda para `0.04`). Para contornar esses problemas, você pode usar a biblioteca [`accounting`](http://openexchangerates.github.io/accounting.js/) para formatar moedas de forma mais confiável.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de filtros de texto obsoletos. Se você perder algum, também poderá ver <strong>erros no console</strong>.</p>
</div>
{% endraw %}

### Filtros Bidirecionais <sup>substituído</sup>

Alguns usuários gostaram de usar filtros bidirecionais com `v-model` para criar inputs interessantes com muito pouco código. Embora _aparentemente_ simples no entanto, os filtros bidirecionais também podem ocultar uma grande complexidade - e até mesmo encorajar uma UX pobre ao atrasar as atualizações do estado. Em vez disso, os componentes que envolvem um input são recomendados como uma maneira mais explícita e rica em recursos para criar inputs personalizados.

Como exemplo, agora vamos percorrer a migração de um filtro de moeda bidirecional:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/6744xnjk/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Na maior parte funciona bem, mas as atualizações de estado atrasadas podem causar um comportamento estranho. Por exemplo, clique na guia `Result`e tente digitar `9.999` em um desses inputs. Quando o input perde o foco, seu valor será atualizado para `$10.00`. Ao olhar para o total calculado no entanto, você verá que `9.999` é o que está armazenado em nossos dados. A versão da realidade que o usuário vê está fora de sincronia!

Para iniciar a transição para uma solução mais robusta usando o Vue 2.0, primeiro envolva esse filtro em um novo componente `<currency-input>`:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/943zfbsh/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Isso nos permite adicionar um comportamento que um filtro por si só não poderia encapsular, como selecionar o conteúdo de um input no foco. Agora, o próximo passo será extrair a lógica de negócios do filtro. Abaixo, puxamos tudo para um objeto externo [`currencyValidator`](https://gist.github.com/chrisvfritz/5f0a639590d6e648933416f90ba7ae4e):

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/9c32kev2/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Esse aumento da modularidade não só facilita a migração para a Vue 2, mas também permite que a análise e formatação da moeda sejam:

- testadas como unidades isoladas do seu código Vue
- usado por outras partes do seu app, como para validar a carga útil de uma ponta final de API

Tendo este validador extraído, nós também construíremos isso mais confortavelmente em uma solução mais robusta. As peculiaridades do estado foram eliminadas e, na verdade, é impossível para os usuários inserirem algo errado, semelhante ao que o input numérico nativo do navegador tenta fazer.

Ainda estamos limitados, porém, por filtros e pelo Vue 1.0 em geral, então vamos completar a atualização para o Vue 2.0:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/1oqjojjx/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Você pode notar que:

- Todos os aspectos do nosso input são mais explícitos, usando gatilhos do ciclo de vida e eventos DOM no lugar do comportamento oculto dos filtros bidirecionais.
- Agora podemos usar o `v-model` diretamente em nossos inputs personalizados, o que não é apenas mais consistente com as inputs normais, mas também significa que nosso componente é amigável ao Vuex.
- Uma vez que não estamos mais usando opções de filtro que exigem um valor a ser retornado, nosso trabalho de moeda pode ser feito assincronamente. Isso significa que se tivéssemos muitos apps que precisassem trabalhar com moedas, poderíamos facilmente refatorar essa lógica em um microservice compartilhado.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de filtros usados em diretivas como <code>v-model</code>. Se você perder algum, também poderá ver <strong>erros no console</strong>.</p>
</div>
{% endraw %}

## Slots

### Slots Duplicados <sup>removido</sup>

Não é mais suportado ter vários `<slot>` com o mesmo nome no mesmo template. Quando um slot é renderizado, ele é "usado" e não pode ser renderizado em outro lugar na mesma árvore de renderização. Se você precisa renderizar o mesmo conteúdo em vários lugares, passe-o como uma prop.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute seu conjunto de testes de ponta a ponta ou app após a atualização e procure <strong>avisos no console</strong> sobre slots <code>v-model</code> duplicados.</p>
</div>
{% endraw %}

### Estilizando o Atributo `slot` <sup>removido</sup>

Conteúdo inserido através de `<slot>` nomeado não preserva mais o atributo `slot`. Use um elemento wrapper para estilizá-los, ou para casos de uso avançados, modifique o conteúdo inserido programaticamente usando [funções de renderização](render-function.html).

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar seletores CSS mirando slots nomeados (exemplo: <code>[slot="my-slot-name"]</code>).</p>
</div>
{% endraw %}

## Atributos Especiais

### Atributo `keep-alive` <sup>substituído</sup>

`keep-alive` não é mais um atributo especial, mas sim um componente wrapper, semelhante a `<transition>`. Por exemplo:

``` html
<keep-alive>
  <component v-bind:is="view"></component>
</keep-alive>
```

Isso torna possível usar `<keep-alive>` em múltiplos filhos condicionais:

``` html
<keep-alive>
  <todo-list v-if="todos.length > 0"></todo-list>
  <no-todos-gif v-else></no-todos-gif>
</keep-alive>
```

<p class="tip">Quando `<keep-alive>` tem vários filhos, eventualmente somente um será avaliado. Qualquer filho que não seja o primeiro será ignorado.</p>

Quando usado em conjunto com `<transition>`, certifique-se de aninhá-lo dentro:

``` html
<transition>
  <keep-alive>
    <component v-bind:is="view"></component>
  </keep-alive>
</transition>
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar atributos <code>keep-alive</code>.</p>
</div>
{% endraw %}

## Interpolação

### Interpolação dentro de Atributos <sup>removido</sup>

Interpolação com atributos não é mais válido. Por exemplo:

``` html
<button class="btn btn-{{ size }}"></button>
```

Deve ser atualizado para usar uma expressão inline:

``` html
<button v-bind:class="'btn btn-' + size"></button>
```

Ou uma propriedade/dado computado:

``` html
<button v-bind:class="buttonClasses"></button>
```

``` js
computed: {
  buttonClasses: function () {
    return 'btn btn-' + size
  }
}
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de interpolação usada dentro de atributos.</p>
</div>
{% endraw %}

### Interpolação HTML <sup>removido</sup>

Interpolações HTML (`{% raw %}{{{ foo }}}{% endraw %}`)  foram removidas em favor da [diretiva `v-html`](../api/#v-html).

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar interpolações HTML.</p>
</div>
{% endraw %}

### Vinculações de Vez Única <sup>substituído</sup>

Vinculações de vez única (`{% raw %}{{* foo }}{% endraw %}`) foram substituídas pela nova [diretiva `v-once`](../api/#v-once).

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar vinculações de vez única.</p>
</div>
{% endraw %}

## Reatividade

### `vm.$watch` <sup>modificado</sup>

Observadores criados via `vm.$watch` agora são disparados antes que os componentes associados re-renderizem. Isso lhe dá a chance de atualizar o estado antes do componente re-renderizar, evitando atualizações desnecessárias. Por exemplo, você pode assistir um prop de componente e atualizar os próprios dados do componente quando o prop mudar.

Se você anteriormente confiava no `vm.$watch` para fazer algo com o DOM após as atualizações de um componente, você pode fazê-lo no gatilho `updated` do ciclo de vida.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute seu conjunto de testes de ponta a ponta, se você tem um. Os <strong>testes com falha</strong> alertarão para o fato de que um observador está confiando no comportamento antigo.</p>
</div>
{% endraw %}

### `vm.$set` <sup>modificado</sup>

`vm.$set` agora é um apelido para [`Vue.set`](../api/#Vue-set).

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos do uso obsoleto.</p>
</div>
{% endraw %}

### `vm.$delete` <sup>modificado</sup>

`vm.$delete` agora é um apelido para [`Vue.delete`](../api/#Vue-delete).

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos do uso obsoleto.</p>
</div>
{% endraw %}

### `Array.prototype.$set` <sup>removido</sup>

Use `Vue.set` ao invés disso. 

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>.$set</code> em um array. Se você perder algum, poderá ver os <strong>erros no console</strong> sobre o método ausente.</p>
</div>
{% endraw %}

### `Array.prototype.$remove` <sup>removido</sup>

Use `Array.prototype.splice` ao invés disso. Por exemplo:

``` js
methods: {
  removeTodo: function (todo) {
    var index = this.todos.indexOf(todo)
    this.todos.splice(index, 1)
  }
}
```

Ou melhor ainda, passe aos métodos de remoção um index:

``` js
methods: {
  removeTodo: function (index) {
    this.todos.splice(index, 1)
  }
}
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>.$remove</code> em um array. Se você perder algum, poderá ver <strong>erros no console</strong> sobre o método ausente.</p>
</div>
{% endraw %}

### `Vue.set` e `Vue.delete` em Instâncias Vue <sup>removido</sup>

`Vue.set` e `Vue.delete` não funcionam mais em instâncias Vue. Agora é obrigatório declarar corretamente todas as propriedades reativas de nível superior na opção data. Se você quiser excluir propriedades em uma instância do Vue ou seu `$data`, defina-a como null.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>Vue.set</code> ou <code>Vue.delete</code> em uma instância Vue. Se você perder algum, eles dispararão <strong>erros no console</strong>.</p>
</div>
{% endraw %}

### Substituíndo `vm.$data` <sup>removido</sup>

Agora é proibido substituir uma instância raiz `$data` de um componente. Isso evita alguns casos de borda no sistema de reatividade e torna o estado do componente mais previsível (especialmente com sistemas de verificação de tipos).

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de sobrescrita do <code>vm.$data</code>. Se você perder algum, <strong>erros no console</strong> serão emitidos.</p>
</div>
{% endraw %}

### `vm.$get` <sup>removido</sup>

Em vez disso, recupere dados reativos diretamente.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>vm.$get</code>. Se você perder algum, verá <strong>erros no console</strong>.</p>
</div>
{% endraw %}

## Métodos de Instância Focados no DOM

### `vm.$appendTo` <sup>removido</sup>

Use a API nativa do DOM:

{% codeblock lang:js %}
myElement.appendChild(vm.$el)
{% endcodeblock %}

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>vm.$appendTo</code>. Se você perder algum, verá <strong>erros no console</strong>.</p>
</div>
{% endraw %}

### `vm.$before` <sup>removido</sup>

Use a API nativa do DOM:

{% codeblock lang:js %}
myElement.parentNode.insertBefore(vm.$el, myElement)
{% endcodeblock %}

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>vm.$before</code>. Se você perder algum, verá <strong>erros no console</strong>.</p>
</div>
{% endraw %}

### `vm.$after` <sup>removido</sup>

Use a API nativa do DOM:

{% codeblock lang:js %}
myElement.parentNode.insertBefore(vm.$el, myElement.nextSibling)
{% endcodeblock %}


Ou se `myElement` for o último filho:

{% codeblock lang:js %}
myElement.parentNode.appendChild(vm.$el)
{% endcodeblock %}

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>vm.$after</code>. Se você perder algum, verá <strong>erros no console</strong>.</p>
</div>
{% endraw %}

### `vm.$remove` <sup>removido</sup>

Use a API nativa do DOM:

{% codeblock lang:js %}
vm.$el.remove()
{% endcodeblock %}

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>vm.$remove</code>. Se você perder algum, verá <strong>erros no console</strong>.</p>
</div>
{% endraw %}

## Métodos de Meta-Instância

### `vm.$eval` <sup>removido</sup>

Nenhum uso real. Se acontecer de você contar com esse recurso de alguma forma e não sabe como contornar a falta dele, publique no [fórum](https://forum.vuejs.org/) para obter ideias.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>vm.$eval</code>. Se você perder algum, verá <strong>erros no console</strong>.</p>
</div>
{% endraw %}

### `vm.$interpolate` <sup>removido</sup>

Nenhum uso real. Se acontecer de você contar com esse recurso de alguma forma e não sabe como contornar a falta dele, publique no [fórum](https://forum.vuejs.org/) para obter ideias.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>vm.$interpolate</code>. Se você perder algum verá <strong>erros no console</strong>.</p>
</div>
{% endraw %}

### `vm.$log` <sup>removido</sup>

Ao invés disso use o [Vue Devtools](https://github.com/vuejs/vue-devtools) para uma experiência de depuração otimizada.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>vm.$log</code>. Se você perder algum, verá <strong>erros no console</strong>.</p>
</div>
{% endraw %}

## Opções da Instância DOM

### `replace: false` <sup>removido</sup>

Agora os componentes substituem sempre o elemento ao qual estão vinculados. Para simular o comportamento de `replace: false`, você pode envolver seu componente raiz com um elemento semelhante ao que você está substituíndo. Por exemplo:

``` js
new Vue({
  el: '#app',
  template: '<div id="app"> ... </div>'
})
```

Ou com uma função de renderização:

``` js
new Vue({
  el: '#app',
  render: function (h) {
    h('div', {
      attrs: {
        id: 'app',
      }
    }, /* ... */)
  }
})
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>replace: false</code>.</p>
</div>
{% endraw %}

## Configuração Global

### `Vue.config.debug` <sup>removido</sup>

Não é mais necessário, já que os avisos vêm com rastreamentos de pilha por padrão agora.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>Vue.config.debug</code>.</p>
</div>
{% endraw %}

### `Vue.config.async` <sup>removido</sup>

Funcionamento assíncrono agora já é necessário para a performance de renderização.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>Vue.config.async</code>.</p>
</div>
{% endraw %}

### `Vue.config.delimiters` <sup>substituído</sup>

Isso foi retrabalhado como uma [opção no nível de componente](../api/#delimiters). Isso permite que você use delimitadores alternativos dentro da sua aplicação sem quebrar componentes de terceiros.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>Vue.config.delimiters</code>.</p>
</div>
{% endraw %}

### `Vue.config.unsafeDelimiters` <sup>removido</sup>

Interpolação do HTML já foi [removida por causa do `v-html`](#HTML-Interpolation-removed).

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>Vue.config.unsafeDelimiters</code>. Depois disso, o assistente também encontrará instâncias de interpolação HTML para que você possa substituí-las por <code>v-html</code>.</p>
</div>
{% endraw %}

## API Global

### `Vue.extend` com `el` <sup>removido</sup>

A opção `el` não pode mais ser usada no `Vue.extend`. Só é válida como uma opção na criação de instância.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute seu conjunto de testes de ponta a ponta ou app após a atualização e procure <strong>avisos no console</strong> sobre a opção <code>el</code> no <code>Vue.extend</code>.</p>
</div>
{% endraw %}

### `Vue.elementDirective` <sup>removido</sup>

Use componentes ao invés disso.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>Vue.elementDirective</code>.</p>
</div>
{% endraw %}

### `Vue.partial` <sup>removido</sup>

Parciais foram removidos em favor de fluxo de dados mais explícito entre componentes, usando props. A menos que você esteja usando um `partial` em uma área crítica para desempenho, a recomendação é usar um [componente normal](components.html) em vez disso. Se você estiver vinculando dinamicamente o `name` de um `partial`, poderá usar um [componente dinâmico](components.html#Dynamic-Components).

Se você estiver usando `partial` em uma parte crítica para o desempenho do seu aplicativo, então você deve atualizar para [componentes funcionais](render-function.html#Functional-Components). Eles devem estar em um arquivo JS/JSX simples (em vez de um arquivo `.vue`) e são sem estados ou instâncias, como `partial`. Isso torna a renderização extremamente rápida.

Um benefício de componentes funcionais sobre parciais é que eles podem ser muito mais dinâmicos, porque eles lhe permitem acessar todo o poder do JavaScript. No entanto, há um custo para este poder. Se você nunca usou um framework de componentes com funções de renderização antes, elas podem demorar um pouco mais para se aprender.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de <code>Vue.partial</code>.</p>
</div>
{% endraw %}
