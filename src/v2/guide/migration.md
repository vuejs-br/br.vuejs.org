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

#### Replacing the `debounce` Filter

Instead of:

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

Use [lodash's `debounce`](https://lodash.com/docs/4.15.0#debounce) (or possibly [`throttle`](https://lodash.com/docs/4.15.0#throttle)) to directly limit calling the expensive method. You can achieve the same as above like this:

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

For more on the advantages of this strategy, see [the example here with `v-model`](#debounce-Param-Attribute-for-v-model-removed).

#### Replacing the `limitBy` Filter

Instead of:

``` html
<p v-for="item in items | limitBy 10">{{ item }}</p>
```

Use JavaScript's built-in [`.slice` method](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Array/slice#Examples) in a computed property:

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

#### Replacing the `filterBy` Filter

Instead of:

``` html
<p v-for="user in users | filterBy searchQuery in 'name'">{{ user.name }}</p>
```

Use JavaScript's built-in [`.filter` method](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Array/filter#Examples) in a computed property:

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

JavaScript's native `.filter` can also manage much more complex filtering operations, because you have access to the full power of JavaScript within computed properties. For example, if you wanted to find all active users and case-insensitively match against both their name and email:

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

#### Replacing the `orderBy` Filter

Instead of:

``` html
<p v-for="user in users | orderBy 'name'">{{ user.name }}</p>
```

Use [lodash's `orderBy`](https://lodash.com/docs/4.15.0#orderBy) (or possibly [`sortBy`](https://lodash.com/docs/4.15.0#sortBy)) in a computed property:

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

You can even order by multiple columns:

``` js
_.orderBy(this.users, ['name', 'last_login'], ['asc', 'desc'])
```

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of filters being used inside directives. If you miss any, you should also see <strong>console errors</strong>.</p>
</div>
{% endraw %}

### Filter Argument Syntax <sup>changed</sup>

Filters' syntax for arguments now better aligns with JavaScript function invocation. So instead of taking space-delimited arguments:

``` html
<p>{{ date | formatDate 'YY-MM-DD' timeZone }}</p>
```

We surround the arguments with parentheses and delimit the arguments with commas:

``` html
<p>{{ date | formatDate('YY-MM-DD', timeZone) }}</p>
```

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the old filter syntax. If you miss any, you should also see <strong>console errors</strong>.</p>
</div>
{% endraw %}

### Built-In Text Filters <sup>removed</sup>

Although filters within text interpolations are still allowed, all of the filters have been removed. Instead, it's recommended to use more specialized libraries for solving problems in each domain (e.g. [`date-fns`](https://date-fns.org/) to format dates and [`accounting`](http://openexchangerates.github.io/accounting.js/) for currencies).

For each of Vue's built-in text filters, we go through how you can replace them below. The example code could exist in custom helper functions, methods, or computed properties.

#### Replacing the `json` Filter

You actually don't need to for debugging anymore, as Vue will nicely format output for you automatically, whether it's a string, number, array, or plain object. If you want the exact same functionality as JavaScript's `JSON.stringify` though, then you can use that in a method or computed property.

#### Replacing the `capitalize` Filter

``` js
text[0].toUpperCase() + text.slice(1)
```

#### Replacing the `uppercase` Filter

``` js
text.toUpperCase()
```

#### Replacing the `lowercase` Filter

``` js
text.toLowerCase()
```

#### Replacing the `pluralize` Filter

The [pluralize](https://www.npmjs.com/package/pluralize) package on NPM serves this purpose nicely, but if you only want to pluralize a specific word or want to have special output for cases like `0`, then you can also easily define your own pluralize functions. For example:

``` js
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

#### Replacing the `currency` Filter

For a very naive implementation, you could do something like this:

``` js
'$' + price.toFixed(2)
```

In many cases though, you'll still run into strange behavior (e.g. `0.035.toFixed(2)` rounds up to `0.04`, but `0.045` rounds down to `0.04`). To work around these issues, you can use the [`accounting`](http://openexchangerates.github.io/accounting.js/) library to more reliably format currencies.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the obsolete text filters. If you miss any, you should also see <strong>console errors</strong>.</p>
</div>
{% endraw %}

### Two-Way Filters <sup>replaced</sup>

Some users have enjoyed using two-way filters with `v-model` to create interesting inputs with very little code. While _seemingly_ simple however, two-way filters can also hide a great deal of complexity - and even encourage poor UX by delaying state updates. Instead, components wrapping an input are recommended as a more explicit and feature-rich way of creating custom inputs.

As an example, we'll now walk the migration of a two-way currency filter:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/6744xnjk/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

It mostly works well, but the delayed state updates can cause strange behavior. For example, click on the `Result` tab and try entering `9.999` into one of those inputs. When the input loses focus, its value will update to `$10.00`. When looking at the calculated total however, you'll see that `9.999` is what's stored in our data. The version of reality that the user sees is out of sync!

To start transitioning towards a more robust solution using Vue 2.0, let's first wrap this filter in a new `<currency-input>` component:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/943zfbsh/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

This allows us add behavior that a filter alone couldn't encapsulate, such as selecting the content of an input on focus. Now the next step will be to extract the business logic from the filter. Below, we pull everything out into an external [`currencyValidator` object](https://gist.github.com/chrisvfritz/5f0a639590d6e648933416f90ba7ae4e):

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/9c32kev2/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

This increased modularity not only makes it easier to migrate to Vue 2, but also allows currency parsing and formatting to be:

- unit tested in isolation from your Vue code
- used by other parts of your application, such as to validate the payload to an API endpoint

Having this validator extracted out, we've also more comfortably built it up into a more robust solution. The state quirks have been eliminated and it's actually impossible for users to enter anything wrong, similar to what the browser's native number input tries to do.

We're still limited however, by filters and by Vue 1.0 in general, so let's complete the upgrade to Vue 2.0:

<iframe width="100%" height="300" src="https://jsfiddle.net/chrisvfritz/1oqjojjx/embedded/js,html,result" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

You may notice that:

- Every aspect of our input is more explicit, using lifecycle hooks and DOM events in place of the hidden behavior of two-way filters.
- We can now use `v-model` directly on our custom inputs, which is not only more consistent with normal inputs, but also means our component is Vuex-friendly.
- Since we're no longer using filter options that require a value to be returned, our currency work could actually be done asynchronously. That means if we had a lot of apps that had to work with currencies, we could easily refactor this logic into a shared microservice.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of filters used in directives like <code>v-model</code>. If you miss any, you should also see <strong>console errors</strong>.</p>
</div>
{% endraw %}

## Slots

### Duplicate Slots <sup>removed</sup>

It is no longer supported to have `<slot>`s with the same name in the same template. When a slot is rendered it is "used up" and cannot be rendered elsewhere in the same render tree. If you must render the same content in multiple places, pass that content as a prop.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run your end-to-end test suite or app after upgrading and look for <strong>console warnings</strong> about duplicate slots <code>v-model</code>.</p>
</div>
{% endraw %}

### `slot` Attribute Styling <sup>removed</sup>

Content inserted via named `<slot>` no longer preserves the `slot` attribute. Use a wrapper element to style them, or for advanced use cases, modify the inserted content programmatically using [render functions](render-function.html).

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find CSS selectors targeting named slots (e.g. <code>[slot="my-slot-name"]</code>).</p>
</div>
{% endraw %}

## Special Attributes

### `keep-alive` Attribute <sup>replaced</sup>

`keep-alive` is no longer a special attribute, but rather a wrapper component, similar to `<transition>`. For example:

``` html
<keep-alive>
  <component v-bind:is="view"></component>
</keep-alive>
```

This makes it possible to use `<keep-alive>` on multiple conditional children:

``` html
<keep-alive>
  <todo-list v-if="todos.length > 0"></todo-list>
  <no-todos-gif v-else></no-todos-gif>
</keep-alive>
```

<p class="tip">When `<keep-alive>` has multiple children, they should eventually evaluate to a single child. Any child other than the first one will be ignored.</p>

When used together with `<transition>`, make sure to nest it inside:

``` html
<transition>
  <keep-alive>
    <component v-bind:is="view"></component>
  </keep-alive>
</transition>
```

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find <code>keep-alive</code> attributes.</p>
</div>
{% endraw %}

## Interpolation

### Interpolation within Attributes <sup>removed</sup>

Interpolation within attributes is no longer valid. For example:

``` html
<button class="btn btn-{{ size }}"></button>
```

Should either be updated to use an inline expression:

``` html
<button v-bind:class="'btn btn-' + size"></button>
```

Or a data/computed property:

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
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of interpolation used within attributes.</p>
</div>
{% endraw %}

### HTML Interpolation <sup>removed</sup>

HTML interpolations (`{% raw %}{{{ foo }}}{% endraw %}`) have been removed in favor of the [`v-html` directive](../api/#v-html).

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find HTML interpolations.</p>
</div>
{% endraw %}

### One-Time Bindings <sup>replaced</sup>

One time bindings (`{% raw %}{{* foo }}{% endraw %}`) have been replaced by the new [`v-once` directive](../api/#v-once).

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find one-time bindings.</p>
</div>
{% endraw %}

## Reactivity

### `vm.$watch` <sup>changed</sup>

Watchers created via `vm.$watch` are now fired before the associated component rerenders. This gives you the chance to further update state before the component rerender, thus avoiding unnecessary updates. For example, you can watch a component prop and update the component's own data when the prop changes.

If you were previously relying on `vm.$watch` to do something with the DOM after a component updates, you can instead do so in the `updated` lifecycle hook.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run your end-to-end test suite, if you have one. The <strong>failed tests</strong> should alert to you to the fact that a watcher was relying on the old behavior.</p>
</div>
{% endraw %}

### `vm.$set` <sup>changed</sup>

`vm.$set` is now an alias for [`Vue.set`](../api/#Vue-set).

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the obsolete usage.</p>
</div>
{% endraw %}

### `vm.$delete` <sup>changed</sup>

`vm.$delete` is now an alias for [`Vue.delete`](../api/#Vue-delete).

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of the obsolete usage.</p>
</div>
{% endraw %}

### `Array.prototype.$set` <sup>removed</sup>

Use `Vue.set` instead.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>.$set</code> on an array. If you miss any, you should see <strong>console errors</strong> from the missing method.</p>
</div>
{% endraw %}

### `Array.prototype.$remove` <sup>removed</sup>

Use `Array.prototype.splice` instead. For example:

``` js
methods: {
  removeTodo: function (todo) {
    var index = this.todos.indexOf(todo)
    this.todos.splice(index, 1)
  }
}
```

Or better yet, pass removal methods an index:

``` js
methods: {
  removeTodo: function (index) {
    this.todos.splice(index, 1)
  }
}
```

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>.$remove</code> on an array. If you miss any, you should see <strong>console errors</strong> from the missing method.</p>
</div>
{% endraw %}

### `Vue.set` and `Vue.delete` on Vue instances <sup>removed</sup>

`Vue.set` and `Vue.delete` can no longer work on Vue instances. It is now mandatory to properly declare all top-level reactive properties in the data option. If you'd like to delete properties on a Vue instance or its `$data`, set it to null.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.set</code> or <code>Vue.delete</code> on a Vue instance. If you miss any, they'll trigger <strong>console warnings</strong>.</p>
</div>
{% endraw %}

### Replacing `vm.$data` <sup>removed</sup>

It is now prohibited to replace a component instance's root $data. This prevents some edge cases in the reactivity system and makes the component state more predictable (especially with type-checking systems).

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of overwriting <code>vm.$data</code>. If you miss any, <strong>console warnings</strong> will be emitted.</p>
</div>
{% endraw %}

### `vm.$get` <sup>removed</sup>

Instead, retrieve reactive data directly.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$get</code>. If you miss any, you'll see <strong>console errors</strong>.</p>
</div>
{% endraw %}

## DOM-Focused Instance Methods

### `vm.$appendTo` <sup>removed</sup>

Use the native DOM API:

``` js
myElement.appendChild(vm.$el)
```

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$appendTo</code>. If you miss any, you'll see <strong>console errors</strong>.</p>
</div>
{% endraw %}

### `vm.$before` <sup>removed</sup>

Use the native DOM API:

``` js
myElement.parentNode.insertBefore(vm.$el, myElement)
```

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$before</code>. If you miss any, you'll see <strong>console errors</strong>.</p>
</div>
{% endraw %}

### `vm.$after` <sup>removed</sup>

Use the native DOM API:

``` js
myElement.parentNode.insertBefore(vm.$el, myElement.nextSibling)
```

Or if `myElement` is the last child:

``` js
myElement.parentNode.appendChild(vm.$el)
```

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$after</code>. If you miss any, you'll see <strong>console errors</strong>.</p>
</div>
{% endraw %}

### `vm.$remove` <sup>removed</sup>

Use the native DOM API:

``` js
vm.$el.remove()
```

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$remove</code>. If you miss any, you'll see <strong>console errors</strong>.</p>
</div>
{% endraw %}

## Meta Instance Methods

### `vm.$eval` <sup>removed</sup>

No real use. If you do happen to rely on this feature somehow and aren't sure how to work around it, post on [the forum](https://forum.vuejs.org/) for ideas.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$eval</code>. If you miss any, you'll see <strong>console errors</strong>.</p>
</div>
{% endraw %}

### `vm.$interpolate` <sup>removed</sup>

No real use. If you do happen to rely on this feature somehow and aren't sure how to work around it, post on [the forum](https://forum.vuejs.org/) for ideas.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$interpolate</code>. If you miss any, you'll see <strong>console errors</strong>.</p>
</div>
{% endraw %}

### `vm.$log` <sup>removed</sup>

Use the [Vue Devtools](https://github.com/vuejs/vue-devtools) for the optimal debugging experience.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>vm.$log</code>. If you miss any, you'll see <strong>console errors</strong>.</p>
</div>
{% endraw %}

## Instance DOM Options

### `replace: false` <sup>removed</sup>

Components now always replace the element they're bound to. To simulate the behavior of `replace: false`, you can wrap your root component with an element similar to the one you're replacing. For example:

``` js
new Vue({
  el: '#app',
  template: '<div id="app"> ... </div>'
})
```

Or with a render function:

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
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>replace: false</code>.</p>
</div>
{% endraw %}

## Global Config

### `Vue.config.debug` <sup>removed</sup>

No longer necessary, since warnings come with stack traces by default now.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.config.debug</code>.</p>
</div>
{% endraw %}

### `Vue.config.async` <sup>removed</sup>

Async is now required for rendering performance.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.config.async</code>.</p>
</div>
{% endraw %}

### `Vue.config.delimiters` <sup>replaced</sup>

This has been reworked as a [component-level option](../api/#delimiters). This allows you to use alternative delimiters within your app without breaking 3rd-party components.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.config.delimiters</code>.</p>
</div>
{% endraw %}

### `Vue.config.unsafeDelimiters` <sup>removed</sup>

HTML interpolation has been [removed in favor of `v-html`](#HTML-Interpolation-removed).

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.config.unsafeDelimiters</code>. After this, the helper will also find instances of HTML interpolation so that you can replace them with `v-html`.</p>
</div>
{% endraw %}

## Global API

### `Vue.extend` with `el` <sup>removed</sup>

The el option can no longer be used in `Vue.extend`. It's only valid as an instance creation option.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run your end-to-end test suite or app after upgrading and look for <strong>console warnings</strong> about the <code>el</code> option with <code>Vue.extend</code>.</p>
</div>
{% endraw %}

### `Vue.elementDirective` <sup>removed</sup>

Use components instead.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.elementDirective</code>.</p>
</div>
{% endraw %}

### `Vue.partial` <sup>removed</sup>

Partials have been removed in favor of more explicit data flow between components, using props. Unless you're using a partial in a performance-critical area, the recommendation is to use a [normal component](components.html) instead. If you were dynamically binding the `name` of a partial, you can use a [dynamic component](components.html#Dynamic-Components).

If you happen to be using partials in a performance-critical part of your app, then you should upgrade to [functional components](render-function.html#Functional-Components). They must be in a plain JS/JSX file (rather than in a `.vue` file) and are stateless and instanceless, like partials. This makes rendering extremely fast.

A benefit of functional components over partials is that they can be much more dynamic, because they grant you access to the full power of JavaScript. There is a cost to this power however. If you've never used a component framework with render functions before, they may take a bit longer to learn.

{% raw %}
<div class="upgrade-path">
  <h4>Upgrade Path</h4>
  <p>Run the <a href="https://github.com/vuejs/vue-migration-helper">migration helper</a> on your codebase to find examples of <code>Vue.partial</code>.</p>
</div>
{% endraw %}
