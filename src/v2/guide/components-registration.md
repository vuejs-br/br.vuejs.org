---
title: Registro de Componentes
type: guide
order: 101
---

> Esta página assume que você já leu o [Básico sobre Componentes](components.html). Leia lá primeiro se você for novo com componentes.

## Nome dos Componentes

Quando registramos um componente, sempre daremos um nome. Por exemplo, no registro global que vimos até agora:

```js
Vue.component('my-component-name', { /* ... */ })
```

O nome do componente é o primeiro argumento de `Vue.component`.

O nome que você dá a um componente depende de onde você pretende utilizá-lo. Quando for utilizar diretamente no DOM (ao contrário de uma _template_ String ou um [Componente Single-File](single-file-components.html), nós recomendamnos fortemente seguir as [regras do W3C](https://html.spec.whatwg.org/multipage/custom-elements.html#valid-custom-element-name) para nome de _tags_ customizadas (todas as letras minuscúlas, com hífen entre as palavras). Isso lhe ajuda a evitar conflitos com atuais e futuros elementos nativos do HTML.

Você pode ver outras recomendações sobre nome de componentes no [Guia de Estilo](../style-guide/#Base-component-names-strongly-recommended).

### Convenção do Nome

Você tem duas opções quando estiver definindo nomes de componentes:

#### Com kebab-case

```js
Vue.component('my-component-name', { /* ... */ })
```

Quando definir um nome de componente com _kebab-case_, você precisa usar a mesma notação quando for referenciar como um elemento customizado, algo como `<my-component-name>`.

#### Com PascalCase

```js
Vue.component('MyComponentName', { /* ... */ })
```

Quando definir um nome de componente com _PascalCase_, você pode usar ambas as notações quando referenciar esse elemento customizado. Isso significa que tanto `<my-component-name>` quanto `<MyComponentName>` serão aceitos. Note que, entretanto, apenas o nome em _kebab-case_ é valido diretamente no DOM (ou seja, fora de _templates_ baseados em Strings).

## Registro Global

Até agora, apenas criamos componentes usando `Vue.component`:

```js
Vue.component('my-component-name', {
  // ... opções ...
})
```

Estes componentes são **registrados globalmente**. Isso significa que eles podem ser usados em _templates_ de qualquer instância Vue (`new Vue`) criada após o registro. Por exemplo:

```js
Vue.component('component-a', { /* ... */ })
Vue.component('component-b', { /* ... */ })
Vue.component('component-c', { /* ... */ })

new Vue({ el: '#app' })
```

```html
<div id="app">
  <component-a></component-a>
  <component-b></component-b>
  <component-c></component-c>
</div>
```

Isso também é aplicado para todos os subcomponentes, isso significa que todos estes três componentes também estão disponíveis _dentro um do outro_.

## Registro Local

Registro global frequentemente não é ideal. Por exemplo, se você está usando um sistema de empacotamento com Webpack, registrar globalmente todos seus componentes significa que, mesmo se você parar de usar um componente, ele ainda estará incluído no seu _build_ final. Isso aumenta desnecessariamente a quantidade de JavaScript que seu usuário precisará baixar.

Nesses casos, você pode definir seus componentes como simples objetos JavaScript:

```js
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
var ComponentC = { /* ... */ }
```

Então, defina os componentes que você gostaria de usar com a opção `components`: 

```js
new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

Para cada propriedade no objeto `components`, a chave será o nome do elemento customizado, enquanto o valor conterá o objeto de opções para o componente.

Note que **registrar localmente os componentes _não_ os torna disponíveis em subcomponentes**. Por exemplo, se você quiser que o `ComponentA` esteja disponível dentro do `ComponentB`, você terá que usar:

```js
var ComponentA = { /* ... */ }

var ComponentB = {
  components: {
    'component-a': ComponentA
  },
  // ...
}
```

Ou, se você usa módulos ES2015, através de Babel e Webpack, pode ficar mais parecido com:

```js
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  },
  // ...
}
```
Note que em ES2015+, colocar uma variável com nome `ComponentA` dentro de um objeto é um atalho para `ComponentA: ComponentA`, isto quer dizer que o nome da variável é ambos:

- o elemento customizado usado no _template_, e
- o nome da variável contendo as opções do componente

## Sistema de Módulos

Se você não está usando um sistema de módulos com `import`/`require`, provavelmente pode pular esta seção agora. Se estiver usando, temos algumas instruções especiais e dicas para você.

### Registro Local em um Sistema de Módulos
Se você continua aqui, então felizmente você está utilizando um sistema de módulos, tal como Babel e Webpack. Nesse caso, nós recomendamos criar um diretório `components`, com cada componente em seu próprio arquivo. 

Então, importe cada componente se você desejar utilizá-lo, antes de registrar localmente. Por exemplo, em um hipotético arquivo `ComponentB.js` ou `ComponentB.vue`:

```js
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  },
  // ...
}
```

Agora, ambos `ComponentA` e `ComponentC` podem ser utilizados dentro do _template_ do `ComponentB`.

### Registro Global Automático para Componentes de Base

Muitos dos seus componentes vão ser relativamente genéricos, possivelmente apenas encapsulando um elemento como um _input_ ou um _button_. Às vezes nos referimos a eles como [componentes base](../style-guide/#Base-component-names-strongly-recommended) e tendem a ser usados frequentemente entre vários de seus outros componentes.

O resultado é que muitos componentes podem incluir uma longa lista de componentes base:

```js
import BaseButton from './BaseButton.vue'
import BaseIcon from './BaseIcon.vue'
import BaseInput from './BaseInput.vue'

export default {
  components: {
    BaseButton,
    BaseIcon,
    BaseInput
  }
}
```

Apenas para permitir uma marcação relativamente pequena no _template_:

```html
<BaseInput
  v-model="searchText"
  @keydown.enter="search"
/>
<BaseButton @click="search">
  <BaseIcon name="search"/>
</BaseButton>
```

Felizmente, se você estiver usando Webpack (ou [Vue CLI 3+](https://github.com/vuejs/vue-cli), que usa o Webpack internamente), você pode se aproveitar do `require.context` para registrar globalmente apenas os componentes base mais comuns. Aqui, um exemplo de código que permite a importação global de componentes base no arquivo de entrada de seu aplicativo (por exemplo, `src/main.js`):

```js
import Vue from 'vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

const requireComponent = require.context(
  // O caminho relativo da pasta de componentes
  './components',
  // Se deve ou não olhar subpastas
  false,
  // Expressão regular para localizar nomes de componentes base
  /Base[A-Z]\w+\.(vue|js)$/
)

requireComponent.keys().forEach(fileName => {
  // Pega a configuração do componente
  const componentConfig = requireComponent(fileName)

  // Obtém nome em PascalCase do componente
  const componentName = upperFirst(
    camelCase(
      // Obtém o nome do arquivo, independentemente da profundidade da pasta
      fileName
        .split('/')
        .pop()
        .replace(/\.\w+$/, '')
    )
  )

  // Registra o componente globalmente
  Vue.component(
    componentName,
    // Olha para as opções em `.default`, existentes
    // se o componente foi exportado com `export default`,
    // caso contrário usa o módulo raiz.
    componentConfig.default || componentConfig
  )
})
```

Lembre-se que o **registro global precisa ocorrer antes da instância Vue ser criada (com `new Vue`)**. Veja [um exemplo](https://github.com/chrisvfritz/vue-enterprise-boilerplate/blob/master/src/components/_globals.js) desse padrão em um contexto de projeto real.
