---
<<<<<<< HEAD
title: Depuração no VS Code e no Chrome
=======
title: Debugging in VS Code
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
type: cookbook
order: 8
---

<<<<<<< HEAD
## Introdução

Toda aplicação chega em um ponto onde é necessário entender as falhas, pequenas ou grandes. Nesse tutorial, vamos explorar algumas abordagens para usuários do VS Code, que estão usando o Chrome para testar. 
=======
Every application reaches a point where it's necessary to understand failures, small to large. In this recipe, we explore a few workflows for VS Code users who would like to debug their application in the browser.

This recipe shows how to debug [Vue CLI](https://github.com/vuejs/vue-cli) applications in VS Code as they run in the browser.

<p class="tip">Note: This recipe covers Chrome and Firefox. If you know how to setup VS Code debugging with other browsers, please consider sharing your insights (see bottom of the page).</p>
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

Esse tutorial mostra como usar a extensão [Debugger for Chrome](https://github.com/Microsoft/VSCode-chrome-debug) com o VS Code para depurar aplicações Vue.js geradas através do [Vue CLI](https://github.com/vuejs/vue-cli).

<<<<<<< HEAD
## Pré-requisitos

Você precisa ter o Chrome e o VS Code instalados. Certifique-se de ter a versão mais recente da extensão [Debugger for Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) instalada no VS Code.

Instale e crie o projeto com o [vue-cli](https://github.com/vuejs/vue-cli) e com as intruções presentes no `README.md`. Acesse o novo diretório criado para a aplicação e abra no VS Code.

### Exibindo Fontes no Chrome Devtools

Antes que você depure seus componentes Vue no VS Code, você precisa atualizar a configuração gerada do Webpack para construir os _sourcemaps_. Nós fazemos isso para que o nosso depurador tenha como mapear o código compactado para cada posição correspondente no arquivo original. Isso garante que você possa depurar a aplicação, mesmo após os recursos terem sido otimizados pelo Webpack.

Vá até `config/index.js` e localize a propriedade `devtool`. Atualize para:
=======
Make sure you have VS Code and the browser of your choice installed, and the latest version of the corresponding Debugger extension installed and enabled:

* [Debugger for Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)
* [Debugger for Firefox](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-firefox-debug)

Install and create a project with the [vue-cli](https://github.com/vuejs/vue-cli), following the instructions in the [Vue CLI Guide](https://cli.vuejs.org/). Change into the newly created application directory and open VS Code.

### Displaying Source Code in the Browser

Before you can debug your Vue components from VS Code, you need to update the generated Webpack config to build sourcemaps. We do this so that our debugger has a way to map the code within a compressed file back to its position in the original file. This ensures that you can debug an application even after your assets have been optimized by Webpack.

If you use Vue CLI 2, set or update the `devtool` property inside `config/index.js`:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

```json
devtool: 'source-map',
```

<<<<<<< HEAD
Com o Vue CLI 3, você definirá a propriedade `devtool` dentro do `vue.config.js`:
=======
If you use Vue CLI 3, set or update the `devtool` property inside `vue.config.js`:

>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b
```js
module.exports = {
  configureWebpack: {
    devtool: 'source-map'
  }
}
```

### Iniciando a Aplicação pelo VS Code

<<<<<<< HEAD
Clique no icone de duparação na barra de tarefas para trazer a visualização de depuração, então clique no icone de engrenagem para configurar o arquivo `launch.json`, selecionando **Chrome** como o ambiente de execução. Substitua o conteúdo gerado no arquivo pela configuração correspondente:
=======
<p class="tip">We're assuming the port to be `8080` here. If it's not the case (for instance, if `8080` has been taken and Vue CLI automatically picks another port for you), just modify the configuration accordingly.</p>

Click on the Debugging icon in the Activity Bar to bring up the Debug view, then click on the gear icon to configure a launch.json file, selecting **Chrome/Firefox: Launch** as the environment. Replace content of the generated launch.json with the corresponding configuration:
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

![Adicionando Configuração do Chrome](/images/config_add.png)

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "vuejs: chrome",
      "url": "http://localhost:8080",
      "webRoot": "${workspaceFolder}/src",
      "breakOnLoad": true,
      "sourceMapPathOverrides": {
        "webpack:///./src/*": "${webRoot}/*"
      }
    },
    {
      "type": "firefox",
      "request": "launch",
      "name": "vuejs: firefox",
      "url": "http://localhost:8080",
      "webRoot": "${workspaceFolder}/src",
      "pathMappings": [{ "url": "webpack:///src/", "path": "${webRoot}/" }]
    }
  ]
}
```

## Configurando um Ponto de Parada

1.  Defina um ponto de parada (_breakpoint_) em **src/components/HelloWorld.vue** na `linha 90` onde a função `data` retorna uma String.

  ![Renderizando o Ponto de Parada](/images/breakpoint_set.png)

2.  Abra seu terminal favortio na raiz e sirva o aplicativo usando o Vue CLI:

  ```
  npm run serve
  ```

<<<<<<< HEAD
3.  Vá ate a visualização de depuração e selecione a configuração **'vuejs: chrome'**, então presione F5 ou clique no botão verde de inicio.

4.  Seu ponto de parada deve ser ativado com a abertura do Chorme na url `http://localhost:8080`.
=======
3.  Go to the Debug view, select the **'vuejs: chrome/firefox'** configuration, then press F5 or click the green play button.

4.  Your breakpoint should now be hit as a new browser instance opens `http://localhost:8080`.
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

  ![Ativando o Ponto de Parada](/images/breakpoint_hit.png)

## Padrões Alternativos

### Vue Devtools

<<<<<<< HEAD
Existem outros métodos para depurar, com diversas complexidades. A mais popular e simples delas é usando o excelente [vue-devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd). Um dos beneficios de trabalhar com a barra de ferramentas do Vue é poder ativar a edição em tempo real das propriedades e ver as mudanças refletidas imetiatamente. Outro grande benefício é a habilidade de realizar uma viagem temporal de depuração no histórico de mutações do Vuex.
=======
There are other methods of debugging, varying in complexity. The most popular and simple of which is to use the excellent Vue.js devtools [for Chrome](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd) and [for Firefox](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/). Some of the benefits of working with the devtools are that they enable you to live-edit data properties and see the changes reflected immediately. The other major benefit is the ability to do time travel debugging for Vuex.
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

![Depuração Temporal no Vue Devtools](/images/devtools-timetravel.gif)

<p class="tip">Vale observar que se a página usa uma versão de produção/minificada do Vue.js (como o _link_ padrão do CDN), a inspeção do _devtools_ é desabilitada por padrão, então o painel Vue não vai aparecer. Se você trocar para uma versão não minificada, você pode ter de realizar uma atualização forçada para vê-lo (limpando o cache do navegador, por exemplo).
</p>

<<<<<<< HEAD
### Vuetron

[Vuetron](http://vuetron.io/) é um projeto realmente legal, que amplia o trabalho que o _devtools_ tem feito. Em adição ao fluxo de trabalho normal do _devtools_, você será capaz de:

* Rapidamente ver requisições/respostas da API: se você está usando a API `fetch` para requisições, o evento será exibido para qualquer requisição feita. A área de informação detalhada exibe os dados da requisição, bem como os dados da resposta.
* Observar partes específicas do estado da sua aplicação para depuração rápida.
* Visualizar a hierarquia de componentes e uma animação que permite esconder ou exibir a árvore da hierarquia específica de um componente.

![Hierarquia no Vuetron](/images/vuetron-heirarchy.gif)

### Declaração Simples do Depurador
=======
### Simple Debugger Statement
>>>>>>> dc8b494b86b36d0169cea6f972596faeb6ef228b

O exemplo acima é um ótimo fluxo de trabalho. Entretando, existe uma simples opção alternativa, onde você pode usar a [declaração nativa de depuração (`debugger`)](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Statements/debugger) diretamente no seu código. Se você escolher trabalhar desse modo, é importante se lembrar de remover a declaração quando terminar a depuração.

```js
<script>
export default {
  data() {
    return {
      message: ''
    }
  },
  mounted() {
    const hello = 'Olá mundo!'
    debugger
    this.message = hello
  }
};
</script>
```

## Reconhecimento

Este tutorial foi baseado na contribuição do [Kenneth Auchenberg](https://twitter.com/auchenberg), [disponível aqui](https://github.com/Microsoft/VSCode-recipes/tree/master/vuejs-cli).
