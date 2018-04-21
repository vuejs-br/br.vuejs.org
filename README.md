# br.vuejs.org

Este é o repositório oficial da tradução em português brasileiro do _site_ [vuejs.org](http://www.vuejs.org/). O _site_ foi construído com [hexo](http://hexo.io/) e o conteúdo escrito em formato _Markdown_ na pasta `src`. _Pull requests_ são bem-vindos!

## Desenvolvendo

```
$ npm install
$ npm start # servidor executando em http://localhost:4000
```

## Publicando

O site é automaticamente publicado quando _commits_ chegam em `master`, via [Netlify](https://www.netlify.com/).

Este processo é disparado somente por um grupo seleto de contribuidores que podem atualizar o _branch_ `master`, a fim de manter a organização. As contribuições são recebidas na forma de _pull requests_ e, quando aceitas, o _deploy_ automático ocorre.


If you are the maintainer of a community translation fork and would like to deploy via Netlify instead of GitHub pages, please ping @yyx990803 in an issue to request a Netlify team membership and DNS update.

## On Translations

Translation for this documentation project are currently maintained in separate repositories forked from this original one.

### French

French translation is maintained by Vuejs-FR.

* Translation Repo - [/vuejs-fr/vuejs.org](https://github.com/vuejs-fr/vuejs.org)

### Italian

* Translation Repo - [/vuejs/it.vuejs.org](https://github.com/vuejs/it.vuejs.org)

### Japanese

Japanese translation is maintained by [Vue.js japan user group](https://github.com/vuejs-jp)

* Translation Repo - [/vuejs/jp.vuejs.org](https://github.com/vuejs/jp.vuejs.org)
* Primary maintainer - [kazupon](https://github.com/kazupon)
* Secondary Maintainers:
    * [re-fort](https://github.com/re-fort)
    * [potato4d](https://github.com/potato4d)

### Korean

Korean translation is maintained by [Vue.js Korean User group](https://github.com/vuejs-kr).

* Translation Repo - [/vuejs-kr/kr.vuejs.org](https://github.com/vuejs-kr/kr.vuejs.org)
* Primary maintainer - [ChangJoo Park](https://github.com/ChangJoo-Park)

### Mandarin

* Translation Repo - [/vuejs/cn.vuejs.org](https://github.com/vuejs/cn.vuejs.org)

### Persian (Farsi)

Persian translation is maintained by VueJS-fa.

* Translation Repo - [/vuejs-fa/fa.vuejs.org](https://github.com/vuejs-fa/fa.vuejs.org)
* Primary maintainer - [Pooya Parsa](https://github.com/pi0)

### Português-Br

Português-Br translation is maintained by [Vuejs-Br](https://github.com/vuejs-br).

* Translation Repo - [/vuejs-br/br.vuejs.org](https://github.com/vuejs-br/br.vuejs.org)

### Russian

Russian translation is maintained by Translation Gang.

* Translation Repo - [/translation-gang/ru.vuejs.org](https://github.com/translation-gang/ru.vuejs.org)
* Primary maintainer - [Grigoriy Beziuk](https://gbezyuk.github.io)

### Spanish

Spanish translation is maintained by VueJS-ES.

* Translation Repo - [/vuejs-es/vuejs.org](https://github.com/vuejs-es/vuejs.org)

### Vietnamese

Vietnamese translation is maintained by [Vue.js Vietnam User group](https://github.com/vuejs-vn/).

* Translation Repo: [/vuejs-vn/vuejs.org](https://github.com/vuejs-vn/vuejs.org)
* Primary maintainer - [phanan](https://github.com/phanan)

### Want to help with the translation?

If you feel okay with translating sorta alone, you can fork the repo, create a "work-in-progress" issue to inform others that you're doing the translation, and go for it.

If you are more of a team player, Translation Gang might be for you. Let us know somehow that you're ready to join this international open-source translators community. Feel free to contact [Grigoriy Beziuk](https://gbezyuk.github.io) or anybody else from [the team](https://github.com/orgs/translation-gang/people).

And thank you in advance ;)

## Como colaborar?

Problemas pontuais nas traduções já realizadas, como erros de grafia ou frases confusas, podem ser reportados apenas enviando _issues_ neste repositório. Deixe claro sobre qual página se refere o problema e, preferencialmente, ofereça uma sugestão para a correção desejada.

Se você se sente à vontade para traduzir/revisar conteúdos por conta, os passos são:

- Verifique o que está em aberto nas _issues_, ou sugira algo que encontrar;
- Faça um _fork_ deste _repo_ para sua própria conta;
- Crie um _issue_ neste _repo_ para informar o que está fazendo;
- Faça as traduções/revisões nos arquivos que se propôs, usando o editor que quiser;
- Ao finalizar, faça um _pull request_ **com a descrição do _commit_ em inglês**;
- Não se esqueça de obter as alterações mais recentes antes de recomeçar o processo.

Caso não consiga terminar por completo algum arquivo, mas queira enviar o trabalho parcial, deixe isso claro na _issue_ de seu trabalho após a submissão, para informar quais problemas persistem no arquivo enviado.

## Considerações para padronização

Para uma documentação padronizada, seguem recomendações do que fazer:

- Traduzir os comentários dos códigos-fonte;
- Traduzir textos informativos dos códigos-fonte, por exemplo: `<div id="level-1">Nível 1</div>`;
- Seguir maiúsculas e minúsculas conforme o original em inglês sempre que possível;
- Utilizar alguma extensão para ortografia e gramática, para evitar que erros deste tipo;
- Usar _itálico_ em expressões sem tradução (por exemplo, _view layer_);
- Quando for submeter, sempre escreva os comentários do _commit_ em inglês.

E algumas recomendações do que **não** fazer:

- Não traduzir nomes de variáveis, métodos, dados, _ids_, classes etc. nos códigos-fonte.
- Não grafar em itálico tipos de dados ou valores primitivos;
- Não faça _pull request_ diretamente para `vuejs/master`, a tradução deve passar por esse repositório.
- Não faça _pull_ diretamente de `vuejs/master`, apenas deste repositório para seu _fork_ pessoal.

### Traduções padronizadas

Alguns termos recorrentes no guia devem ser traduzidos sempre da mesma maneira, desde que a tradução não atrapalhe o contexto da frase:

- *Bundle* = Pacote
- *Debug* = Depuração
- *Handling* = Manipulação
- *Event Listening* = Escuta de Eventos
- *Render Function* = Função de Renderização
- *Computed Properties* = Dados Computados
- *Single-File Components* = Componentes Single-File
- *Custom Elements* = Elementos Personalizados
- *Performance* = Desempenho
- *Watchers* = Observadores
- *Under the hood* = Em seu interior
- *Server-Side Rendering* = Renderização no Lado do Servidor
- *Hooks* = Gatilhos
- *Bind* = Vínculo (ou Interligação)

### Termos não traduzidos

Atualmente recomenda-se que estes termos sejam mantidos em inglês:

- _getter_
- _setter_
- _standalone_
- _runtime_
- _store_
- _scaffolding_
- _loader_
- _loop_
- _template_
- _wrapper_
- _hot-reload_
- true
- false
- Number
- String
- Boolean
- Array

## Como se envolver com a comunidade?

Se você não chegou até aqui para colaborar, mas sim para pedir ajuda sobre alguma coisa da documentação oficial do Vue, a Comunidade Brasileira criou [uma lista](https://github.com/vuejs-br/comunidades) com todos locais oficialmente reconhecidos. Saiba que a comunidade Vue é muito receptiva (no Brasil e no mundo), então não se acanhe para se envolver mais.

## Estou com dúvidas sobre o Vue, onde posso perguntar

Nós temos o nosso fórum em pt-br sobre o Vue, basta [clicar aqui](https://github.com/vuejs-br/forum/). Este fórum está no próprio GitHub, e usamos o gerenciamento de Issues como um fórum.

