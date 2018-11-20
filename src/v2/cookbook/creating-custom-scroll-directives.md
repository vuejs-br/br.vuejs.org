---
title: Criando Diretivas de Rolagem
type: cookbook
order: 7
---

## Exemplo Base

Há certos momentos em que podemos querer adicionar algum comportamento, especialmente animações, em um evento de rolagem em uma página. Há várias formas de fazer isso mas, possivelmente, o caminho com menos quantidade de código e dependências é usar uma [diretiva personalizada](https://vuejs.org/v2/guide/custom-directive.html) para criar um gatilho para qualquer coisa que disparar um determinado evento de rolagem.

```js
// Código da diretiva
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

// Código principal
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
    class="box">
    <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. A atque amet harum aut ab veritatis earum porro praesentium ut corporis. Quasi provident dolorem officia iure fugiat, eius mollitia sequi quisquam.</p>
  </div>
</div>
```

<p class="tip">Lembre-se! A diretiva deve ser registrada antes de instância Vue.</p>

Precisamos também de uma propriedade de estilo que faça a transição dos valores intermediários aqui, neste caso:

```css
.box {
  transition: 1.5s all cubic-bezier(0.39, 0.575, 0.565, 1);
}
```

<p data-height="450" data-theme-id="5162" data-slug-hash="983220ed949ac670dff96bdcaf9d3338" data-default-tab="result" data-user="sdras" data-embed-version="2" data-pen-title="Custom Scroll Directive- CSS Transition" class="codepen">Veja o Pen <a href="https://codepen.io/sdras/pen/983220ed949ac670dff96bdcaf9d3338/">Custom Scroll Directive- CSS Transition</a> por Sarah Drasner (<a href="https://codepen.io/sdras">@sdras</a>) no <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Ou, usando GreenSock (GSAP) ou qualquer outra biblioteca JavaScript de animação, o código se torna ainda mais simples:

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

No entanto, removeremos a transição CSS da implementação anterior, pois agora será tratada com JavaScript.

## O Benefício das Diretivas Personalizadas

O Vue é rico em opções para diretivas, a maioria das quais abrange casos de uso muito comuns, o que pode criar uma experiência muito produtiva para o desenvolvedor. Mas, mesmo se você tiver uma exceção à uma regra não coberta pelo _framework_, você também estará coberto neste caso, porque é fácil criar uma diretiva personalizada para atender às suas necessidades específicas.

Adicionar e remover escutas a eventos de rolagem para elementos é um bom caso de uso para essa técnica pois, assim como outras diretivas que usamos, estão necessariamente vinculadas ao elemento e, caso contrário, precisaríamos encontrar a referência para ele no DOM. Esse padrão evita a necessidade de varrer o DOM em busca do elemento, além de manter a lógica de eventos emparelhada com o nó ao qual ela está em referência.

## Exemplo do Mundo Real: Usando Diretiva de Rolagem para Animações em Cascata

No decorrer da criação de um _site_ coeso, você pode descobrir que está reutilizando o mesmo tipo de lógica de animação em várias áreas. Parece simples, então criaríamos uma diretiva personalizada específica, certo? Bem, normalmente, se você tentar reutilizá-la, acabaria precisando alterá-la _apenas_ um pouquinho para cada uso.

Para ajudar a manter nosso código conciso e legível, poderíamos querer passar alguns argumentos pré-definidos, como o ponto inicial e final da animação, enquanto rolamos a página para baixo.

**Este exemplo é melhor visualizado na [versão em tela cheia](https://s.codepen.io/sdras/debug/078c19f5b3ed7f7d28584da450296cd0).**

<p data-height="500" data-theme-id="5162" data-slug-hash="c8c55e3e0bba997350551dd747119100" data-default-tab="result" data-user="sdras" data-embed-version="2" data-pen-title="Scrolling Example- Using Custom Directives in Vue" class="codepen">Veja o Pen <a href="https://codepen.io/sdras/pen/c8c55e3e0bba997350551dd747119100/">Scrolling Example- Using Custom Directives in Vue</a> by Sarah Drasner (<a href="https://codepen.io/sdras">@sdras</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Na demonstração acima, cada uma das seções tem dois tipos diferentes de animação acionados a partir da rolagem: uma animação de metamorfose e uma animação de desenho, que anima os caminhos individuais no SVG. Nós reutilizamos essas duas animações, para que possamos criar uma diretiva personalizada para cada uma delas. Os argumentos que transmitiremos ajudarão a manter tudo simples e reutilizável.

Para mostrar como fazemos isso, vamos dar uma olhada no exemplo da metamorfose, onde precisaremos declarar o início e o fim, além de passar um valor de caminho para o qual transformaremos o gráfico vetorial. Estes argumentos são definidos como `binding.value.algo`:

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

Nós podemos, então, usar esta animação em nosso _template_. Neste caso, estamos anexando a diretiva ao elemento `clipPath`, e passamos todos os nossos argumentos para a diretiva através de um objeto.

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

As diretivas personalizadas são extremamente úteis, mas você pode encontrar algumas situações em que precisa de algo muito específico, e que já existe em algumas bibliotecas de rolagem, não desejando reconstruir a partir do zero.

[Scrollmagic](http://scrollmagic.io/) tem um ecossistema muito rico em recursos, bem como uma boa documentação e demonstrações para explorar. Isso inclui, mas não está limitado a coisas como [parallax](http://scrollmagic.io/examples/advanced/parallax_scrolling.html), [cascading pinning](http://scrollmagic.io/examples/expert/cascading_pins.html), [section wipes](http://scrollmagic.io/examples/basic/section_wipes_natural.html), e [responsive duration](http://scrollmagic.io/examples/basic/responsive_duration.html).
