---
title: Introdução
type: cookbook
order: 0
---

## O Cookbook vs o Guia

Como o cookbook é diferente do guia? Por que isto é necessário?

* **Ênfase no Foco**: No guia, estamos essencialmente contando uma história. Cada seção baseia-se e assume o conhecimento de cada seção anterior. No cookbook, cada exemplo pode e deve funcionar sozinho. Isso significa que os exemplos podem se concentrar em um aspecto específico do Vue, em vez de fornecer uma visão geral.

* **Ênfase na Complexidade**: Para evitar fazer um guia longo demais, tentamos incluir apenas os exemplos mais simples possíveis para te ajudar a entender cada recurso. Então vamos prosseguir. No cookbook, podemos incluir exemplos mais complexos, combinando recursos de formas interessantes. Cada exemplo pode também ser tão longo e detalhado quanto necessário, a fim de explorar completamente todas as possibilidades.

* **Ensinando JavaScript**: No guia, assumimos pelo menos um conhecimento intermediário com o JavaScript ES5. Por exemplo, não iremos explicar como o `Array.prototype.filter` funciona em uma propriedade computada que filtra uma lista. No cookbook, entretanto, os recursos essenciais do JavaScript (incluindo ES6/2015+) podem ser explorados e explicados no contexto de como eles nos ajudam a criar melhores aplicações Vue.

 * **Explorando o Ecossistema**: Para recursos avançados, assumimos algum conhecimento do ecossistema. Por exemplo, se você quer usar componentes single-file no Webpack, não explicamos como configurar as partes "non-Vue" da configuração do Webpack. No cookbook, temos espaço para explorar estas bibliotecas do ecossistema com mais profundidade - pelo menos na proporção que seja universalmente útil para desenvolvedores Vue.

## Contribuições do Cookbook 

### O que estamos procurando

O Cookbook fornece aos desenvolvedores, tanto exemplos comuns, quanto casos de usos interessantes, e também pode explicar progressivamente detalhes mais complexos. Nosso objetivo é ir além de um simples exemplo introdutório e demonstrar conceitos que são mais amplamente aplicáveis, bem como algumas ressalvas à abordagem utilizada.

Se você está interessado em contribuir, por favor inicie a colaboração preenchendo uma *issue* com a tag **cookbook idea** com uma explicação (em inglês), para que possamos te ajudar a realizar com êxito o *pull request*. Depois que sua ideia for aprovada, por favor siga o modelo abaixo tanto quanto possível. Algumas seções são necessárias e algumas são opcionais. Sugerimos veementemente que siga a ordem numérica, mas não é obrigatório.

Exemplos devem geralmente:

> * Resolver um problema comum e específico
> * Começar com o exemplo mais simples possível
> * Introduzir complexidades, uma de cada vez
> * Ter link para outros documentos, em vez de reexplicar conceitos
> * Descrever o problema, em vez de assumir a familiaridade
> * Explicar o processo, em vez de explicar só o resultado final
> * Explicar os prós e contras de sua estratégia, inclusive quando ela é ou não é apropriada
> * Mencionar soluções alternativas, se relevantes, mas deixar explorações aprofundadas para um exemplo em separado.

Solicitamos que você siga o modelo abaixo. Entendemos, entretanto, que há momentos que você pode necessariamente precisar se desviar para alcançar objetividade e clareza. De qualquer modo, todos os exemplos devem, em algum ponto, discutir a escolha feita usando esse padrão, de preferência na forma da seção de padrões alternativos.

### Exemplo Base

_obrigatório_

1.  Articular o problema em uma ou duas sentenças. 
2.  Explicar a solução mais simples possível em uma ou duas sentenças.
3.  Mostrar uma pequena amostra do código.
4.  Explicar qual o objetivo disso em uma sentença.

### Detalhes sobre a Importância

_obrigatório_

1.  Abordar dúvidas comuns que alguém possa ter enquanto visualiza o exemplo. (A tag *Blockquote* é ótima para isso)
2.  Mostrar exemplos de erros comuns e como eles podem ser evitados.
3.  Mostrar de forma simplificada exemplos de códigos bons e ruins.
4.  Discutir o porquê do padrão utilizado ser o ideal. Links de referência não são obrigatórios, mas incentivados.

### Exemplo Real 

_obrigatório_

Demonstrar que o código pode ser um caso de uso comum ou interessante, através de:

1.  Através de alguns exemplos sucintos de configuração, ou
2.  Incorporando um exemplo no codepen/jsfiddle 

Se você escolher fazer o último, você ainda deve explicar o que ele faz.

### Contexto Adicional

_opcional_

É extremamente útil escrever um pouco sobre este padrão. Por exemplo, onde ele se aplica, por que ele funciona bem e percorrer um pouco do código ou fornecer aos leitores um pouco mais de material de leitura.

### Quando Evitar Este Padrão

_opcional_

Esta seção não é obrigatória, mas é muito recomendada. Não fará sentido escrevê-la para algo muito simples como alternar classes com base na mudança de estado, mas para padrões mais avançados, como mixins, é fundamental. A resposta para a maioria das perguntas sobre desenvolvimento é ["Depende!"](https://codepen.io/rachsmith/pen/YweZbG). Aqui, vamos analisar quando o padrão é útil e quando deve ser evitado, ou quando algo faz mais sentido.

### Padrões Alternativos

_obrigatório_

Esta seção é necessária quando você usa a seção acima. É importante explorar outros métodos. Então, se as pessoas acharem que algo não deve ser usado em certas situações, elas não irão ficar na dúvida. Ao fazer isso, considere que a web é um grande lugar em que muitas pessoas têm diferentes tipos de código, e estão resolvendo problemas diferentes. A aplicação é grande ou pequena? Está integrando o Vue em um projeto existente ou está sendo construída do zero? Seus usuários estão apenas tentando alcançar um objetivo ou vários? Existe muitos dados assíncronos? Todas essas preocupações irão afetar diferentes implementações. Um bom exemplo do cookbook dá aos desenvolvedores este contexto.

## Obrigado

É preciso tempo para contribuir com a documentação. Então, se você usar o seu tempo para enviar um PR para esta seção de nossos documentos, faça isso com nossa gratidão.
