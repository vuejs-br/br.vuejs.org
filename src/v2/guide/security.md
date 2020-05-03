---
title: Segurança
type: guide
order: 504
---

## Reportando Vulnerabilidades

Quando uma vulnerabilidade é reportada, ela se torna imediatamente nossa principal preocupação, com um contribuidor em tempo integral parando tudo para trabalhar nela. Para reportar uma vulnerabilidade, por favor envie email para [security@vuejs.org](mailto:security@vuejs.org).

Mesmo que a descoberta de novas vulnerabilidades seja rara, nós também recomendamos sempre usar as últimas versões do Vue e suas bibliotecas de apoio para assegurar que sua aplicação se mantenha a mais segura possível.

## Regra 1: Apenas Use Templates Confiáveis

A regra de segurança mais fundamental quando se utiliza Vue é **nunca utilizar conteúdo não-confiável como template do seu componente**. Fazer isso é equivalente à liberar execução de JavaScript arbitrário em sua aplicação - e pior, isso pode levar a violações ao servidor se o código é executado durante a renderização no lado do servidor. Um exemplo dessa utilização:

``` js
new Vue({
  el: '#app',
  template: `<div>` + userProvidedString + `</div>` // NUNCA FAÇA ISSO
})
```

Os templates Vue são compilados em JavaScript, e expressões dentro dos templates serão executadas como parte do processo de renderização. Ainda que as expressões sejam processadas em um contexto específico de renderização, devido a complexidade do ambiente de execução global, não é prático para um framework como o Vue proteger completamente contra potencial execução de código malicioso sem que haja uma sobrecarga irreal de performance. A maneira mais direta para evitar essa categoria de problemas completamente é garantir que os templates Vue sejam confiáveis e inteiramente controlados por você.

## Ações do Vue para Te Proteger

### Conteúdo HTML

Seja usando templates ou funções de renderização, o conteúdo é automaticamente convertido em texto. Isso significa que nesse template:

```html
<h1>{{ userProvidedString }}</h1>
```

se `userProvidedString` contiver:

```js
'<script>alert("Olá")</script>'
```

então ele será convertido em texto de acordo com o HTML seguinte:

```html
&lt;script&gt;alert(&quot;Olá&quot;)&lt;/script&gt;
```

para prevenir a injeção de script. Essa conversão para texto é feita usando APIs nativas do navegador, como `textContent`, então uma vulnerabilidade só pode existir se o navegador em si estiver vulnerável.

### Interligações de Atributo

De maneira similar, interligações dinâmicas de atributos também são automaticamente convertidas em texto. Isso significa que nesse template:

```html
<h1 v-bind:title="userProvidedString">
  oi
</h1>
```

se `userProvidedString` contiver:

```js
'" onclick="alert(\'oi\')'
```

então ele será convertido em texto de acordo com o seguinte HTML:

```html
&quot; onclick=&quot;alert('oi')
```

para prevenir que o fechamento do atributo `title` injete HTML novo e arbitrário. Essa conversão para texto é feita usando APIs nativas do navegador, como `setAttribute`, então uma vulnerabilidade só pode existir se o navegador em si estiver vulnerável.

## Perigos Potenciais

Qualquer aplicação web que permita que conteúdo provido pelo usuário sem sanitização seja executado como HTML, CSS ou JavaScript é potencialmente perigosa, então isso deve ser evitado sempre que possível. No entanto, existem casos em que algum risco é aceitável.

Por exemplo, serviços como CodePen e JSFiddle permitem que conteúdo provido pelo usuário seja executado, mas é em um contexto onde isso é esperado e tratado como sandbox dentro de iframes. Nesses casos quando uma importante funcionalidade inerentemente requer algum nível de vulnerabilidade, está na responsabilidade de seu time pesar a importância da funcionalidade contra os piores casos que a vulnerabilidade habilita.

### Injetando HTML

Como você aprendeu anteriormente, o Vue automaticamente converte conteúdo HTML em texto, prevenindo que você acidentalmente injete um HTML executável em sua aplicação. De qualquer forma, em casos que você saiba que o HTML é confiável, você poderá renderizar explicitamente o conteúdo HTML:

- Usando um template:
  ```html
  <div v-html="userProvidedHtml"></div>
  ```

- Usando uma função de renderização:
  ```js
  h('div', {
    domProps: {
      innerHTML: this.userProvidedHtml
    }
  })
  ```

- Usando uma função de renderização com JSX:
  ```jsx
  <div domPropsInnerHTML={this.userProvidedHtml}></div>
  ```

<p class="tip">Observe que HTML provido pelo usuário nunca será considerado 100% confiável a não ser que esteja em um iframe tratado como sandbox ou em uma parte da aplicação onde apenas o usuário que escreveu esse código será exposto a ele. Adicionalmente, autorizar usuários a escrever seus próprios templates Vue gera perigos similares.</p>

### Injetando URLs

Em uma URL como essa:

```html
<a v-bind:href="userProvidedUrl">
  clique aqui
</a>
```

Existe um potencial problema de segurança se a URL não tiver sido sanitizada para prevenir execução usando `javascript:`. Existem bibliotecas como [sanitize-url](https://www.npmjs.com/package/@braintree/sanitize-url) para nos ajudar com isso, mas observe:

<p class="tip">Se você estiver fazendo sanitização em algum lugar do frontend, você já tem um problema de segurança. URLs providas por usuários sempre devem ser sanitizadas pelo seu backend antes de serem salvas em um banco de dados. Então o problema será evitado em _qualquer_ cliente que esteja conectado à sua API, incluindo aplicações nativas para celular. Também observe que ainda que você sanitize URLs, o Vue não pode ajudá-lo a garantir que elas levarão à destinos seguros.</p>

### Injetando Estilos

Veja este exemplo:

```html
<a
  v-bind:href="sanitizedUrl"
  v-bind:style="userProvidedStyles"
>
  clique aqui
</a>
```

vamos assumir que `sanitizedUrl` foi sanitizada, então é definitivamente uma URL e não um JavaScript. Com a `userProvidedStyles`, usuários maliciosos ainda podem prover CSS para "furto de clique", por ex. estilizando o link como uma caixa transparente acima do botão de "Log in". Então se `https://user-controlled-website.com/` for criado para simular a página de login da sua aplicação, eles podem capturar informação real de login de usuários.

Você deve imaginar que liberando conteúdo provido pelo usuário para um elemento `<style>` criará uma vulnerabilidade ainda maior, dando àquele usuário controle total sobre como estilizar a página inteira. É por isso que o Vue previne renderização de tags de estilo dentro de templates, como abaixo:

```html
<style>{{ userProvidedStyles }}</style>
```

Para manter seus usuários inteiramente protegidos de furto de click, nós recomendamos apenas liberar controle total sobre CSS dentro de um iframe de sandbox. Alternativamente, quando prover ao usuário controle sobre uma interligação de estilo, nós recomendamos usar sua [sintaxe de objeto](class-and-style.html#Sintaxe-de-Objeto) e apenas autorizar o usuário à prover valores para propriedades específicas que sejam seguras para ele controlar, como no exemplo:

```html
<a
  v-bind:href="sanitizedUrl"
  v-bind:style="{
    color: userProvidedColor,
    background: userProvidedBackground
  }"
>
  clique aqui
</a>
```

### Injetando JavaScript

Nós desencorajamos fortemente qualquer renderização de um elemento `<script>` no Vue, pois templates e funções de renderização nunca devem ter efeitos colaterais. No entanto, essa não é a única forma de incluir strings que podem ser processadas como JavaScript em tempo de execução.

Cada elemento HTML tem atributos com valores que aceitam strings de JavaScript, assim como `onclick`, `onfocus`, e `onmouseenter`. Interligar JavaScript provido pelo usuário à qualquer desses atributos de evento é um potencial risco de segurança, então deveria ser evitado.

<p class="tip">Observe que JavaScript provido pelo usuário nunca será considerado 100% confiável a não ser que esteja em um iframe tratado como sandbox ou em uma parte da aplicação onde apenas o usuário que escreveu esse código JavaScript será exposto a ele.</p>

Algumas vezes recebemos avisos de vulnerabilidades sobre como é possível fazer cross-site scripting (XSS) nos templates Vue. Em geral, nós não consideramos esses casos como vulnerabilidades atuais, porque não existe uma maneira prática de proteger desenvolvedores de dois cenários que permitem XSS:

1. O desenvolvedor está explicitamente pedindo ao Vue para renderizar conteúdo provido pelo usuário sem sanitização como templates Vue. Isso é inerentemente inseguro e não existe uma maneira de o Vue saber a origem.

2. O desenvolvedor está montando Vue como uma página HTML inteira em que existe conteúdo renderizado no lado do servidor e conteúdo provido pelo usuário. Isso é fundamentalmente o mesmo problema de \#1, mas às vezes os desenvolvedores podem fazê-lo sem intenção. Isso leva à possíveis vulnerabilidades em que o atacante provê HTML que é seguro enquanto HTML simples mas inseguro como um template Vue. A melhor prática é nunca montar Vue em nós que podem conter conteúdo renderizado no lado do servidor e provido pelo usuário.

## Melhores Práticas

Como regra geral se você permite que conteúdo provido pelo usuário sem sanitização seja executado (seja como HTML, JavaScript, ou até mesmo CSS), você pode tornar você mesmo aberto a ataques. Esse conselho se aplica independentemente de você usar Vue, outro framework, ou até mesmo nenhum framework.

Além das recomendações que fizemos acima para [Perigos Potenciais](#Perigos-Potenciais), nós também recomendamos que você se familiarize com esses recursos:

- [Folha de Dicas para Segurança HTML5](https://html5sec.org/)
- [Folha de Dicas para Prevenção de Cross Site Scripting (XSS) da OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)

Então use o que você aprender para também revisar o código fonte de suas dependências para utilização de padrões potencialmente perigosos, se qualquer uma delas incluir componentes de terceiros ou influenciar no que é renderizado no DOM.

## Coordenação com Backend

Vulnerabilidades de segurança HTTP, tal como falsificação de solicitação _cross-site_ (CSRF/XSRF) e inclusão de script _cross-site_ (XSSI), são primariamente endereçadas no backend, então não são preocupação do Vue. No entanto, ainda assim é uma boa ideia se comunicar com o seu time de backend para entender a melhor forma de interagir com a sua API, por ex. submetendo tokens CSRF na submissão de formulários.

## Renderização no Lado do Servidor (SSR)

Existem algumas preocupações adicionais de segurança quando se utiliza SSR, então tenha certeza de seguir as melhores práticas descritas na [documentação do SSR](https://ssr.vuejs.org/) para evitar vulnerabilidades.
