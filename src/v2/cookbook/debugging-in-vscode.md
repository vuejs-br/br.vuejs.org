---
title: Depuração no VS Code com Navegador
type: cookbook
order: 8
---

Toda aplicação chega em um ponto onde é necessário entender as falhas, de pequenas à grandes. Nesse tutorial, vamos explorar alguns fluxos de trabalho para usuários do VS Code que gostariam de depurar seus aplicativos no navegador.

Este tutorial mostra como depurar aplicações geradas através do [Vue CLI](https://github.com/vuejs/vue-cli) enquanto são executadas no navegador.

<p class="tip">Nota: Este tutorial cobre Chrome e Firefox. Se você sabe como configurar a depuração do VS Code com outros navegadores, por favor, considere compartilhar suas ideias (veja a parte inferior da página).</p>

## Pré-requisitos

Você precisa ter o VS Code e seu navegador preferido instalados e a versão mais recente da extensão correspondente do depurador instalada e ativada:

* [Depurador para Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)
* [Depurador para Firefox](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-firefox-debug)

Instale e crie o projeto com o [vue-cli](https://github.com/vuejs/vue-cli) seguindo as instruções no [Guia do Vue CLI](https://cli.vuejs.org/).
 Acesse o novo diretório criado para a aplicação e abra no VS Code.

### Exibindo Código Fonte no Navegador

Antes que você depure seus componentes Vue no VS Code, você precisa atualizar a configuração gerada do Webpack para construir os _sourcemaps_. Nós fazemos isso para que o nosso depurador tenha como mapear o código compactado para cada posição correspondente no arquivo original. Isso garante que você possa depurar a aplicação, mesmo após os recursos terem sido otimizados pelo Webpack.

Se você usar o Vue CLI 2, defina ou atualize a propriedade `devtool` dentro de `config/index.js`, para:

```json
devtool: 'source-map',
```

Se você usar o Vue CLI 3, defina ou atualize a propriedade `devtool` dentro de `vue.config.js`, para:

```js
module.exports = {
  configureWebpack: {
    devtool: 'source-map'
  }
}
```

### Iniciando a Aplicação pelo VS Code

<p class="tip">Estamos assumindo que a porta seja `8080` aqui. Se não for o caso (por exemplo, se o `8080` estiver em uso e o Vue CLI escolher automaticamente outra porta para você), apenas modifique a configuração de acordo.</p>

Clique no ícone de depuração na barra de atividades para trazer a tela de depuração, então clique no ícone de engrenagem para configurar o arquivo `launch.json`, selecionando **Chrome/Firefox: Launch** como o ambiente de execução. Substitua o conteúdo gerado no arquivo pela configuração correspondente:

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
        "webpack:///src/*": "${webRoot}/*"
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

1. Defina um ponto de parada (_breakpoint_) em **src/components/HelloWorld.vue** na `linha 90` onde a função `data` retorna uma String.

![Renderizando o Ponto de Parada](/images/breakpoint_set.png)

2. Abra seu terminal favorito na raiz e sirva o aplicativo usando o Vue CLI:

```
npm run serve
```

3.  Vá até a tela de depuração e selecione a configuração **'vuejs: chrome/firefox'**, então pressione F5 ou clique no botão verde de inicio.

4.  Seu ponto de parada agora será ativado com a abertura do navegador na url `http://localhost:8080`.

![Ativando o Ponto de Parada](/images/breakpoint_hit.png)

## Padrões Alternativos

### Vue Devtools

Existem outros métodos para depurar, com diversas complexidades. Os mais populares e simples deles é usando o excelente Vue.js devtools [para Chrome](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd) e [para Firefox](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/). Um dos benefícios de trabalhar com a ferramenta de desenvolvedor do Vue é poder ativar a edição em tempo real das propriedades e ver as mudanças refletidas imediatamente. Outro grande benefício é a habilidade de realizar uma viagem temporal de depuração no histórico de mutações do Vuex.

![Depuração Temporal no Vue Devtools](/images/devtools-timetravel.gif)

<p class="tip">Vale observar que se a página usa uma versão de produção/minificada do Vue.js (como o _link_ padrão do CDN), a inspeção do _devtools_ é desabilitada por padrão, então o painel Vue não vai aparecer. Se você trocar para uma versão não minificada, você pode ter de realizar uma atualização forçada para vê-lo (limpando o cache do navegador, por exemplo).</p>

### Declaração Simples do Depurador

O exemplo acima é um ótimo fluxo de trabalho. Entretanto, existe uma simples opção alternativa, onde você pode usar a [declaração nativa de depuração (`debugger`)](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Statements/debugger) diretamente no seu código. Se você escolher trabalhar desse modo, é importante se lembrar de remover a declaração quando terminar a depuração.

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
