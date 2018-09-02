---
title: Registro de Componentes
type: guide
order: 101
---

> Essa página assumi que você já leu [Componentes](components.html). Leia ela primeiro se você é novo em lidar com componentes.

## Nome dos Componentes

Quando registramos um componente, sempre iremos dar um nome. Por exemplo, no registro global que vimos até agora:

```js
Vue.component('my-component-name', { /* ... */ })
```

O nome do componente é o primeiro argumento de `Vue.component`.

O nome que você dá a um componente depende de onde você pretende usar ele. Quando usado diretamente no DOM (ao contrário de um string template ou [Componente single-file](single-file-components.html), nós recomendamnos fortemente as [regras do W3C](https://www.w3.org/TR/custom-elements/#concepts) para nome de tags customizadas (todas as letras minuscúlas, deve conter hifén). Isso ajuda você a evitar conflitos com atuais e futuros elementos HTML.

Você pode ver outras recomendações para nome de componentes no [Style Guide](../style-guide/#Base-component-names-strongly-recommended).

### Convenção do Nome

Você tem duas opções quando estiver definindo nome de componentes:

#### Com kebab-case

```js
Vue.component('my-component-name', { /* ... */ })
```

Quando definir um nome de componente com kebab-case, você precisa usar kebab-case quando referenciar ele como um elemento customizado, algo como `<my-component-name>`.

#### Com PascalCase

```js
Vue.component('MyComponentName', { /* ... */ })
```

Quando definir um nome de componente com PascalCase, você pode usar ambos quando referenciar esse elemento customizado. Isso significa que ambos `<my-component-name>` e `<MyComponentName>` são aceitos. Note, que entretanto, apenas o nome em kebab-case é valido diretamente no DOM (ou seja, fora de string templates). 

## Registro Global

Até agora, nós apenas criamos componentes usando `Vue.component`:

```js
Vue.component('my-component-name', {
  // ... opções ...
})
```

Este componentes são **registrados globalmente**. Isso significa que eles podem ser usados em templates de qualquer instância Vue (`new Vue`) criada após o registro. Por exemplo:

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

Registro global frequentemente não é ideal. Por exemplo, se você está usando um sistema de build como Webpack, registrar globalmente todos seus componentes significa que mesmo se você parar de usar um componente, ele ainda estara incluido no seu build final. Isso aumenta desnecessáriamente a quantidade de JavaScript que seu usuário tem que fazer o download.

Nesses casos, você pode definir seus componentes como simples objetos JavaScript:

```js
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
var ComponentC = { /* ... */ }
```

Então defina os componentes que você gostaria de usar dentro da opção `components`: 

```js
new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

Para cada propriedade no objeto `components`, a chave será o nome do elemento customizado, enquanto o valor irá conter o objeto de opções para o componente. 

Note que **registrar localmente os componentes _não_ torna eles disponíveis em subcomponentes**. Por exemplo, se você quiser que o `ComponentA` esteja disponível dentro do `ComponentB`, você terá que usar:

```js
var ComponentA = { /* ... */ }

var ComponentB = {
  components: {
    'component-a': ComponentA
  },
  // ...
}
```

Ou se você usa módulos ES2015, através de Babel e Webpack, pode ficar mais parecido com:

```js
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  },
  // ...
}
```
Note que em ES2015+, colocar uma variável como nome `ComponentA` dentro de um objeto é um atalho para `ComponentA: ComponentA`, isto quer dizer que o nome da variável é ambos:

- o elemento customizado usado no template, e
- o nome da variável contendo as opções do componente

## Sistema de Módulos

Se você não está usando um sistema de módulos com `import`/`require`, você pode provavelmente pular esse seção agora. Se você está usando, nós temos algumas instruções especiais e dicas apenas para você.

### Registro Local em um Sistema de Módulos
Se você continua aqui, então felizmente você está utilizando um sistema de módulos, tal como Babel e Webpack. Nesse caso, nós recomendamos criar um diretório `components`, com cada componente em seu próprio arquivo. 

Então você precisa importar cada componente se você desejar usar ele, antes de registrar localmente. Por exemplo, em um hipotético arquivo `ComponentB.js` ou `ComponentB.vue`:

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

Agora ambos `ComponentA` e `ComponentC` podem ser utilizados dentro do template do `ComponentB`.

### Registro Global Automático para Componentes de Base

Muitos dos seus componentes vão ser relativamente genericos, possivelmente apenas encapsulando um elemento como um input ou um button. Nós as vezes nos referimos a eles como [componentes de base](../style-guide/#Base-component-names-strongly-recommended) e eles tendem a ser usados frequentemente entre seus componentes.

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

Apenas para permitir uma marcação relativamente pequena no template:

```html
<BaseInput
  v-model="searchText"
  @keydown.enter="search"
/>
<BaseButton @click="search">
  <BaseIcon name="search"/>
</BaseButton>
```

Felizmente, se você está usando Webpack (ou [Vue CLI 3+](https://github.com/vuejs/vue-cli), que usa Webpack internamente), você pode usar `require.context` para registrar globalmente apenas esses componentes de base mais comuns. Aqui um exemplo de código que permite você usar a importação global de componentes basse no seu arquivo de entrada do seu aplicativo (Por exemplo `src/main.js`):

```js
import Vue from 'vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

const requireComponent = require.context(
  // O caminho relativo da pasta de componentes
  './components',
  // Se deve ou não olhar em sub pastas
  false,
  //A expressão regular usada para localizar nome de componentes de base
  /Base[A-Z]\w+\.(vue|js)$/
)

requireComponent.keys().forEach(fileName => {
  // Pega a configuração do componente
  const componentConfig = requireComponent(fileName)

  // Pega nome em PascalCase do componente
  const componentName = upperFirst(
    camelCase(
      // Tira o início `./` e a extensão do nome do arquivo
      fileName.replace(/^\.\/(.*)\.\w+$/, '$1')
    )
  )

  // Registra o componente globalmente
  Vue.component(
    componentName,
    // Olha para as opções em `.default`, que irá existir
    // se o componente foi exportado com `export default`,
    // do contrário usa o módulo raiz.
    componentConfig.default || componentConfig
  )
})
```

Lembre que o **registro global precisa acontecer antes da instância Vue ser criada (com `new Vue`)** [Aqui um exemplo](https://github.com/chrisvfritz/vue-enterprise-boilerplate/blob/master/src/components/_globals.js) desse padrão em um contexto de projeto real.