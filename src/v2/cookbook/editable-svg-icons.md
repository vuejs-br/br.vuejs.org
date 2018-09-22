---
Título: Sistema de ícones SVG Editáveis 
Tipo: cookbook
ordem: 4
---

## Base de Exemplo

Existem muitas maneiras de criar um sistema de ícone SVG, mas há um método que tira vantagem da capacidade do Vue para criar ícones *in-line* editáveis como componente. Alguma das vantagens dessa forma de trabalhar são:

* Facilidade de se editar enquanto se está produzindo
* São animáveis
* Pode-se usar props padrão, e padronizá-las para manter em um tamanho característico ou altera-lás caso necessário
* Elas são *in-line*, então requisições HTTP não são requisitadas
* Elas podem ser acessadas dinamicamente


Primeiramente, criaremos um arquivo para todos os ícones, e nomearemos com um estilo padronizado para uma fácil recuperação

> components/icons/IconBox.vue
> components/icons/IconCalendar.vue
> components/icons/IconEnvelope.vue

Eis um exemplo de repositório, onde é possível ver esse exemplo:
[https://github.com/sdras/vue-sample-svg-icons/](https://github.com/sdras/vue-sample-svg-icons/)

![Documentation site](https://s3-us-west-2.amazonaws.com/s.cdpn.io/28963/screendocs.jpg 'Docs demo')

Criaremos um componente de ícone base (`IconBase.vue`), que usará um slot.

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

Você pode usar essa base de ícones como é – A única coisa que você pode precisar para atualizar é a `viewBox`, dependendo da *viewBox* de seus ícones. Na base, vamos fazer o `width`, `height`, `iconColor` e o nome da props do ícone para que essa, possa ser atualizado dinamicamente com essa props. O nome será usado tanto para o conteúdo do `<title>` e seu `id` para acessibilidade.

Nosso *script* vai parecer dessa forma, nos teremos alguns padrões para que nosso ícone seja renderizado consistentemente, a não ser que seu estado seja alterado:

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

A propriedade `currentColor` que é a padrão no preenchimento ira fazer um ícone herdar a cor independente do que o texto que o rodeie. Podemos inclusive passar um cor diferente como uma prop caso desejemos.

Podemos usa-ló como quisermos, apenas com o conteúdo do `IconWrite.vue` contendo o caminho dentro do ícone:

```html
<icon-base icon-name="write"><icon-write /></icon-base>
```

Agora, caso deseje fazer vários tamanho diferentes para o ícone, podemos então, fazer facilmente:

```html
<p>
  <!-- pode-se passar um pequeno `width` e `height` como props -->
  <icon-base
    width="12"
    height="12"
    icon-name="write"
  ><icon-write /></icon-base>
  <!-- ou pode-se usar o padrão, que é 18 -->
  <icon-base icon-name="write"><icon-write /></icon-base>
  <!-- ou faze-lo um pouco maior também :) -->
  <icon-base
    width="30"
    height="30"
    icon-name="write"
  ><icon-write /></icon-base>
</p>
```

<img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/28963/Screen%20Shot%202018-01-01%20at%204.51.40%20PM.png" width="450" />

## Ícones animáveis

Manter ícones como componentes faz-se conveniente quando deseja-se animá-los especialmente em uma interação. SVG’s *inline* tem um maior suporte para interações com qualquer método. Eis um simples exemplos de um ícone sendo animado com um *click*:

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
    >Ícone de animação da tesoura</title>
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

Estamos aplicando `refs` para os grupos de caminhos que precisamos mover, e como os lados da tesoura precisam mover-se em conjunto, criaremos uma função que permita o reuso, nas `refs` que utilizaremos. O Uso de *GreenSock* ajuda a resolver no suporte a animações e os problemas de `transform-origin` dos navegadores.

<p data-height="300" data-theme-id="0" data-slug-hash="dJRpgY" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Editable SVG Icon System: Animated icon" class="codepen">Veja em <a href="https://codepen.io/team/Vue/pen/dJRpgY/">Sistema SVG de ícones editáveis: Ícone animado</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>) em <a href="https://codepen.io">CodePen</a>.</p><script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

<p style="margin-top:-30px">Uma bela e fácil realização! E fácil de se atualizar em produção.</p>

Mais exemplos de animações podem ser visto nesse repositório [aqui](https://github.com/sdras/vue-sample-svg-icons/)

## Notas adicionais

Designers podem mudar de idéia. Mudanças nos requisitos do produto. Manter todo o seu sistema de ícones em uma base de componentes, significa a possibilidade de se atualizar todos os ícones e ter a sua propagação para todo o sistema. Mesmo com o uso de um ícone de loader, algumas situações irão requerer que você recrie ou Edit todos os SVG para tornar a mudança global. Esse método pode poupar-lhe tempo e dor.

## Quando evitar esse padrão

Esse tipo de sistema de ícones SVG é muito útil quando se tem um número de ícones que são usados em diferentes maneiras por todo o site. Se a repetição do mesmo ícone ocorre em uma pagina (Ex.: Uma grande tabela com um ícone de delete em cada linha), fará mais sentido ter todo os ícones compilado em uma folha de ícones e usar a tag `<use>` para carrega-las.

## Padrões alternativos

Outra ferramenta que ajuda no gerenciamento de ícones incluem:

* [svg-sprite-loader](https://github.com/kisenka/svg-sprite-loader)
* [svgo-loader](https://github.com/rpominov/svgo-loader)

Esse pacote de ferramentas agrupa SVG’s em tempo real, mas tornam-nas um pouco difíceis de editar durante um runtime em razão da tag `<use>` que pode trazer problemas devido a transição entre navegadores, quando realiza ações complexas. Eles também deixam você com duas propriedades `viewBox` aninhadas e então dois sistemas de iguais. isso tornará sua implementação um pouco mais complexa.
