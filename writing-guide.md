# Guia para Escrita de Documentação do Vue

Escrever documentação é um exercício de empatia. Nós não estamos descrevendo uma realidade objetiva - o código fonte já faz isso. Nosso trabalho é ajudar a moldar o relacionamento entre os usuários e o ecossistema Vue. Esse guia em constante evolução provê algumas regras e recomendações sobre como fazer isso de maneira consistente com o ecossistema Vue.

## Princípios

- **Uma funcionalidade não existe até que esteja documentada.**
- **Respeite a capacidade cognitiva dos usuários (i.e. poder cerebral).** Quando um usuário começa a ler, ele começa com uma certa quantidade de poder cerebral e quando acaba, ele pára de aprender.
  - Capacidade cognitiva é **esgotada rapidamente** por sentenças complexas, onde você tem que aprender mais de um conceito por vez, e abstrair exemplos que não se relacionam diretamente a uma atividade do usuário.
  - Capacidade cognitiva é **esgotada mais lentamente** quando nós ajudamos eles se sentirem consistentemente inteligentes, poderosos, e curiosos. Quebre as coisas em partes fáceis de digerir e se preocupe se o fluxo do documento pode ajudar eles a se manterem nesse estado.
- **Sempre tente ver pela perspectiva do usuário.** Quando a gente entende algo profundamente, começa a se tornar óbvio para nós. Isso é chamado de _caminho do conhecimento_. Para escrever boa documentação, tente relembrar o que você precisou  entender quando estava aprendendo esse conceito. Quais jargões você precisou aprender? O que você entendeu errado? O que tomou muito tempo para realmente entender? Boa documentação atende aos usuários onde eles estão. Algo que pode ajudar é tentar praticar o conceito pessoalmente para alguém antes de escrever.
- **Descreva o _problema_ primeiro, e então a solução.** Antes de mostrar como uma funcionalidade funciona, é importante explicar porque ela existe. Do contrário, os usuários não terão o contexto para entender se essa informação é importante para eles (é um problema que eles estão enfrentando?) ou quais conhecimentos/experiências prévias podem conectar a isso.
- **Enquanto escreve, não tenha medo de fazer perguntas**, _especialmente_ se você tiver medo que sejam perguntas "bestas". Ser vulnerável é difícil, mas é a única forma de nós entendermos completamente o que precisamos explicar.
- **Esteja envolvido nas discussões sobre a funcionalidade.** As melhores APIs surgem do desenvolvimento orientado a documentação, onde nós construímos funcionalidades que são fáceis de explicar, mais do que tentamos encontrar uma maneira de explicar elas depois. Fazer perguntas (especialmente questões "bestas") antecipadamente geralmente ajuda a revelar confusões, inconsistências, e comportamento problemático antes que uma _breaking change_ seja necessária para corrigí-los.

## Organização

- **Instalação/Integração**: Forneça uma visão geral minuciosa sobre como integrar o software a tantos diferentes tipos de projetos quanto necessário.
- **Introdução/_Getting Started_**:
  - Forneça uma visão geral de até 10 minutos sobre os problemas que o projeto resolve e o porque dele existir.
  - Forneça uma visão geral de até 30 minutos sobre os problemas que o projeto resolve e como, incluindo quando e porque usar o projeto e alguns exemplos simples de código. No final, inclua links para a página de Instalação e para o início do Guia Essencial.
- **Guia**: Faça os usuários se sentirem inteligentes, poderosos, e curiosos, então mantenha esse estado para que os usuários mantenham a motivação e a capacidade cognitiva para continuar aprendendo mais. Páginas de guia são feitas para serem lidas sequencialmente, então devem ser ordenadas da maior para a menor relação potencial/esforço.
  - **Essenciais**: Não deveria demorar mais que 5 horas para ler os Essenciais, quanto menor melhor. O seu objetivo é fornecer s 20% do conhecimento que vai ajudar o usuário a lidar com 80% dos casos de uso. Os Essenciais podem fazer link com guias mais avançados e a API, mas, na maioria dos casos, você deveria evitar esses links. Quando eles são fornecidos, você também precisa fornecer um contexto para que os usuários estejam atentos se eles deveriam navegar até esse link em sua primeira leitura. Do contrário, muitos usuários terminarão exaurindo sua capacidade cognitiva acessando links, tentando aprender completamente cada aspecto de uma funcionalidade antes de continuar, e como resultado, nunca terminarão aquela primeira leitura dos Essenciais. Lembre-se que uma leitura suave é mais importante do que ser detalhista. Nós queremos fornecer informações que as pessoas precisam para evitar uma experiência frustrante, mas elas podem sempre voltar e ler mais, ou buscar no Google um problema menos comum que elas estejam enfrentando.
  - **Avançado**: Enquanto os Essenciais ajudam as pessoas a resolverem ~80% dos casos de uso, os guias subsequentes ajudam os usuários chegarem a 95% dos casos de uso, além de informações mais detalhadas sobre funcionalidades não essenciais (por ex. transições, animações), funcionalidades complementares mais complexas (por ex. mixins, diretivas personalizadas), e melhorias na experiência do desenvolvedor (por ex. JSX, plugins). Os restantes 5% de casos de uso são mais específicos, complexos, e/ou propensos a má utilização serão deixados para o livro de receitas e a referência da API, o que pode ser linkado a partir desses guias avançados.
- **Referência/API**: Fornece uma lista completa das funcionalidades, incluindo informação de tipo, descrições sobre o problema que cada uma resolve, exemplos de cada combinação das opções, e links para os guias, livro de receitas, e outros recursos internos fornecendo mais detalhes. Ao contrário de outras páginas, esse não é para ser lido do início ao fim, então uma boa quantidade de detalhes podem ser fornecidos. Essas referências também devem ser mais fáceis de ler rapidamente que os guias, então o formato deveria ser mais próximo a entradas de um dicionário do que ao formato de narrativa dos guias.
- **Migrações**:
  - **Versões**: Quando mudanças importantes são feitas, incluir uma lista completa de mudanças é útil, além de explicação detalhada do porquê uma mudança foi feita e como migrar seus projetos.
  - **De outros projetos**: Como esse software se compara à similares? É importante ajudar os usuários a entenderem quais problemas adicionais nós podemos estar resolvendo ou criando para eles, e até que ponto eles podem transferir o conhecimento que já possuem.
- **Guia de Estilo**: Existem algumas peças chave no desenvolvimento que precisam de decisões, mas não são o principal para a API. O guia de estilo fornece de maneira educada, recomendações baseadas na sua opinião para ajudar a guiar nessas escolhas. Isso não deve ser seguido cegamente, mas pode ajudar os times a economizarem tempo para estarem alinhados nos mínimos detalhes.
- **Livro de Receitas**: Receitas no livro de receitas são escritas com certa suposição sobre a familiaridade com o Vue e seu ecossistema. Cada uma é um documenta altamente estrutura que percorre algumas implementações comuns com detalhes que um desenvolvedor Vue pode encontrar.

## Writing & Grammar

### Style

- **Headings should describe problems**, not solutions. For example, a less effective heading might be "Using props", because it describes a solution. A better heading might be "Passing Data to Child Components with Props", because it provides the context of the problem props solve. Users won't really start paying attention to the explanation of a feature until they have some idea of why/when they'd use it.
- **When you assume knowledge, declare it** at the beginning and link to resources for less common knowledge that you're expecting.
- **Introduce only one new concept at a time whenever possible** (including both text and code examples). Even if many people are able to understand when you introduce more than one, there are also many who will become lost - and even those who don't become lost will have depleted more of their cognitive capacity.
- **Avoid special content blocks for tips and caveats when possible.** It's generally preferable to blend these more naturally into the main content, e.g. by building on examples to demonstrate an edge case.
- **Don't include more than two interwoven tips and caveats per page.** If you find that more than two tips are needed in a page, consider adding a caveats section to address these issues. The guide is meant to be read straight through, and tips and caveats can be overwhelming or distracting to someone trying to understand the base concepts.
- **Avoid appeals to authority** (e.g. "you should do X, because that's a best practice" or "X is best because it gives you full separation of concerns"). Instead, demonstrate with examples the specific human problems caused and/or solved by a pattern.
- **When deciding what to teach first, think of what knowledge will provide the best power/effort ratio.** That means teaching whatever will help users solve the greatest pains or greatest number of problems, with the relatively least effort to learn. This helps learners feel smart, powerful, and curious, so their cognitive capacity will drain more slowly.
- **Unless the context assumes a string template or build system, only write code that works in any environment by the software (e.g. Vue, Vuex, etc).**
- **Show, don't tell.** For example, "To use Vue on a page, you can add this to your HTML" (then show the script tag), instead of "To use Vue on a page, you can add a script element with a src attribute, the value of which should be a link to Vue's compiled source".
- **Almost always avoid humor (for English docs)**, especially sarcasm and pop culture references, as it doesn't translate well across cultures.
- **Never assume a more advanced context than you have to.**
- **In most cases, prefer links between sections of the docs over repeating the same content in multiple sections.** Some repetition in content is unavoidable and even essential for learning. However, too much repetition also makes the docs more difficult to maintain, because a change in the API will require changes in many places and it's easy to miss something. This is a difficult balance to strike.
- **Specific is better than generic.** For example, a `<BlogPost>` component example is better than `<ComponentA>`.
- **Relatable is better than obscure.** For example, a `<BlogPost>` component example is better than `<CurrencyExchangeSettings>`.
- **Be emotionally relevant.** Explanations and examples that relate to something people have experience with and care about will always be more effective.
- **Always prefer simpler, plainer language over complex or jargony language.** For example:
  - "you can use Vue with a script element" instead of "in order to initiate the usage of Vue, one possible option is to actually inject it via a script HTML element"
  - "function that returns a function" instead of "higher order function"
- **Avoid language that invalidate struggle**, such as "easy", "just", "obviously", etc. For reference, see [Words To Avoid in Educational Writing](https://css-tricks.com/words-avoid-educational-writing/).

### Grammar

- **Avoid abbreviations** in writing and code examples (e.g. `attribute` is better than `attr`, `message` is better than `msg`), unless you are specifically referencing an abbreviation in an API (e.g. `$attrs`). Abbreviation symbols included on standard keyboards (e.g. `@`, `#`, `&`) are OK.
- **When referencing a directly following example, use a colon (`:`) to end a sentence**, rather than a period (`.`).
- **Use the Oxford comma** (e.g. "a, b, and c" instead of "a, b and c"). ![Why the Oxford comma is important](https://raw.githubusercontent.com/vuejs/vuejs.org/master/src/images/oxford-comma.jpg)
- **When referencing the name of a project, prioritize the broader conventions of English over internal branding conventions of that project.** For example, "webpack" and "npm" both disregard conventions such as "always start a word at the beginning of a sentence with a capital letter", "project names always use Title Case", and "acronyms are always capitalized". Instead, always write "Webpack and NPM" to provide a more consistent experience in the docs and avoid sentences like "If you don't want to use Vue CLI, you can use webpack or Rollup directly by installing them via npm or Yarn".
- **Use Title Case for headings** - at least for now, since it's what we use through the rest of the docs. There's research suggesting that sentence case (only first word of the heading starts with a capital) is actually superior for legibility and also reduces the cognitive overhead for documentation writers, since they don't have to try to remember whether to capitalize words like "and", "with", and "about".
- **Don't use emojis (except in discussions).** Emojis are cute and friendly, but they can be a distraction in documentation and some emoji even convey  different meanings in different cultures.

## Iteration & Communication

- **Excellence comes from iteration.** First drafts are always bad, but writing them is a vital part of the process. It's extremely difficult to avoid the slow progression of Bad -> OK -> Good -> Great -> Inspiring -> Transcendent.
- **Only wait until something is "Good" before publishing.** The community will help you push it further down the chain.
- **Try not to get defensive when receiving feedback.** Our writing can be very personal to us, but if we get upset with the people who help us make it better, they will either stop giving feedback or start limiting the kind of feedback they give.
- **Proof-read your own work before showing it to others.** If you show someone work with a lot of spelling/grammar mistakes, you'll get feedback about spelling grammar/mistakes instead of more valuable notes about whether the writing is achieving your goals.
- **When you ask people for feedback, tell reviewers what:**
  - **you're trying to do**
  - **your fears are**
  - **balances you're trying to strike**
- **When someone reports a problem, there is almost always a problem**, even if the solution they proposed isn't quite right. Keep asking follow-up questions to learn more.
- People need to feel safe asking questions when contributing/reviewing content. Here's how you can do that:
  - **Thank people for their contributions/reviews, even if you're feeling grumpy.** For example:
    - "Great question!"
    - "Thanks for taking the time to explain. 🙂"
    - "This is actually intentional, but thanks for taking the time to contribute. 😊"
  - **Listen to what people are saying and mirror if you're not sure you're understanding correctly.** This can help validate people's feelings and experiences, while also understanding if *you're* understanding *them* correctly.
  - **Use a lot of positive and empathetic emojis.** It's always better to seem a little strange than mean or impatient.
  - **Kindly communicate rules/boundaries.** If someone behaves in a way that's abusive/inappropriate, respond only with kindness and maturity, but also make it clear that this behavior is not acceptable and what will happen (according to the code of conduct) if they continue behaving poorly.

## Resources

### Software

- [Grammarly](https://www.grammarly.com/): Desktop app and browser extension for checking spelling and grammar (though grammar checking doesn't catch everything and occasionally shows a false positive).
- [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker): An extension for VS Code to help you check spelling within markdown and code examples.

### Books

- [On Writing Well](https://www.amazon.com/Writing-Well-30th-Anniversary-Nonfiction-ebook/dp/B0090RVGW0) (see [popular quotes](https://www.goodreads.com/work/quotes/1139032-on-writing-well-the-classic-guide-to-writing-nonfiction))
- [Bird by Bird](https://www.amazon.com/Bird-Some-Instructions-Writing-Life/dp/0385480016) (see [popular quotes](https://www.goodreads.com/work/quotes/841198-bird-by-bird-some-instructions-on-writing-and-life))
- [Cognitive Load Theory](https://www.amazon.com/Cognitive-Explorations-Instructional-Performance-Technologies/dp/144198125X/)
