---
title: Introdução
type: cookbook
order: 0
---

## The Cookbook vs the Guide

Como o cookbook é diferente do guia? Por que isto é necessário?

* **Ênfase no Foco**: No guia, estamos essencialmente contando uma história. Cada seção baseia-se e assume o conhecimento de cada seção anterior. No cookbook, cada exemplo pode e deve funcionar sozinho. Isso significa que os exemplos podem se concentrar em um aspecto específico do Vue, em vez de fornecer uma visão geral.

* **Ênfase na Complexidade**: Para evitar fazer um guia longo demais, tentamos incluir apenas os exemplos mais simples possíveis para te ajudar a entender cada recurso. Então vamos prosseguir. No cookbook, podemos incluir exemplos mais complexos, combinando recursos de formas interessantes. Cada exemplo pode também ser tão longo e detalhado quanto necessário, a fim de explorar completamente todas as possibilidades.

* **Ensinando JavaScript**: No guia, assumimos pelo menos um conhecimento intermediário com o JavaScript ES5. Por exemplo, não iremos explicar como o `Array.prototype.filter` funciona em uma propriedade computada que filtra uma lista. No cookbook, entretanto, os recursos essenciais do JavaScript (incluindo ES6/2015+) podem ser explorados e explicados no contexto de como eles nos ajudam a criar melhores aplicações Vue.

 * **Explorando o Ecossistema**: Para recursos avançados, assumimos algum conhecimento do ecossistema. Por exemplo, se você quer usar componentes single-file no Webpack, não explicamos como configurar as partes "non-Vue" da configuração do Webpack. No cookbook, temos espaço para explorar estas bibliotecas do ecossistema com mais profundidade - pelo menos na proporção que seja universalmente útil para desenvolvedores Vue.

## Cookbook Contributions

### What we're looking for

The Cookbook gives developers examples to work off of that both cover common or interesting use cases, and also progressively explain more complex detail. Our goal is to move beyond a simple introductory example, and demonstrate concepts that are more widely applicable, as well as some caveats to the approach.

If you're interested in contributing, please initiate collaboration by filing an issue under the tag **cookbook idea** with your concept so that we can help guide you to a successful pull request. After your idea has been approved, please follow the template below as much as possible. Some sections are required, and some are optional. Following the numerical order is strongly suggested, but not required.

Exemplos devem geralmente:

> * Resolver um problema comum e específico
> * Começar com o exemplo mais simples possível
> * Introduzir complexidades, uma de cada vez
> * Ter link para outros documentos, em vez de reexplicar conceitos
> * Descrever o problema, em vez de assumir a familiaridade
> * Explicar o processo, em vez de explicar só o resultado final
> * Explicar os prós e contras de sua estratégia, inclusive quando ela é ou não é apropriada
> * Mencionar soluções alternativas, se relevantes, mas deixar explorações aprofundadas para um exemplo em separado.

We request that you follow the template below. We understand, however, that there are times when you may necessarily need to deviate for clarity or flow. Either way, all recipes should at some point discuss the nuance of the choice made using this pattern, preferably in the form of the alternative patterns section.

### Base Example

_required_

1.  Articulate the problem in a sentence or two.
2.  Explain the simplest possible solution in a sentence or two.
3.  Show a small code sample.
4.  Explain what this accomplishes in a sentence.

### Details about the Value

_required_

1.  Address common questions that one might have while looking at the example. (Blockquotes are great for this)
2.  Show examples of common missteps and how they can be avoided.
3.  Show very simple code samples of good and bad patterns.
4.  Discuss why this may be a compelling pattern. Links for reference are not required but encouraged.

### Real-World Example

_required_

Demonstrate the code that would power a common or interesting use case, either by:

1.  Walking through a few terse examples of setup, or
2.  Embedding a codepen/jsfiddle example

If you choose to do the latter, you should still talk through what it is and does.

### Additional Context

_optional_

It's extremely helpful to write a bit about this pattern, where else it would apply, why it works well, and run through a bit of code as you do so or give people further reading materials here.

### When To Avoid This Pattern

_optional_

This section is not required, but heavily recommended. It won't make sense to write it for something very simple such as toggling classes based on state change, but for more advanced patterns like mixins it's vital. The answer to most questions about development is ["It depends!"](https://codepen.io/rachsmith/pen/YweZbG), this section embraces that. Here, we'll take an honest look at when the pattern is useful and when it should be avoided, or when something else makes more sense.

### Alternative Patterns

_required_

This section is required when you've provided the section above about avoidance. It's important to explore other methods so that people told that something is an antipattern in certain situations are not left wondering. In doing so, consider that the web is a big tent and that many people have different codebase structures and are solving different goals. Is the app large or small? Are they integrating Vue into an existing project, or are they building from scratch? Are their users only trying to achieve one goal or many? Is there a lot of asynchronous data? All of these concerns will impact alternative implementations. A good cookbook recipe gives developers this context.

## Thank you

It takes time to contribute to documentation, and if you spend the time to submit a PR to this section of our docs, you do so with our gratitude.
