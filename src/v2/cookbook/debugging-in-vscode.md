---
title: Depuração no VS Code e no Chrome
type: cookbook
order: 8
---
## Introdução

Toda aplicação chega em um ponto onde é necessário entender as falhas, pequenas ou grandes. Nesse tutorial, vamos explorar algumas abordagens para usuários do VS Code, que estão usando o Chrome para testar. 

Esse tutorial mostra como usar a extensão [Debugger for Chrome](https://github.com/Microsoft/VSCode-chrome-debug) com o VS Code para depurar aplicações Vue.js geradas pelo [Vue CLI](https://github.com/vuejs/vue-cli).
## Pré requisitos

Você precisa ter o Chrome e o VS Code instalados. Certifique-se de ter a versão mais recente da extensão [Debugger for Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)  instalada no VS Code.

Instale e crie o projeto com o [vue-cli](https://github.com/vuejs/vue-cli), com as intruções presentes no arquivo readme.md do projeto. Acesse o novo diretorio criado para a aplicação e abra o VS Code.

### Exibindo codigo fonte no Chrome Devtools

Antes que você depure seus componentes Vue no VS Code, você precisa atualizar a configuração gerada do Webpack para construir os sourcemaps. Nós fazemos isso para que o nosso depurador tenha como mapear o codigo que está compactado para sua posição que está no arquivo original. Isso garante que você pode depurar a aplicação mesmo após seus recursos terem sido otimizados pelo webpack.


Vá até `config/index.js` e localize a propriedade `devtool`. Atualize para:

```json
devtool: 'source-map',
```

Com o Vue CLI 3 você precisa definir a propriedade `devtool` dentro do `vue.config.js`:
```js
module.exports = {
  configureWebpack: {
    devtool: 'source-map'
  }
}
```

### Iniciando a aplicação pelo VS Code

Clique no icone de duparação na barra de tarefas para trazer a visualização de depuração, então clique no icone de engrenagem para configurar o arquivo launch.json, selecionando **Chrome** para o ambiente. Substitua o conteúdo gerado no arquivo launch.json pelo seguinte conteúdo: 

![Adicionando configuração do Chrome](/images/config_add.png)

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
        "webpack:///src/*": "${webRoot}/*"
      }
    }
  ]
}
```

## Configurando um ponto de parada

1.  Defina um ponto de parada em **src/components/HelloWorld.vue** na `linha 90` onde a função `data` retorna uma String.

  ![Renderizando ponto de parada](/images/breakpoint_set.png)

2.  Abra seu terminal favortio na raiz e sirva o aplicativo usando o Vue CLI:
  ```
  npm start
  ```

3.  Vá ate a visualização de depuração e selecione a configuração **'vuejs: chrome'**, então presione F4 ou clique no botão verde de inicio.

4.  Seu ponto de parada deve ser ativado com a abertura do Chorme na url `http://localhost:8080`.
  ![Ativando ponto de parada](/images/breakpoint_hit.png)

## Padrãoes alternativos

### Vue Devtools

Existem outros métodos para depurar, com diversas complexidades. A mais popular e simples delas é usando o excelente [vue-devtools](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd). Alguns dos beneficios de trabalhar com o devtools são que ele pode ativar a edição em tempo real das propriedades and ver as mudanças refletidas imetiatamente. Um outro grande benefício é a habilidade de realizar uma viagem temporal de depuração para o Vuex.

![Depuração Temporal Devtools](/images/devtools-timetravel.gif)

<p class="tip">Por favor note que se a página usa uma versão de produção/minificada do Vue.js (como o link padrão do CDN), a inspeção do devtools é desabilitada por padrão, então o painel Vue não vai aparecer. Se você trocar para uma versão não minificada, você pode ter que realizar uma atualização forçada para ve-lo (limpando o cache por exemplo). 
</p>

### Vuetron

[Vuetron](http://vuetron.io/) é um projeto realmente legal que amplia o trabalho que o vue-devtools tem feito. Em adição ao fluxo de trabalho normal do devtools, você sera capaz de:

* Rapidamente ver requisições/repostas da API: Se você está usando a API fetch para requisições, o evento será exibido para qualquer requisição feita. A informação detalhada exibe os dados da requisição bem como o dados da resposta.
* Observar partes especificas do estado da sua aplicação para depuração rápida
* Visualizar a hierarquia de componentes e uma animação permite você esconder ou exibir a árvore para exibições de hierarquia específica de um componente.

![Hierarquia no Vuetron](/images/vuetron-heirarchy.gif)

### Declaração simples do depurador

O exemplo acima é um ótimo fluxo de trabalho. Entretando, existe uma opção alternativa onde você pode usar a [declarção nativa de depuração (debugger)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/debugger) direto no seu código. Se você escolher trabalhar desse modo, é importante se lembrar de remover a declaração quando você terminar.

```js
<script>
export default {
  data() {
    return {
      message: ''
    }
  },
  mounted() {
    const hello = 'Ola mundo!'
    debugger
    this.message = hello
  }
};
</script>
```

## Reconhecimento

Este tutorial foi baseado na contribuição do [Kenneth Auchenberg](https://twitter.com/auchenberg), [disponível aqui](https://github.com/Microsoft/VSCode-recipes/tree/master/vuejs-cli)