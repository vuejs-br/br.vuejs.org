---
title: Eventos Personalizados
type: guide
order: 103
---

> Esta página assume que você já leu o [Básico sobre Componentes](components.html). Leia lá primeiro se você for novo com componentes.

## Nomes dos Eventos

Diferente dos componentes e propriedades, os nomes dos eventos não possuem nenhuma transformação. Sendo assim, o nome de um evento emitido deve corresponder exatamente ao nome usado para chamar esse evento. Por exemplo, caso for emitir um nome de evento _camelCased_:

```js
this.$emit('myEvent')
```

Fazer a escuta a uma versão _kebab-cased_ do evento não terá efeito:

```html
<my-component v-on:my-event="doSomething"></my-component>
```

Diferente dos componentes e propriedades, os nomes de eventos nunca serão usados como variáveis ou nomes de propriedades JavaScript, então não há motivos para usar _camelCase_ ou _PascalCase_. Além disso, `v-on` é uma escuta de eventos contida dentro do DOM que transforma automaticamente o nome em letras minúsculas (devido ao HTML não diferenciar letras maiúsculas e minúsculas), então `v-on:myEvent` se tornaria `v-on:myevent`, sendo assim `myEvent` não seria possível de se escutar.

Por isso, recomendamos que **sempre use _kebab-case_ nos nomes dos eventos**.

## Personalizando `v-model` do Componente

> Novo em 2.2.0+

Por padrão, `v-model` é uma estrutura que possui `value` como uma propriedade e `input` como um evento, mas alguns tipos de _inputs_ como por exemplo _checkboxes_ e _radio buttons_ podem usar o atributo `value` para [diferentes propósitos](https://developer.mozilla.org/pt-BR/docs/Web/HTML/Element/Input/checkbox#Valor). Usando a opção `model`, podemos evitar conflitos em alguns casos:

```js
Vue.component('base-checkbox', {
  model: {
    prop: 'checked',
    event: 'change'
  },
  props: {
    checked: Boolean
  },
  template: `
    <input
      type="checkbox"
      v-bind:checked="checked"
      v-on:change="$emit('change', $event.target.checked)"
    >
  `
})
```

Quando usamos o `v-model` neste componente:

```html
<base-checkbox v-model="lovingVue"></base-checkbox>
```

O valor de `lovingVue` será passado para a propriedade `checked`. E o `lovingVue` será atualizado quando o `<base-checkbox>` emitir o evento `change` com o novo valor.

<p class="tip">Observe que você ainda precisa declarar a propriedade <code>checked</code> que está contida nas opções de <code>props</code>.</p>

## Eventos Nativos nos Componentes

Pode acontecer de você querer escutar diretamente um evento nativo no elemento raiz de um componente. Nestes casos, você pode usar o modificador `.native` no `v-on`:

```html
<base-input v-on:focus.native="onFocus"></base-input>
```

Pode ser útil algumas vezes, mas não é boa ideia quando você está tentando escutar um elemento específico, como um `<input>`. Por exemplo, o componente `<base-input>` acima poderia ser refatorado para que o elemento raiz fosse um `<label>`:

```html
<label>
  {{ label }}
  <input
    v-bind="$attrs"
    v-bind:value="value"
    v-on:input="$emit('input', $event.target.value)">
</label>
```

Nesse caso, a escuta `.native` no pai causaria uma quebra silenciosa. Não apareceriam erros, mas o manipulador `onFocus` não seria chamado.

Para resolver esse problema, Vue fornece a propriedade `$listeners` contendo um objeto de escutas sendo usado no componente. Por exemplo:

```js
{
  focus: function (event) { /* ... */ },
  input: function (value) { /* ... */ }
}
```

Usando a propriedade `$listeners`, você pode encaminhar todas as escutas de eventos no componente para um elemento filho específico com o `v-on="$listeners"`. Para os elementos como o `<input>`, você também irá utilizar o `v-model`, então é útil criar um novo dado computado para as escutas, como o `inputListeners` abaixo:

```js
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  computed: {
    inputListeners: function () {
      var vm = this
      // `Object.assign` mescla objetos para formar um novo objeto
      return Object.assign({},
        // Nós adicionamos todas as escutas do pai
        this.$listeners,
        // Então podemos adicionar escutas personalizadas ou substituir
        // comportamento de algumas escutas.
        {
          // Isso garante que o componente funcione com o v-model
          input: function (event) {
            vm.$emit('input', event.target.value)
          }
        }
      )
    }
  },
  template: `
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on="inputListeners"
      >
    </label>
  `
})
```

Agora o componente `<base-input>` é um **empacotador totalmente transparente**, ou seja, pode ser usado exatamente como um elemento `<input>` tradicional: todos os atributos e escutas funcionarão, sem o modificador `.native`.

## Modificador `.sync`

> Novo em 2.3.0+

Em alguns casos, podemos precisar de uma "interligação bidirecional" para uma propriedade. Infelizmente, a verdadeira interligação bidirecional pode criar problemas de manutenção, porque os componentes filhos podem sofrer mutações e não conseguiremos saber a verdadeira origem.

Por conta disso, recomendamos que emita os nomes dos eventos no padrão `update:myPropName`. Por exemplo, em um componente hipotético com a propriedade `title`, poderíamos comunicar a intenção de atribuir um novo valor com:

```js
this.$emit('update:title', newTitle)
```

Em seguida, o pai pode escutar esse evento e atualizar uma propriedade de dados locais, se quiser. Por exemplo:

```html
<text-document
  v-bind:title="doc.title"
  v-on:update:title="doc.title = $event"
></text-document>
```

Por conveniência, oferecemos um atalho para esse padrão com o modificador `.sync`:

```html
<text-document v-bind:title.sync="doc.title"></text-document>
```

O modificador `.sync` também pode ser usado com o `v-bind` ao usar um objeto para definir várias propriedades de uma só vez:

```html
<text-document v-bind.sync="doc"></text-document>
```

Isso passa cada propriedade no objeto `doc` (por exemplo, `title`) como uma propriedade individual, em seguida, adiciona escutas de atualização para cada `v-on`.

<p class="tip">Usar <code>v-bind.sync</code> com objetos literais, como em <code>v-bind.sync="{ title: doc.title }"</code>, não funcionará, pois há muitos pequenos casos a serem considerados na análise de uma expressão complexa.</p>
