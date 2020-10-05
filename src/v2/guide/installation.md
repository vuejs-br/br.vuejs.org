---
title: Instalação
type: guide
order: 1
vue_version: 2.5.17
gz_size: "30.90"
---

### Sobre a Compatibilidade

Vue **não** suporta IE8 e versões anteriores, pois usa funcionalidades ECMAScript 5 incompatíveis nestes. Entretanto, suporta todos os [navegadores compatíveis com ECMAScript 5](https://caniuse.com/#feat=es5).

### Versionamento Semântico

Vue segue o [Versionamento Semântico](https://semver.org/lang/pt-BR/) em todos os seus projetos oficiais onde são documentados features e comportamentos. Para comportamentos não documentados ou partes internas expostas, as mudanças estão descritas nas [notas de lançamento](https://github.com/vuejs/vue/releases).

### Notas de Lançamento

Última versão estável: {{vue_version}}

Notas detalhadas para cada versão estão disponíveis no [GitHub](https://github.com/vuejs/vue/releases).

## Vue Devtools

Antes de mais nada, ao utilizar Vue também recomendamos que você instale a extensão [Vue Devtools](https://github.com/vuejs/vue-devtools#vue-devtools) em seu navegador, lhe permitindo inspecionar e depurar suas aplicações Vue em uma interface mais amigável.

## Inclusão Direta com `<script>`

Simplesmente faça o _download_ e inclua a _tag_ `<script>` com o caminho correto. `Vue` será registrado como uma variável global.

<p class="tip">Não use a versão minificada (de produção) durante o desenvolvimento. Você irá perder todos os avisos interessantes para os erros mais comuns!</p>

<div id="downloads">
  <a class="button" href="/js/vue.js" download>Versão Desenvolvedor</a><span class="light info">Mensagens de erro completas e modo de depuração</span>

  <a class="button" href="/js/vue.min.js" download>Versão de Produção</a><span class="light info">Sem mensagens de erro, {{gz_size}}KB min+gzip</span>
</div>

### CDN

Para propósitos de prototipação ou aprendizado, você pode usar a versão mais recente com:

``` html
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```

Para produção, recomendados vincular a um número de versão específico  para evitar quebra de funções das versões mais novas:

``` html
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.0"></script>
```

Se você está usando Modulos ES nativos, existe uma _build_ compatível com isso:

``` html
<script type="module">
  import Vue from 'https://cdn.jsdelivr.net/npm/vue@2.6.0/dist/vue.esm.browser.js'
</script>
```

Você pode navegar pelos códigos-fonte do pacote NPM em [cdn.jsdelivr.net/npm/vue](https://cdn.jsdelivr.net/npm/vue/).

Vue também está disponível no [unpkg](https://unpkg.com/vue@{{vue_version}}/dist/vue.js) e no [cdnjs](https://cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.js) (cdnjs leva algum tempo para sincronizar, então a última versão lançada pode não estar disponível ainda).

Tenha certeza de ler sobre [as diferentes distribuições do Vue](#Explicacao-das-Distribuicoes) e usar a **versão para produção** em seu projeto publicado, alterando `vue.js` por `vue.min.js`. Este é um pacote menor otimizado para desempenho ao invés de experiência de desenvolvimento.

## NPM

NPM é o método de instalação recomendado para construção de aplicações em larga escala com o Vue. Ele combina perfeitamente com empacotadores de módulos, tais como [Webpack](https://webpack.js.org/) ou [Browserify](http://browserify.org/). Vue também oferece ferramentas para a criação de [Componentes Single-File](single-file-components.html).

``` bash
# última versão estável
$ npm install vue
```

## CLI

Vue.js oferece um [CLI oficial](https://github.com/vuejs/vue-cli) para rapidamente construir ambiciosas _Single Page Applications_. Ele possui um conjunto de configurações de _build_ prontas para um processo de trabalho de _front-end_ moderno. Leva apenas alguns minutos para estar pronto e executando com _hot-reload_, _lint_ ao salvar e empacotamento pronto para distribuição em produção. Dê uma olhada na [documentação do Vue CLI](https://cli.vuejs.org) para mais detalhes.

<p class="tip">Este assunto pressupõe conhecimento prévio em Node.js e ferramentas de _build_ associadas. Se você é novo no Vue ou neste tipo de ferramenta, recomendamos fortemente que passe por todo <a href="./">o guia</a> antes de tentar usar o CLI.</p>

<div class="vue-mastery"><a href="https://www.vuemastery.com/courses/real-world-vue-js/vue-cli" target="_blank" rel="sponsored noopener" title="Vue CLI">Assista a uma lição em vídeo gratuita na Vue Mastery</a></div>

## Explicação das Distribuições

No [diretório `dist/`do pacote NPM](https://cdn.jsdelivr.net/npm/vue/dist/) você encontrará muitas distribuições diferentes do Vue.js. Aqui está uma visão geral das variações entre cada uma delas:

| | UMD | CommonJS | ES Module (para empacotadores) | ES Module (para navegadores) |
| --- | --- | --- | --- |
| **Completa** | vue.js | vue.common.js | vue.esm.js | vue.esm.browser.js |
| **Apenas Runtime** | vue.runtime.js | vue.runtime.common.js | vue.runtime.esm.js | - |
| **Completa (produção)** | vue.min.js | - | - | vue.esm.browser.min.js |
| **Apenas Runtime (produção)** | vue.runtime.min.js | - | - | - |

### Termos

- **Completa**: pacotes que contêm tanto o compilador quanto o motor de tempo de execução (em inglês, _runtime_).

- **Compilador**: código responsável por compilar Strings com _templates_ para funções de renderização em JavaScript.

- **Runtime**: código responsável por criar instâncias Vue, renderizar e atualizar o Virtual DOM, etc. Basicamente, tudo menos o compilador.

- **[UMD](https://github.com/umdjs/umd)**: definição de módulos universal, pode ser usada diretamente no navegador através de uma _tag_ `<script>`. O arquivo padrão no jsDelivr CDN em [https://cdn.jsdelivr.net/npm/vue](https://cdn.jsdelivr.net/npm/vue) é a distribuição Compilador + Runtime em formato UMD (`vue.js`).

- **[CommonJS](http://wiki.commonjs.org/wiki/Modules/1.1)**: formato destinado a empacotadores antigos como [browserify](http://browserify.org/) ou [webpack 1](https://webpack.github.io). O arquivo padrão para estes empacotadores (`pkg.main`) é a distribuição apenas Runtime em formato CommonJS (`vue.runtime.common.js`).

- **[ES Module](http://exploringjs.com/es6/ch_modules.html)**: a partir de 2.6 Vue fornece dois _builds_ ES Modules (ESM):

  - ESM para empacotadores: destinado ao uso com empacotadores modernos como o [webpack 2](https://webpack.js.org) ou [rollup](https://rollupjs.org/). O formato ESM é projetado para ser estaticamente analisável, de modo que os empacotadores possam tirar proveito disso para realizar _"tree-shaking"_ e eliminar o código não utilizado de seu pacote final. O arquivo padrão para estes empacotadores (`pkg.module`) é a distribuição apenas Runtime em formato ES Module (`vue.runtime.esm.js`).

  - ESM para navegadores (somente 2.6+): destinado a importações diretas em navegadores modernos via `<script type="module">`.

### Runtime + Compilador vs. Runtime

Se você precisar compilar _templates_ em tempo real no cliente (por exemplo, passando uma String na opção `template`, ou usando elementos pré-existentes no DOM através do HTML como _template_), você vai precisar do compilador e, portanto, do _build_ completo:

``` js
// Isso requer o compilador
new Vue({
  template: '<div>{{ hi }}</div>'
})

// Isso não requer
new Vue({
  render: h => h('div', this.hi)
})
```

Ao utilizar `vue-loader` ou `vueify`, _templates_ dentro de arquivos `*.vue` são pré-compilados para JavaScript durante o processo de construção. Você realmente não precisa do compilador no seu pacote final, desta forma pode utilizar apenas a distribuição Runtime.

Uma vez que as distribuições apenas com o código de Runtime são aproximadamente 30% mais leves que as completas, você deve usá-las sempre que puder. Se você ainda assim desejar utilizar a distribuição completa, precisará configurar um _alias_ no seu empacotador:

#### Webpack

``` js
module.exports = {
  // ...
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js' // 'vue.common.js' para webpack 1
    }
  }
}
```

#### Rollup

``` js
const alias = require('rollup-plugin-alias')

rollup({
  // ...
  plugins: [
    alias({
      'vue': require.resolve('vue/dist/vue.esm.js')
    })
  ]
})
```

#### Browserify

Adicione no `package.json` de seu projeto:

``` js
{
  // ...
  "browser": {
    "vue": "vue/dist/vue.common.js"
  }
}
```

#### Parcel

Adicione no `package.json` de seu projeto:

``` js
{
  // ...
  "alias": {
    "vue" : "./node_modules/vue/dist/vue.common.js"
  }
}
```

### Desenvolvimento vs. Produção

Modos de desenvolvimento/produção estão fixos no código das distribuições UMD: os arquivos não minificados são para desenvolvimento, os arquivos minificados são para produção.

Distribuições CommonJS e ES Module são destinadas a empacotadores, desta forma não oferecemos versões minificadas. Você será responsável por minificar todo seu pacote final.

Tais distribuições também preservam checagens à variável `process.env.NODE_ENV`, para determinar o modo de operação que utilizarão. Você deve configurar apropriadamente seu empacotador para alterar essa variável de ambiente corretamente, de forma a controlar o modo em que o Vue irá executar. Alterar `process.env.NODE_ENV` usando Strings literais também permite que minificadores como UglifyJS possam remover completamente blocos de código exclusivos de desenvolvimento, reduzindo o tamanho final do pacote.

#### Webpack

No Webpack 4+, você pode usar a opção `mode`:

``` js
module.exports = {
  mode: 'production'
}
```

No Webpack 3 e anteriores é necessário utilizar o [DefinePlugin](https://webpack.js.org/plugins/define-plugin/):

``` js
var webpack = require('webpack')

module.exports = {
  // ...
  plugins: [
    // ...
    new webpack.DefinePlugin({
      'process.env': {
        NODE_ENV: JSON.stringify('production')
      }
    })
  ]
}
```

#### Rollup

Utilize o [rollup-plugin-replace](https://github.com/rollup/rollup-plugin-replace):

``` js
const replace = require('rollup-plugin-replace')

rollup({
  // ...
  plugins: [
    replace({
      'process.env.NODE_ENV': JSON.stringify('production')
    })
  ]
}).then(...)
```

#### Browserify

Aplique uma transformação [envify](https://github.com/hughsk/envify) global em seu pacote.

``` bash
NODE_ENV=production browserify -g envify -e main.js | uglifyjs -c -m > build.js
```

Veja também [Dicas de Publicação para Produção](deployment.html).

### Ambientes CSP

Alguns ambientes, como aplicativos do Google Chrome, forçam Content Security Policy (CSP), a qual proíbe o uso de `new Function()` para avaliar expressões. A distribuição completa depende deste recurso para compilar os _templates_, portanto não é viável nestes ambientes.

Por outro lado, a distribuição apenas Runtime é totalmente compatível. Ao utilizar este tipo de distribuição com [Webpack + vue-loader](https://github.com/vuejs-templates/webpack-simple) ou [Browserify + vueify](https://github.com/vuejs-templates/browserify-simple), seus _templates_ serão pré-compilados em funções `render`, as quais funcionam perfeitamente neste tipo de ambiente.

## Compilação de Desenvolvimento

**Importante**: os arquivos compilados no diretório `/dist` do GitHub são criados apenas nos lançamentos de novas versões. Se quiser usar o Vue a partir do código-fonte mais recente do GitHub (e portanto, possivelmente não estável), você terá que compilá-lo você mesmo!

``` bash
git clone https://github.com/vuejs/vue.git node_modules/vue
cd node_modules/vue
npm install
npm run build
```

## Bower

Apenas distribuições UMD estão disponíveis através do Bower.

``` bash
# última versão estável
$ bower install vue
```

## Módulos AMD

Todas as distribuições UMD podem ser utilizadas diretamente como módulos AMD.
