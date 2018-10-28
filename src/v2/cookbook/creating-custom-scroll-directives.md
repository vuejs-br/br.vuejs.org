---
title: Criando Diretivas de Rolagem
type: cookbook
order: 7
---

Existem diversos momentos que podemos querer adicionar um pouco de comportamenteo, especialmente animações, para um evento de rolagem de um site. Existem muitas formas de faze-lo. mas o caminho com menos quantidade de código e dependências é talvez usar uma [diretriz customizada](https://vuejs.org/v2/guide/custom-directive.html) para criar um gancho(hook) para qualquer coisa que dispare um determinado evento de rolagem.

```js
Vue.directive('scroll', {
  inserted: function (el, binding) {
    let f = function (evt) {
      if (binding.value(evt, el)) {
        window.removeEventListener('scroll', f)
      }
    }
    window.addEventListener('scroll', f)
  }
})

// main app
new Vue({
  el: '#app',
  methods: {
    handleScroll: function (evt, el) {
      if (window.scrollY > 50) {
        el.setAttribute(
          'style',
          'opacity: 1; transform: translate3d(0, -10px, 0)'
        )
      }
      return window.scrollY > 100
    }
  }
})
```

```html
<div id="app">
  <h1 class="centered">Scroll me</h1>
  <div
    v-scroll="handleScroll"
    class="box"
  >
    <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. A atque amet harum aut ab veritatis earum porro praesentium ut corporis. Quasi provident dolorem officia iure fugiat, eius mollitia sequi quisquam.</p>
  </div>
</div>
```

<p class="tip">Lembre-se! A diretriz deve ser registrada antes da instância do Vue.</p>

Também precisamos de uma propriedade de estilo que faça a transição dos valores intermediários aqui, neste caso:

```css
.box {
  transition: 1.5s all cubic-bezier(0.39, 0.575, 0.565, 1);
}
```

<p data-height="450" data-theme-id="5162" data-slug-hash="983220ed949ac670dff96bdcaf9d3338" data-default-tab="result" data-user="sdras" data-embed-version="2" data-pen-title="Custom Scroll Directive- CSS Transition" class="codepen">Veja o CodePen <a href="https://codepen.io/sdras/pen/983220ed949ac670dff96bdcaf9d3338/">Custom Scroll Directive- CSS Transition</a> por Sarah Drasner (<a href="https://codepen.io/sdras">@sdras</a>) em <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Ou, com o GreenSock (GSAP) ou qualquer outra biblioteca de animação JavaScript, o código se torna ainda mais simples:

```js
Vue.directive('scroll', {
  inserted: function (el, binding) {
    let f = function (evt) {
      if (binding.value(evt, el)) {
        window.removeEventListener('scroll', f)
      }
    }
    window.addEventListener('scroll', f)
  }
})

// main app
new Vue({
  el: '#app',
  methods: {
    handleScroll: function (evt, el) {
      if (window.scrollY > 50) {
        TweenMax.to(el, 1.5, {
          y: -10,
          opacity: 1,
          ease: Sine.easeOut
        })
      }
      return window.scrollY > 100
    }
  }
})
```

No entanto, removeríamos a transição anterior do CSS dessa implementação porque agora é tratada com JavaScript.

## Os Beneficios de Utilizar Diretrizes Customizadas

O Vue é rico em opções de diretivas, a maioria das quais abrangem casos de uso muito comuns, o que pode criar uma experiência muito produtiva para o desenvolvedor. Mas mesmo se você tiver um caso específico não coberto pelo framework, ele também será abordado nesse caso, porque é fácil criar uma diretiva personalizada para atender às suas necessidades.

Anexar e remover eventos de rolagem para elementos é um bom caso de uso para essa técnica, pois assim como outras diretivas que usamos, elas estão necessariamente vinculadas ao elemento e, caso contrário, precisaríamos encontrar a referência para ele no DOM. Esse padrão evita a necessidade de travessia e mantém a lógica de eventos emparelhada com o nó ao qual ela está em referência.

## Exemplos reais: Usando uma Diretiva de Rolagem Personalizada para Animações em Cascata

No decorrer da criação de um site coeso, você pode descobrir que está reutilizando o mesmo tipo de lógica de animação em várias áreas. Parece simples, então criamos uma diretiva personalizada muito específica, certo? Bem, normalmente, se você estiver reutilizando, você precisará alterá-lo _just_ apenas para cada uso.

Para ajudar a manter nosso código conciso e legível, gostaríamos de passar alguns argumentos pré-definidos, como o ponto inicial e final da animação, enquanto rolamos a página para baixo.

**Esse exemplo é melhor visualizado na [versão tela cheia](https://s.codepen.io/sdras/debug/078c19f5b3ed7f7d28584da450296cd0).**

<p data-height="500" data-theme-id="5162" data-slug-hash="c8c55e3e0bba997350551dd747119100" data-default-tab="result" data-user="sdras" data-embed-version="2" data-pen-title="Scrolling Example- Using Custom Directives in Vue" class="codepen">Veja o CodePen <a href="https://codepen.io/sdras/pen/c8c55e3e0bba997350551dd747119100/">Scrolling Example- Using Custom Directives in Vue</a> por Sarah Drasner (<a href="https://codepen.io/sdras">@sdras</a>) em <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Na demonstração acima, cada uma das seçoes possuem dois diferentes tipos de animações disparadas para a rolagem: uma animação de transformação e uma animação de desenho que anima os caminhos individuais no SVG.  Nós reutilizamos essas duas animações, então nós podemos criar diretivas customizadas para cada. Os argumentos que vamos transmitir ajudarão a manter tudo simples e reusável.

Para monstrar como fazemos isso, vamos dar uma olhada no exemplo da forma de transformação, onde precisaremos declarar o inicio e o fim, alem de passar um valor de caminho para o qual criaremos uma transformação. Estes argumentos são definidos como `binding.value.foo`:

```js
Vue.directive('clipscroll', {
  inserted: function (el, binding) {
    let f = function (evt) {
      var hasRun = false
      if (!hasRun && window.scrollY > binding.value.start) {
        hasRun = true
        TweenMax.to(el, 2, {
          morphSVG: binding.value.toPath,
          ease: Sine.easeIn
        })
      }
      if (window.scrollY > binding.value.end) {
        window.removeEventListener('scroll', f)
      }
    }
    window.addEventListener('scroll', f)
  }
})
```

Nós podemos então utilizar essa animaçõe no nosso modelo, e nesse caso nós estaremos atribuindo a diretiva ao elemento `clipPath`, e passaremos todos os nossos argumentos para as diretivas em um objeto.

```html
<clipPath id="clip-path">
  <path
    v-clipscroll="{ start: '50', end: '100', toPath: 'M0.39 0.34H15.99V22.44H0.39z' }"
    id="poly-shapemorph"
    d="M12.46 20.76L7.34 22.04 3.67 18.25 5.12 13.18 10.24 11.9 13.91 15.69 12.46 20.76z"
  />
</clipPath>
```

## Padrões Alternativos

Diretrizes personalizadas são extremamente úteis, mas voce deve encontrar algumas situações onde necessitará de algo bem específico que ja existe nas bibliotecas de rolagem que você não quer construir do zero.

[Scrollmagic](http://scrollmagic.io/) possui um ecossistema muito rico de ofertas para se trabalhar, conta com boa documentação e exemplos para explorar. Isso inclui, mas não se limita a coisas como [parallax](http://scrollmagic.io/examples/advanced/parallax_scrolling.html), [cascading pinning](http://scrollmagic.io/examples/expert/cascading_pins.html), [section wipes](http://scrollmagic.io/examples/basic/section_wipes_natural.html), and [responsive duration](http://scrollmagic.io/examples/basic/responsive_duration.html).
