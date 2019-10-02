---
title: Introdução
type: cookbook
order: 0
---

## Livro de Receitas vs. Guia

Como o livro de receitas (_cookbook_) é diferente do guia? Por que ele é necessário?

* **Ênfase no Foco**: No guia, estamos essencialmente contando uma história. Cada seção baseia-se e assume conhecimento de cada seção anterior. No livro de receitas, cada exemplo pode e deve funcionar sozinho. Isso significa que os exemplos podem se concentrar em um aspecto específico do Vue, em vez de fornecer visão geral.

* **Ênfase na Complexidade**: Para evitar fazer um guia longo demais, tentamos incluir apenas os exemplos mais simples possíveis, para te ajudar a entender cada recurso. Então passamos para o próximo assunto. No livro de receitas, podemos incluir exemplos mais complexos, combinando recursos de formas interessantes. Cada exemplo pode também ser tão longo e detalhado quanto necessário, a fim de explorar completamente todas as possibilidades.

* **Ensinando JavaScript**: No guia, assumimos um conhecimento intermediário com o JavaScript ES5. Por exemplo, não explicaríamos como `Array.prototype.filter` funciona em um dado computado que filtra uma lista. No livro de receitas, os recursos essenciais do JavaScript (incluindo ES6/2015+) podem ser explorados e explicados no contexto de como nos ajudam a criar melhores aplicações Vue.

* **Explorando o Ecossistema**: Para recursos avançados, assumimos que você já tenha algum conhecimento do ecossistema. Por exemplo, se você quer usar componentes _single-file_ no Webpack, não explicamos como configurar as partes "não-Vue" na configuração do Webpack. No livro de receitas, temos espaço para explorar estas bibliotecas do ecossistema com mais profundidade - pelo menos até onde seja universalmente útil para desenvolvedores Vue.

<p class="tip">Com todas essas diferenças, observe que o livro de receitas ainda _não_ é um manual passo-a-passo. Para a maioria de seu conteúdo, espera-se que você tenha uma compreensão básica de conceitos como HTML, CSS, JavaScript, npm/yarn, etc.</p>

## Fazendo Contribuições

### O Que Procuramos?

O livro de receitas fornece aos desenvolvedores desde exemplos comuns a casos de uso interessantes. Também pode explicar progressivamente, em detalhes, coisas complexas. Nosso objetivo é ir além de um simples exemplo introdutório e demonstrar conceitos que são amplamente aplicáveis, bem como algumas ressalvas à abordagem utilizada.

Se você está interessado em contribuir na versão em português, siga as instruções existentes no [README](https://github.com/vuejs-br/br.vuejs.org/blob/master/README.md) assim como para o restante da documentação. Se você está interessado em contribuir diretamente em inglês, por favor, inicie a colaboração preenchendo uma _issue_ no [respositório principal](https://github.com/vuejs/vuejs.org) com a _tag_ **cookbook idea** e uma explicação (obviamente, em inglês), para que possamos te ajudar a realizar com êxito o _pull request_. Depois que sua ideia for aprovada, por favor, siga o modelo abaixo tanto quanto possível. Algumas seções são necessárias e algumas são opcionais. Sugerimos veementemente que siga a ordem numérica, apesar de não ser obrigatório.

Os exemplos devem geralmente:

> * Resolver um problema comum e específico
> * Começar com o exemplo mais simples possível
> * Introduzir complexidades, uma de cada vez
> * Ter _links_ para outros documentos, em vez de reexplicar conceitos
> * Descrever o problema, em vez de assumir a familiaridade
> * Explicar o processo, em vez de explicar só o resultado final
> * Explicar prós e contras de sua estratégia, inclusive quando ela não é apropriada
> * Mencionar soluções alternativas, se relevantes, mas sem se aprofundar

Solicitamos que você siga o modelo abaixo. Entendemos, entretanto, que há momentos que você pode necessariamente precisar se desviar para alcançar objetividade e clareza. De qualquer modo, todos os exemplos devem, em algum ponto, discutir a escolha feita usando esse padrão, de preferência na forma da seção de [padrões alternativos](#Padroes-Alternativos).

### Exemplo Base

_obrigatório_

1.  Articular o problema em uma ou duas sentenças.
2.  Explicar a solução mais simples possível em uma ou duas sentenças.
3.  Mostrar uma pequena amostra do código.
4.  Explicar qual o objetivo disso em uma sentença.

### Detalhes da Importância

_obrigatório_

1.  Abordar dúvidas comuns que alguém possa ter enquanto visualiza o exemplo. (`blockquote` é ótimo para isso)
2.  Mostrar exemplos de erros comuns e como eles podem ser evitados.
3.  Mostrar, de forma simplificada, exemplos de códigos bons e ruins.
4.  Discutir o porquê do padrão utilizado ser o ideal. Referências são incentivadas.

### Exemplo do Mundo Real

_obrigatório_

Demonstrar que o código pode ser um caso de uso comum ou interessante, através de:

1.  Alguns exemplos sucintos de configuração, ou
2.  Incorporando um exemplo no _codepen_/_jsfiddle_

Se você escolher fazer o último, ainda assim deve explicar o que ele faz.

### Notas Adicionais

_opcional_

É extremamente útil escrever um pouco sobre este padrão. Por exemplo, onde ele se aplica, por que ele funciona bem e percorrer um pouco do código ou fornecer aos leitores um pouco mais de material de leitura.

### Quando Evitar o Padrão

_opcional_

Esta seção não é obrigatória, mas é muito recomendada. Não fará sentido escrevê-la para algo muito simples, como alternar classes com base na mudança de estado. Mas, para padrões mais avançados, como _mixins_, é fundamental. A resposta para a maioria das perguntas sobre desenvolvimento é ["Depende!"](https://codepen.io/rachsmith/pen/YweZbG). Aqui, vamos aplicar um olhar honesto sobre quando o padrão é útil e quando deve ser evitado, ou quando algo faz mais sentido.

### Padrões Alternativos

_obrigatório_

Esta seção é necessária quando você usa a seção acima. É importante explorar outros métodos. Então, se as pessoas acharem que algo não deve ser usado em certas situações, elas não ficarão na dúvida. Ao fazer isso, considere que a Web é um grande local em que muitas pessoas têm diferentes tipos de código, e estão resolvendo problemas diferentes. A aplicação é grande ou pequena? Está integrando o Vue em um projeto existente ou está sendo construído do zero? Seus usuários estão apenas tentando alcançar um objetivo ou vários? Existem muitos dados assíncronos? Todas essas preocupações irão afetar diferentes implementações. Um bom exemplo do livro de receitas dá aos desenvolvedores o contexto da solução.

## Obrigado

É preciso tempo para contribuir com a documentação. Então, se você usar o seu tempo para enviar um _pull request_ para esta seção de nossos documentos, faça isso com toda nossa gratidão.
