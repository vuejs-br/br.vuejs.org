---
title: Sistemas de Ícones SVG Editáveis
type: cookbook
order: 4
---

## Exemplo Base

Existem muitas maneiras de se criar um sistema de ícone SVG, mas há um método que tira vantagem da capacidade do Vue de criar ícones _inline_ editáveis como componente. Algumas das vantagens desta forma de trabalhar são:

* São fáceis de se editar em tempo real
* São animáveis
* Pode-se usar `props`, definindo padrões ou alterando-as caso necessário
* Elas são incorporados, então não exigem requisições HTTP adicionais
* Eles podem ser acessados dinamicamente

Primeiramente, criaremos um arquivo para cada ícone, os nomeando de forma padronizada para fácil recuperação:

> components/icons/IconBox.vue
> components/icons/IconCalendar.vue
> components/icons/IconEnvelope.vue

Eis um exemplo de repositório, em que é possível ver esse padrão em uso:
[https://github.com/sdras/vue-sample-svg-icons/](https://github.com/sdras/vue-sample-svg-icons/)

![Documentação do repositório](https://s3-us-west-2.amazonaws.com/s.cdpn.io/28963/screendocs.jpg 'Exemplo da documentação')

Criaremos um componente de ícone base (`IconBase.vue`), que usará um `slot`.

```html
<template>
  <svg xmlns="http://www.w3.org/2000/svg"
    :width="width"
    :height="height"
    viewBox="0 0 18 18"
    :aria-labelledby="iconName"
    role="presentation"
  >
    <title
      :id="iconName"
      lang="en"
    >{{ iconName }} icon</title>
    <g :fill="iconColor">
      <slot />
    </g>
  </svg>
</template>
```

Você pode usar essa base de ícones do jeito que está – a única coisa que você pode precisar atualizar é a `viewBox`, dependendo da _viewBox_ dos ícones que for utilizar. Na base, vamos usar propriedades para `width`, `height`, `iconColor` e `iconName`, para que possam ser atualizados dinamicamente. O nome será usado tanto para o conteúdo do `<title>` e seu `id` quanto para a acessibilidade.

Nosso _script_ vai ter a aparência a seguir. Definimos alguns valores padrão para que o ícone seja renderizado consistentemente mesmo se as propriedades não forem preechidas:

```js
export default {
  props: {
    iconName: {
      type: String,
      default: 'box'
    },
    width: {
      type: [Number, String],
      default: 18
    },
    height: {
      type: [Number, String],
      default: 18
    },
    iconColor: {
      type: String,
      default: 'currentColor'
    }
  }
}
```

A propriedade `currentColor`, usada como padrão para o preenchimento, irá permitir ao ícone herdar a cor do texto que o rodeia. Podemos, inclusive, passar uma cor diferente através desta `prop`, caso desejemos.

Podemos usá-lo diretamente, bastando que o conteúdo do componente `IconWrite.vue` seja um conjunto de `path` referente ao desenho desejado:

```html
<icon-base icon-name="write"><icon-write /></icon-base>
```

Caso você queira fazer vários tamanhos diferentes para o ícone, podemos alcançar facilmente:

```html
<p>
  <!-- pode-se passar pequenos `width` e `height` -->
  <icon-base
    width="12"
    height="12"
    icon-name="write"
  ><icon-write /></icon-base>
  <!-- ou pode-se usar o padrão, que é 18 -->
  <icon-base icon-name="write"><icon-write /></icon-base>
  <!-- ou também deixar um pouco maior :) -->
  <icon-base
    width="30"
    height="30"
    icon-name="write"
  ><icon-write /></icon-base>
</p>
```

<img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/28963/Screen%20Shot%202018-01-01%20at%204.51.40%20PM.png" width="450" />

## Ícones Animáveis

Manter ícones como componentes se mostra muito conveniente quando desejamos animá-los, especialmente em uma interação. Utilizando SVGs _inline_, temos maior suporte a interações, seja através de _script_ ou de _style_. Eis um exemplo de um ícone sendo animado durante um evento `@click`:

```html
<template>
  <svg
    @click="startScissors"
    xmlns="http://www.w3.org/2000/svg"
    viewBox="0 0 100 100"
    width="100"
    height="100"
    aria-labelledby="scissors"
    role="presentation"
  >
    <title
      id="scissors"
      lang="en"
    >Ícone de tesoura animado</title>
    <path
      id="bk"
      fill="#fff"
      d="M0 0h100v100H0z"/>
    <g ref="leftscissor">
      <path d="M..."/>
      ...
    </g>
    <g ref="rightscissor">
      <path d="M..."/>
      ...
    </g>
  </svg>
</template>
```

```js
import { TweenMax, Sine } from 'gsap'

export default {
  methods: {
    startScissors() {
      this.scissorAnim(this.$refs.rightscissor, 30)
      this.scissorAnim(this.$refs.leftscissor, -30)
    },
    scissorAnim(el, rot) {
      TweenMax.to(el, 0.25, {
        rotation: rot,
        repeat: 3,
        yoyo: true,
        svgOrigin: '50 45',
        ease: Sine.easeInOut
      })
    }
  }
}
```

Estamos aplicando `refs` para os grupos de caminhos que precisamos mover e, como os lados da tesoura precisam mover-se em conjunto, criamos uma função que permite o reuso em ambas as `refs`. O uso da biblioteca _GreenSock_ ajuda a resolver questões de suporte às animações e problemas de `transform-origin` entre os navegadores.

<p data-height="300" data-theme-id="0" data-slug-hash="dJRpgY" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Editable SVG Icon System: Animated icon" class="codepen">Veja em <a href="https://codepen.io/team/Vue/pen/dJRpgY/">Sistema de Ícones SVG Editáveis: Ícone Animado</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>) em <a href="https://codepen.io">CodePen</a>.</p><script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

<p style="margin-top:-30px">Um resultado belo e fácil de atingir! E fácil de se atualizar sempre que necessário.</p>

Mais exemplos de animações podem ser visto [neste repositório](https://github.com/sdras/vue-sample-svg-icons/).

## Notas Adicionais

Sabemos que _designers_ podem mudar de ideia. Ou podem ocorrer mudanças nos requisitos do produto. Manter todo o seu sistema de ícones baseados em componentes garante a possibilidade de atualizá-los todos, e os ter automaticamente aplicados em todo o sistema. Mesmo com o uso de uma ferramenta do tipo _icon loader_, algumas situações irão requerer que você recrie ou edite todos os SVG, para tornar a mudança global. O presente método pode ainda poupar-lhe tempo e dor.

## Quando Evitar o Padrão

Esse tipo de sistema de ícones SVG é muito útil quando se tem um número de ícones usados de diferentes maneiras por todo o _site_. Entretanto, se houver grande repetição do mesmo ícone em uma página (ex.: uma grande tabela com um ícone _delete_ em cada linha), poderá fazer mais sentido ter todos os ícones compilados em um arquivo de _sprites_, com a _tag_ `<use>` para carregá-los.

## Padrões Alternativos

Outras ferramentas que podem auxiliar no gerenciamento de ícones:

* [svg-sprite-loader](https://github.com/kisenka/svg-sprite-loader)
* [svgo-loader](https://github.com/rpominov/svgo-loader)

Estes pacotes agrupam SVGs em tempo de compilação, mas os torna um pouco mais difíceis de se editar durante o tempo de execução, pois a _tag_ `<use>` pode introduzir problemas de compatibildiade entre navegadores para quaisquer ações mais complexas. Eles ainda deixam você com duas propriedades `viewBox` aninhadas e, desta forma, dois sistemas de coordenadas. Isto deixa a implementação um pouco mais complexa.
