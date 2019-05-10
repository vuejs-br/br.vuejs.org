---
title: Migração do Vue 1.x
type: guide
order: 701
---

## FAQ

> Wow - essa página é super longa! Isso significa que a versão 2.0 é completamente diferente, eu terei que aprender o básico de novo e a migração será praticamente impossivel?

Fico feliz que você tenha perguntado! A resposta é não. Cerca de 90% da API é o mesmo e os conceitos básicos não mudaram. É longo porque queremos oferecer explicações muito detalhadas e incluir muitos exemplos. Tenha certeza, __isso não é algo que você tem que ler de cima para baixo!__

> Por onde devo começar em uma migração?

1. Comece executando o [assistente de migração](https://github.com/vuejs/vue-migration-helper)  em um projeto atual. Nós cuidadosamente minificamos e comprimimos um desenvolvedor sênior do Vue em uma interface de linha de comando simples. Sempre que eles reconhecem um recurso obsoleto, eles vão deixar você saber, oferecer sugestões e fornecer links para mais informações.

2. Depois disso, navegue pela tabela de conteúdos desta página na barra lateral. Se você ver um tópico pelo o qual você pode ser afetado, mas o ajudante de migração não pegou, confira.

3. Se você tiver algum teste, execute-os e veja o que ainda falha. Se você não tiver testes, basta abrir o app em seu navegador e ficar atento para avisos ou erros ao navegar.

4. Por Enquanto, seu app deve ter migrado totalmente. Se você ainda deseja mais, pode ler o resto desta página - ou mergulhar no novo e melhorado [guia de início](index.html). Muitas partes serão desiguais, já que você já está familiarizado com os conceitos básicos.

> Quanto demorará para migrar um app Vue da versão 1.x para a 2.0?

Depende de alguns fatores:

- O tamanho do seu app (app pequeno ou médio provavelmente será inferior a um dia)

- Quantas vezes você se distrai e começa a brincar com um recurso novo e legal. 😉 &nbsp; Não julgando, também aconteceu conosco enquanto construíamos a versão 2.0!

- Quais recursos obsoletos você está usando. A maioria pode ser atualizada com encontrar-e-substituir, mas outros podem demorar alguns minutos. Se você não estiver seguindo as práticas recomendadas, o Vue 2.0 também tentará forçar você a fazê-lo. Isso é bom no longo prazo, mas também pode significar um refatoramento significativo (embora possivelmente em atraso).

> Se eu atualizar para o Vue 2, também terei que atualizar o Vuex e o Vue Router?

Somente o Vue Router 2 é compatível com o Vue 2, então sim, você também terá que seguir o [caminho de migração do Vue Router](migration-vue-router.html). Felizmente, a maioria dos apps não tem muito código de roteador, então provavelmente não demorará mais de uma hora.

Quanto ao Vuex, até a versão 0.8 é compatível com o Vue 2, portanto, você não está obrigado a atualizar. A única razão pela qual você pode querer atualizar imediatamente é aproveitar os novos recursos do Vuex 2, como módulos e boilerplate reduzido.

## Templates

### Instâncias de fragmento <sup>removido</sup>

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
  <p>Execute seu conjunto de testes de ponta a ponta ou app após a atualização e procure <strong>avisos do console</strong> sobre vários elementos raiz em um template.</p>
</div>
{% endraw %}

## Gatilhos de Ciclo de Vida

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

Use o novo gatilho `beforeCreate` em vez disso, o que é essencialmente o mesmo. Foi renomeado para consistência com outros métodos do ciclo de vida.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar todos os exemplos desse gatilho.</p>
</div>
{% endraw %}

### `ready` <sup>Substituído</sup>

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
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de ordem de argumentos obsoleta. Observe que, se você nomear seus argumentos de índice algo incomum como <code>position</code> ou <code>num</code>, o ajudante não irá sinalizá-los.</p>
</div>
{% endraw %}

### Ordem dos Argumentos para Objects no `v-for` <sup>mudado</sup>

Ao incluir uma `chave`, a ordem dos argumentos para objetos costumava ser `(chave, valor)`. Agora é `(valor, chave)` para ser mais consistente com os iteradores de objetos comuns, como o `lodash`.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de ordem de argumentos obsoleta. Observe que, se você nomear seus argumentos de chave como <code>name</code> ou <code>property</code>, o ajudante não os sinalizará.</p>
</div>
{% endraw %}

### `$index` e `$key` <sup>removidos</sup>

As variáveis `$index` e `$key` implicitamente atribuídas foram removidas em favor de definí-las explicitamente no `v-for`. Isso torna o código mais fácil de ler para desenvolvedores menos experientes com Vue e também resulta em um comportamento muito mais claro ao lidar com loops aninhados.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos dessas variáveis removidas. Se você perder alguma, você também deve ver <strong>erros no console</strong>, como: <code>Uncaught ReferenceError: $index is not defined</code></p>
</div>
{% endraw %}

### `track-by` <sup>Substituído</sup>

`track-by` foi substituído por uma `key`, que funciona como qualquer outro atributo: sem o `v-bind:` ou o prefixo  `:`, é tratado como uma string literal. Na maioria dos casos, você desejará usar uma vinculação dinâmica que espera uma expressão completa ao invés de uma chave. Por exemplo, no lugar de:

``` html
<div v-for="item in items" track-by="id">
```

Agora você irá escrever:

``` html
<div v-for="item in items" v-bind:key="item.id">
```

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

Mutar um prop local agora é considerado anti-padrão, e.g. declarando um suporte e em seguida, definindo `this.myProp = 'someOtherValue'` no componente. Devido ao novo mecanismo de renderização, sempre que o componente pai voltar a renderizar, as alterações locais do componente filho serão substituídas.

A maioria dos casos de mutar um prop pode ser substituído por uma dessas opções:

- uma propriedade data, com o prop usado para definir seu valor padrão
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

## Diretivas Built-In

### Verdadeiro/Falso com `v-bind` <sup>modificado</sup>

Quando usado com `v-bind`, os únicos valores falsos são agora: `null`, `undefined` e `false`. Isso significa que `0` e arrays vazios renderizarão como verdadeiro. Então, por exemplo, `v-bind: draggable = "''"` renderizará como `draggable = "true"`.

Para atributos enumerados, além dos valores falsos acima, a string `"false"` também será renderizada como `attr = "false"`.

<p class="tip">Observe que para outras diretivas (v-if e v-show por exemplo), o verdadeiro do Javascript normal ainda se aplica.</p>

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute seus testes de ponta a ponta, se você tiver um. Os <strong>testes com falha</strong> devem te alertar para qualquer parte do seu app que possa ser afetada por essa alteração.</p>
</div>
{% endraw %}

### Escutando Eventos Nativos nos Componentes com `v-on` <sup>modificado</sup>

Quando usando em um componente, `v-on` agora só escuta eventos customizados `$emit`ido por aquele componente. Para escutar um evento nativo do DOM no elemento raiz, você pode usar o modificador `.native`. Por exemplo:

``` html
<my-component v-on:click.native="doSomething"></my-component>
```

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

Outra vantagem desta abordagem é que haverá momentos em que o `debounce` não é a função wrapper correta. Por exemplo, ao usar uma API para sugestões de pesquisa, esperar para oferecer sugestões até que o usuário pare de digitar por um período de tempo não é uma experiência ideal. O que você provavelmente quer, em vez disso, é uma função de __limitação__. Agora, já que você já está usando uma biblioteca de utilidades como lodash, a refatoração para usar a função `throttle`, leva apenas alguns segundos.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos do atributo <code>debounce</code>.</p>
</div>
{% endraw %}

### Atributos `lazy` ou `number` de Paramêtros para `v-model` <sup>substituído</sup>

Os atributos `lazy` e `number` são agora modificadores, para tornar mais claro o que isso significa, ao invés de:

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

``` html
<input v-for="obj in objects" v-model="obj.str">
```

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

Para simplificar, `v-el` e `v-ref` foram incorporados no atributo `ref`, acessível em uma instância de componente por meio de `$refs`. Isso significa que `v-el:my-element` se tornaria `ref = "myElement"` e `v-ref:my-component` se tornaria `ref = "myComponent"`. Quando usado em um elemento normal, o `ref` será o elemento DOM, e quando usado em um componente, a `ref` será a instância do componente.

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
Fortunately, since the new directives are much simpler, you can master them more easily. Read the new [Custom Directives guide](custom-directive.html) to learn more.

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos de diretivas definidas. O assistente sinalizará todos eles, é provável que na maioria dos casos você desejará refatorar para um componente.</p>
</div>
{% endraw %}

### Modificador de Directiva `.literal` <sup>removido</sup>

O modificador `literal` foi removido pois o mesmo pode ser facilmente alcançado fornecendo uma string literal como valor.

Por exemplo, você pode atualizar:

``` js
<p v-my-directive.literal="foo bar baz"></p>
```

para:

``` html
<p v-my-directive="'foo bar baz'"></p>
```

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos do modificador `.literal` em diretivas.</p>
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

Se você precisa escalonar as transições da lista, você pode controlar o tempo ajustando e acessando um `data-index` (ou atributo similar) em um elemento. Veja [um exemplo aqui](transitions.html#Staggering-List-Transitions).

{% raw %}
<div class="upgrade-path">
  <h4>Caminho de atualização</h4>
  <p>Execute o <a href="https://github.com/vuejs/vue-migration-helper">assistente de migração</a> em sua base de código para encontrar exemplos do atributo <code>transition</code>. Durante sua atualização, você pode fazer a transição (trocadilho muito intencional) para a nova estratégia de escalonação também</p>
</div>
{% endraw %}

## Eventos

### Opção `events` <sup>removido</sup>

A opção `events` foi removida. Agora os manipuladores de eventos devem ser registrados no gatilho `created` ao invés disso. Veja o [guia de migração para `$dispatch` e `$broadcast`](#dispatch-and-broadcast-replaced) para exemplo detalhado.

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

`$dispatch` e `$broadcast` foram removidos em favor de uma comunicação mais explícita através componentes e soluções de gerenciamento de estado mais sustentáveis, como o [Vuex](https://github.com/vuejs/vuex).

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

Podemos gerenciar a comunicação entre componentes com este único _hub_ de evento:

``` js
// Este é o hub de evento que usaremos em cada
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

### Substituindo o filtro `debounce`
Ao invés de:
```
<input v-on:keyup="doStuff | debounce 500">

methods: {
  doStuff: function () {
    // ...
  }
}
```

Use o [lodash debounce](https://lodash.com/docs/4.17.5#debounce) ou possivelmente o [throttle](https://lodash.com/docs/4.17.5#throttle) para limitar diretamente a chamada do método. Você pode alcançar o mesmo que acima, como isso:

```<input v-on:keyup="doStuff">
methods: {
  doStuff: _.debounce(function () {
    // ...
  }, 500)
}
```

Para obter mais informações sobre as vantagens desta estratégia, veja o exemplo [aqui](https://github.com/vuejs-br/br.vuejs.org/blob/master/src/v2/guide/migration.md#debounce-Param-Attribute-for-v-model-removed) com `v-model`.

### Substituindo o filtro `limitBy`
Ao invés de: 
`<p v-for="item in items | limitBy 10">{{ item }}</p>`

Use o metodo `slice` do javascript em uma propriedade computada
```
<p v-for="item in filteredItems">{{ item }}</p>
computed: {
  filteredItems: function () {
    return this.items.slice(0, 10)
  }
}
```


### Substituindo o filtro `filterBy`
Ao invés de:
`<p v-for="user in users | filterBy searchQuery in 'name'">{{ user.name }}</p>`

Use o método `filter` do Javascript numa propriedade computada:
```
computed: {
  filteredUsers: function () {
    var self = this
    return self.users.filter(function (user) {
      return user.name.indexOf(self.searchQuery) !== -1
    })
  }
}
```

O Javascript nativo `filter` também pode gerenciar operações de filtragem muito mais complexas, porque você tem acesso ao poder total do JavaScript dentro das propriedades calculadas. Por exemplo, se você quisesse encontrar todos os usuários ativos e combinar caso-insensivelmente contra o nome e o email:
```
var self = this
self.users.filter(function (user) {
  var searchRegex = new RegExp(self.searchQuery, 'i')
  return user.isActive && (
    searchRegex.test(user.name) ||
    searchRegex.test(user.email)
  )
})
```

**Substituindo o filtro `orderBy`**
Ao invés de:
`<p v-for="user in users | orderBy 'name'">{{ user.name }}</p>`

Use o [lodash orderBy](https://lodash.com/docs/4.17.5#orderBy) ou possivelmente o [sortBy](https://lodash.com/docs/4.17.5#sortBy) em uma propriedade computada
`<p v-for="user in orderedUsers">{{ user.name }}</p>`

```
computed: {
  orderedUsers: function () {
    return _.orderBy(this.users, 'name')
  }
}
```

Você pode também ordenar por multiplas colunas:
`_.orderBy(this.users, ['name', 'last_login'], ['asc', 'desc'])`

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) em sua base de código para encontrar exemplos dos filtros sendo usados dentro das diretivas. Se você perder algum, você também deve ver os **erros no console**

## Sintaxe do argumento filter <sup>modificado</sup>
A sintaxe para argumentos agora se alinha melhor com a invocação de função do Javascript. Então ao invés de delimitar o espaço dos argumentos:
`<p>{{ date | formatDate 'YY-MM-DD' timeZone }}</p>` 

Nós cercamos os argumentos com parênteses e delimitamos os argumentos com vírgulas:
`<p>{{ date | formatDate('YY-MM-DD', timeZone) }}</p>`

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) em sua base de código para encontrar exemplos. Se você perder algum, você também deve ver os **erros no console**

## FIltros de texto Built-In <sup>Removido</sup>
Embora os filtros nas interpolações de texto ainda estejam permitidos, todos os filtros foram removidos. Em vez disso, é recomendável usar bibliotecas mais especializadas para resolver problemas em cada domínio. [date-fns](https://date-fns.org) para formatar datas e [moedas](http://openexchangerates.github.io/accounting.js/) para moedas.

Para cada um dos filtros de texto internos do Vue, passamos por como você pode substituí-los abaixo. O código de exemplo pode existir em funções, métodos ou propriedades calculadas personalizadas.

### Substituindo o filtro de `json`
Na verdade, você não precisa mais debugar, já que o Vue irá criar um formato de saída para você automaticamente, seja uma string, número, array ou objeto comum. Se você quiser exatamente a mesma funcionalidade que o `JSON.stringify` do JavaScript, porém, você pode usar isso em um método ou propriedade computada.

### Substituindo o filtro `capitalize`
`text[0].toUpperCase() + text.slice(1)`

### Substituindo o filtro `uppercase`
`text.toUpperCase()`

### Substituindo o filtro `lowercase`
`text.toLowerCase()`

### Substituindo o filtro `pluralize`
O pacote pluralize no NPM atende bem este propósito, mas se você quer apenas pluralizar uma palavra específica ou quer ter uma saída especial para casos como 0, então você também pode definir facilmente suas próprias funções de `pluralize`

```
function pluralizeKnife (count) {
  if (count === 0) {
    return 'no knives'
  } else if (count === 1) {
    return '1 knife'
  } else {
    return count + 'knives'
  }
}
```

### Substituindo o filtro `currency`
Para uma implementação muito simples, você poderia fazer algo como isto:
`'$' + price.toFixed(2)`


Em muitos casos, porém, você ainda terá um comportamento estranho ( `0.035` toFixed(2) arredonda até 0,04, mas 0,045 arredondamentos até 0,04).

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) em sua base de código para encontrar exemplos de filtros de texto obsoletos. Se você perder algum, você também deve ver os **erros no console**

## Filtros bidirecionais  <sup>Substituido</sup>
Alguns usuários gostaram de usar filtros bidirecionais com `v-model` para criar entradas interessantes com muito pouco código. Embora aparentemente simples no entanto, os filtros bidirecionais também podem ocultar uma grande complexidade - e até mesmo encorajar uma UX ruim ao atrasar as atualizações do estado. Em vez disso, os componentes que envolvem uma entrada são recomendados como uma maneira mais explícita e rica em recursos de criar entradas personalizadas.

Como exemplo, agora vamos andar a migração de um filtro de moeda de dois sentidos:
`<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/6744xnjk/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>`

A maioria funciona bem, mas as atualizações de estado atrasadas podem causar um comportamento estranho. Por exemplo, clique na guia `Result`e tente entrar 9.999 em uma dessas entradas. Quando a entrada perde o foco, seu valor será atualizado para US $10,00. Ao olhar para o total calculado no entanto, você verá que 9.999 é o que está armazenado em nossos dados. A versão da realidade que o usuário vê está fora de sincronia!

Para iniciar a transição para uma solução mais robusta usando o Vue 2.0, primeiro envolva esse filtro em um novo componente `<currency-input>`:
`<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/943zfbsh/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>`

Isso nos permite adicionar um comportamento que um filtro por si só não poderia encapsular, como selecionar o conteúdo de uma entrada no foco. Agora, o próximo passo será extrair a lógica de negócios do filtro. Abaixo, puxamos tudo para um [objeto](https://gist.github.com/chrisvfritz/5f0a639590d6e648933416f90ba7ae4e) de `currencyValidator` de moeda externa:
`<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/943zfbsh/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>`

Essa aumento da modularidade não só facilita a migração para a Vue 2, mas também permite que a análise e a formatação da moeda sejam:

Testes unitarios isolados do seu código Vue
Usado por outras partes do seu app, como para validar a carga útil para um ponto final da API

Tendo este validador extraído, nós também construímos mais confortavelmente isso em uma solução mais robusta. As peculiaridades do estado foram eliminadas e, na verdade, é impossível para os usuários inserirem algo errado, semelhante ao que a entrada do número nativo do navegador tenta fazer.

Ainda estamos limitados, porém, por filtros e pelo Vue 1.0 em geral, então vamos completar a atualização para o Vue 2.0:
`<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/1oqjojjx/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>`

Você pode notar que:
Todos os aspectos da nossa entrada são mais explícitos, usando `ciclo de vida dos gatilhos` e eventos DOM em lugar do comportamento oculto dos filtros de dois sentidos

Agora podemos usar o `v-model` diretamente em nossas entradas personalizadas, o que não é apenas mais consistente com as entradas normais, mas também significa que nosso componente é amigável ao Vuex.

Uma vez que não estamos mais usando opções de filtro que exigem um valor a ser retornado, nosso trabalho de moeda pode realmente ser feito de forma assíncrona. Isso significa que se tivéssemos muitos apps que tivessem que trabalhar com moedas, poderíamos facilmente refatorar essa lógica em um microservice compartilhado.

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) em sua base de código para encontrar exemplos de filtros usados em diretivas como o `v-model`. Se você perder algum, você também deve ver os **erros no console**

# Slots

## Slots duplicados <sup>removido</sup>
Não é mais suportado ter `<slot>` s com o mesmo nome no mesmo modelo. Quando um slot é processado, ele é "usado" e não pode ser processado em outro lugar na mesma árvore de renderização. Se você deve renderizar o mesmo conteúdo em vários lugares, passe esse conteúdo como suporte.

Execute seu conjunto de testes de ponta a ponta após a atualização e procure **erros no console** sobre o `v-model` e slots duplicados.

## Atributo `Syiling de slot`<sup>Removido</sup>
O conteúdo inserido através do nome `<slot>` não preserva mais o atributo do slot. Use um elemento de wrapper para modelá-los, ou para casos de uso avançado, modifique o conteúdo inserido de forma programática usando funções de renderização.

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) para encontrar seletores CSS que visem slots nomeados.

# Atributos especiais
## Atributo `keep-alive`  <sup>Substituido</sup>
`keep-alive` não é mais um atributo especial, mas sim um componente de wrapper, semelhante a `<transition>`. Por exemplo:
```
<keep-alive>
  <component v-bind:is="view"></component>
</keep-alive>
```

Isso torna possível usar `<keep-alive>` em múltiplos filhos condicionais:
```
<keep-alive>
  <todo-list v-if="todos.length > 0"></todo-list>
  <no-todos-gif v-else></no-todos-gif>
</keep-alive>
```

Quando `` tem vários filhos, eles devem eventualmente avaliar para um único filho. Qualquer filho que não seja o primeiro será ignorada.

Quando usado em conjunto com `<transition>`, certifique-se de aninhá-lo dentro:
```
<transition>
  <keep-alive>
    <component v-bind:is="view"></component>
  </keep-alive>
</transition>
```

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar os atributos `keep-alive`

# Interpolação
## Interpolação com atributos <sup>removido</sup>
Interpolação com atributos não é mais suportado. Por exemplo:
`<button class="btn btn-{{ size }}"></button>`

Deve ser atualizado para usar uma expressão inline:
`<button v-bind:class="'btn btn-' + size"></button>`

Ou a propriedade/dado computado:
`<button v-bind:class="buttonClasses"></button>`

```
computed: {
  buttonClasses: function () {
    return 'btn btn-' + size
  }
}
```

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos de interpolação usado com atributos

## Interpolação HTML <sup>Removido</sup>
Interpolações html {( foo )} foram removidos devido a diretiva [v-html](https://github.com/vuejs-br/br.vuejs.org/tree/master/src/v2/api#v-html)

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos de interpolação HTML

## One-time bindings <sup>Substituido</sup>
Os `one-time`bindings {{* foo }} foram substituidos pela nova diretiva [v-once](https://github.com/vuejs-br/br.vuejs.org/tree/master/src/v2/api#v-once)

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos de binding `one-time`

# Reatividade
## `vm.$watch` <sup>modificado</sup>
Observadores criados via `vm.$watch` agora são acionados antes dos rerenders associados do componente. Isso lhe dá a chance de atualizar o estado antes do rerender componente, evitando atualizações desnecessárias. Por exemplo, você pode assistir a um suporte de componentes e atualizar os próprios dados do componente quando o suporte mudar.

Se você confiava anteriormente no `vm. $ watch` para fazer algo com o DOM após as atualizações de um componente, você pode fazê-lo na atualização do ciclo de vida dos gatilhos.

Execute seu conjunto de teste de ponta a ponta, se você tiver um. Os testes com falha devem alertar para o fato de que um observador estava confiando no comportamento antigo.

## `vm.$set` <sup>modificado</sup>
vm.$set agora é um alias para [Vue.set](https://github.com/vuejs-br/br.vuejs.org/tree/master/src/v2/api#Vue-set).

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos do uso obsoleto

## `vm.$delete` <sup>modificado</sup>
vm.$delete agora é um alias para [Vue.delete](https://github.com/vuejs-br/br.vuejs.org/tree/master/src/v2/api#Vue-delete).

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos do uso obsoleto

## `Array.prototype.$set` <sup>Removido</sup>
Use `Vue.set` ao invés disso. 

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos do `$set` em um array. Se você perder algum, você também deve ver os **erros no console**


## `Array.prototype.$remove`<sup>Removido</sup>
Use `Array.prototype.splice` ao invés. Por exemplo:

```
methods: {
  removeTodo: function (todo) {
    var index = this.todos.indexOf(todo)
    this.todos.splice(index, 1)
  }
}
```

Ou melhor ainda, passe os metodos de remoção do index

```
methods: {
  removeTodo: function (index) {
    this.todos.splice(index, 1)
  }
}
```

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos do `$remove`  em um array. Se você perder algum, você também deve ver os **erros no console**


## `Vue.set` e `Vue.delete` nas instâncias Vue <sup>Removido</sup>
`Vue.set` e `Vue.delete` não pode mais trabalhar nas instâncias Vue. Agora é obrigatório declarar corretamente todas as propriedades reativas de nível superior na opção de dados. Se você quiser excluir propriedades em uma instância do Vue ou seus `$dados`, defina-a como nulo.

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos das instâncias `Vue.set` e `Vue.delete`. Se você perder algum, você também deve ver os **erros no console**

## Substituindo `vm.$data` <sup>Removido</sup>
Agora é proibido substituir a raiz `$ data` de uma instância de componente. Isso evita alguns casos de borda no sistema de reatividade e torna o estado do componente mais previsível (especialmente com os sistemas de verificação de tipos).

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos da sobrescritura do `vm.$data`. Se você perder algum, você também deve ver os **erros no console**

## `vm.$get` <sup>Removido</sup>
Em vez disso, recupere dados reativos diretamente.

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos do `vm.$get`. Se você perder algum, você também deve ver os **erros no console**


# Métodos de instância focalizada do DOM
## `vm.$appendTo` <sup>Removido</sup>

Use a API nativa do DOM
`myElement.appendChild(vm.$el)`

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos da sobrescritura do `vm.$appendTo`. Se você perder algum, você também deve ver os **erros no console**

## `vm$.before` <sup>Removido</sup>
Use a API nativa do DOM
`myElement.parentNode.insertBefore(vm.$el, myElement)`


Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos do `vm.$before`. Se você perder algum, você também deve ver os **erros no console**

## `vm$.after` <sup>Removido</sup>
Use a API nativa do DOM
`myElement.parentNode.insertBefore(vm.$el, myElement.nextSibling)`

Ou se o ultimo filho for o `myElement`:
`myElement.parentNode.appendChild(vm.$el)`

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos do `vm.$after`. Se você perder algum, você também deve ver os **erros no console**


## `vm$.remove` <sup>Removido</sup>
Use a API nativa do DOM
`vm.$el.remove()`

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos do `vm.$remove`. Se você perder algum, você também deve ver os **erros no console**


# Métodos de Meta-Instância
## `vm.$eval`<sup>Removido</sup>

Nenhum uso real. Se você confia nessa característica de alguma forma e não sabe como trabalhar com ela, publique no fórum idéias.

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos do `vm.$eval`. Se você perder algum, você também deve ver os **erros no console**

## `vm.$interpolate` <sup>Removido</sup>

Nenhum uso real. Se você confia nessa característica de alguma forma e não sabe como trabalhar com ela, publique no fórum idéias.

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos do `vm.$interpolate`. Se você perder algum, você também deve ver os **erros no console**


## `vm.$log` <sup>Removido</sup>
Use o [Vue Devtools](https://github.com/vuejs/vue-devtools) para otimizar a experiencia de depuração.


Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos do `vm.$log`. Se você perder algum, você também deve ver os **erros no console**

# Opções de Instancia do DOM
## `replace: false` <sup>Removido</sup>

Os componentes agora substituem sempre o elemento ao qual estão vinculados. Para simular o comportamento de `replace: false`, você pode conter seu componente raiz com um elemento semelhante ao que você está substituindo. Por exemplo:

```
new Vue({
  el: '#app',
  template: '<div id="app"> ... </div>'
})
```

Ou com uma função de renderização:

```
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

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos de `replace: false`


# Configuração global
## `Vue.config.debug` <sup>Removido</sup>

Não é mais necessário, uma vez que os avisos vêm com traços de pilha por padrão agora.

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos de `Vue.config.debug`

## `Vue.config.asyn`<sup>Removido</sup>
O `Async` agora é necessário para renderizar o desempenho.

# Configuração global
## `Vue.config.debug` <sup>Removido</sup>

Não é mais necessário, uma vez que os avisos vêm com traços de pilha por padrão agora.

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos de `Vue.config.async`

## `Vue.config.delimiter`<sup>Removido</sup>

Isso foi retrabalhado como uma [opção de nível de componente](https://github.com/vuejs-br/br.vuejs.org/tree/master/src/v2/api#delimiters). Isso permite que você use delimitadores alternativos dentro da sua aplicação sem quebrar componentes de terceiros.

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos de `Vue.config.delimiter`


## `Vue.config.unsafeDelimiters` <sup>Removido</sup>
Interpolação do HTML foi removido por causa do `v-html`

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos de `Vue.config.unsafeDelimiter`. Depois disso, o auxiliar também encontrará instâncias de interpolação HTML para que você possa substituí-las por `v-html`.


# API global
## `Vue.extend` com `el` <sup>Removido</sup>

A opção `el` não pode mais ser usada no `Vue.extend`. Só é válido como uma opção de criação de instância.

Execute seu conjunto de testes de ponta a ponta ou app após a atualização e procure avisos de erro no console sobro a opção `el` com `Vue.extend`

## `Vue.elementDirective` <sup>Removido</sup>
Use componentes ao invés disso.

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos de `Vue.elementDirective`


## `Vue.partial` <sup>Removido</sup>

Parciais foram removidos em favor de fluxo de dados mais explícito entre componentes, usando adereços. A menos que você esteja usando um `partial` em uma área de desempenho crítico, a recomendação é usar um [componente normal](https://github.com/vuejs-br/br.vuejs.org/blob/master/src/v2/guide/components.html) em vez disso. Se você estivesse vinculando dinamicamente o nome de um `partial`, você pode usar um [componente dinâmico] (https://github.com/vuejs-br/br.vuejs.org/blob/master/src/v2/guide/components.html#Dynamic-Components).

Se você estiver usando `partial` em uma parte crítica do desempenho do seu aplicativo, então você deve atualizar para [componentes funcionais](https://github.com/vuejs-br/br.vuejs.org/blob/master/src/v2/guide/render-function.html#Functional-Components). Eles devem estar em um arquivo JS / JSX simples (em vez de em um arquivo `.vue`) e são sem estados e sem instâncias, como `partial`. Isso torna a renderização extremamente rápida.

Um benefício de componentes funcionais sobre parciais é que eles podem ser muito mais dinâmicos, porque eles lhe permitem acessar todo o poder do JavaScript. No entanto, há um custo para este poder. Se você nunca usou uma estrutura de componentes com funções de renderização antes, elas podem demorar um pouco mais para aprender.

Execute o [assistente de migração](https://github.com/vuejs/vue-migration-helper) na sua base de código para encontrar exemplos de `Vue.partial`
