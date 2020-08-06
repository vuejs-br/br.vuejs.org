# Vue Docs Writing Guide

Writing documentation is an exercise in empathy. We're not describing an objective reality - the source code already does that. Our job is to help shape the relationship between users and the Vue ecosystem. This ever-evolving guide provides some rules and recommendations on how to do that consistently within the Vue ecosystem.

## Principles

- **A feature doesn't exist until it's well documented.**
- **Respect users' cognitive capacity (i.e. brain power).** When a user starts reading, they begin with a certain amount of limited brain power and when they run out, they stop learning.
  - Cognitive capacity is **depleted faster** by complex sentences, having to learn more than one concept at a time, and abstract examples that don't directly relate to a user's work.
  - Cognitive capacity is **depleted more slowly** when we help them feel consistently smart, powerful, and curious. Breaking things down into digestible pieces and minding the flow of the document can help keep them in this state.
- **Always try to see from the user's perspective.** When we understand something thoroughly, it becomes obvious to us. This is called _the curse of knowledge_. In order to write good documentation, try to remember what you first needed to know when learning this concept. What jargon did you need to learn? What did you misunderstand? What took a long time to really grasp? Good documentation meets users where they are. It can be helpful to practice explaining the concept to people in person before
- **Describe the _problem_ first, then the solution.** Before showing how a feature works, it's important to explain why it exists. Otherwise, users won't have the context to know if this information is important to them (is it a problem they experience?) or what prior knowledge/experience to connect it to.
- **While writing, don't be afraid to ask questions**, _especially_ if you're afraid they might be "dumb". Being vulnerable is hard, but it's the only way for us to more fully understand what we need to explain.
- **Be involved in feature discussions.** The best APIs come from documentation-driven development, where we build features that are easy to explain, rather than trying to figure out how to explain them later. Asking questions (especially "dumb" questions) earlier often helps reveal confusions, inconsistencies, and problematic behavior before a breaking change would be required to fix them.

## Organization

- **Installation/Integration**: Provide a thorough overview of how to integrate the software into as many different kinds of projects as necessary.
- **Introduction/Getting Started**:
  - Provide a less than 10 minute overview of the problems the project solves and why it exists.
  - Provide a less than 30 minute overview of the problems the project solves and how, including when and why to use the project and some simple code examples. At the end, link to both to Installation page and the beginning of the Essentials Guide.
- **Guide**: Make users feel smart, powerful, and curious, then maintain this state so that users maintain the motivation and cognitive capacity to keep learning more. Guide pages are meant to be read sequentially, so should generally be ordered from the highest to lowest power/effort ratio.
  - **Essentials**: It should take no longer than 5 hours to read the Essentials, though shorter is better. Its goal is to provide the 20% of knowledge that will help users handle 80% of use cases. Essentials can link to more advanced guides and the API, though, in most cases, you should avoid such links. When they are provided, you need also provide a context so users are aware if they should follow this link on their first reading. Otherwise, many users end up exhausting their cognitive capacity link-hopping, trying to fully learn every aspect of a feature before moving on, and as a result, never finish that first read-through of the Essentials. Remember that a smooth read is more important than being thorough. We want to give people the information they need to avoid a frustrating experience, but they can always come back and read further, or Google a less common problem when they encounter it.
  - **Advanced**: While the Essentials helps people handle ~80% of use cases, subsequent guides help get users to 95% of use cases, plus more detailed information on non-essential features (e.g. transitions, animations), more complex convenience features (e.g. mixins, custom directives), and dev experience improvements (e.g. JSX, plugins). The final 5% of use cases that are more niche, complex, and/or prone to abuse will be left to the cookbook and API reference, which can be linked to from these advanced guides.
- **Reference/API**: Provide a complete list of features, including type information, descriptions of the problem each solves, examples of every combination of options, and links to guides, cookbook recipes, and other internal resources providing more detail. Unlike other pages, this one is not meant to be read top-to-bottom, so plenty of detail can be provided. These references must also be more easily skimmable than the guides, so the format should be closer to dictionary entries than the story-telling format of the guides.
- **Migrations**:
  - **Versions**: When important changes are made, it's useful to include a full list of changes, including a detailed explanation of why the change was made and how to migrate their projects.
  - **From other projects**: How does this software compare to similar software? This is important to help users understand what additional problems we might solve or create for them, and to what extent they can transfer knowledge they already have.
- **Style Guide**: There are necessarily some key pieces in development that need a decision, but are not core to the API. The style guide provides educated, opinionated recommendations to help guide these decisions. They shouldn't be followed blindly, but can help teams save time by being aligned on smaller details.
- **Cookbook**: Recipes in the cookbook are written with some assumption of familiarity with Vue and its ecosystem. Each is a highly structured document that walks through some common implementation details that a Vue dev might encounter.

## Escrita e Gramática

### Estilo

- **Cabeçalhos devem descrever problemas**, não soluções. Um exemplo, de cabeçalho menos efetivo, poderia ser "Use props", pois descreve a solução. Um cabeçalho melhor poderia ser "Enviando dados para um componente filho com Props", pois há um melhor contexto do que o props resolve. Usuários não começam prestando atenção realmente na explicação da funcionalidade, antes deles terem alguma ideia do porque/quando eles vão usá-la.
- **Ao assumir o conhecimento, declare-o** no início, e conecte-o a recursos para um conhecimento menos comum que você espera.
- **Introduza apenas um conceito por vez sempre que possível** (Tanto o texto quanto o código de exemplo). Mesmo que você introduza mais de um exemplo, alguns irão entender, enquanto outros ficarão perdidos - e mesmo aqueles que não ficarem perdidos, terão sua capacidade cognitiva esgotada mais rapidamente.
- **Evite blocos com conteúdo especial para dicas e advertências.** No geral, é preferível misturá-los de uma forma mais natural dentro do conteúdo principal, ex.: construindo exemplos para demonstrar um caso pontual.
- **Não inclua mais de duas dicas e advertências entrelaçadas.** Se mais de duas dicas forem necessárias na página, considere adicionar uma seção de advertências para resolver esses problemas. A intenção deste guia é ser lido diretamente, e as dicas e advertências podem sobrecarregar ou distrair uma pessoa que está tentando entender os conceitos básicos.
- **Evite apelar à autoridade** (por ex. "você deveria fazer assim, pois essa é a melhor prática" ou "fazer assim é melhor, pois te dará uma maior separação das partes"). Ao invés disso, demonstre com exemplos mais humanos da causa do problema e/ou a solução por um padrão.
- **Quando decidir o que ensinar, primeiro, pense no conhecimento que será mostrado e a melhor relação esforço/energia** Isso significa que ensinar qualquer coisa, irá ajudar o usuário a resolver grandes incômodos ou inúmeros problemas, com um pouco menos de esforço para aprender. Isso ajuda o aprendiz a se sentir esperto, confiante e curioso, então, sua capacidade cognitiva será drenada mais lentamente.
- **A não ser que o contexto assuma que há uma string template ou um sistema de produção, escreva um código que funcione em qualquer ambiente pelo software (por ex.: Vue, Vuex, etc.).**
- **Mostre, não diga.** Por exemplo, "Para usar Vue em uma página, você pode adicionar isso ao seu HTML" (Então mostre uma tag de script), ao invés de, "Para usar Vue em uma página, você pode adicionar um elemento de script com um atributo src, o valor de cada pode ser um link para código compilado do Vue".
- **Quase sempre, evite humor (Para documentos em inglês)**, especialmente referências da cultura pop e sarcasmo, visto que uma tradução não ocorre bem entre culturas.
- **Não assuma um contexto mais avançado do que o necessário.**
- **Em muitos casos, prefira link's entre seções do documento do que repetir o mesmo conteúdo em várias partes** Algumas repetições no conteúdo são inevitáveis e até essenciais para o aprendizado. No entanto, muita repetição também torna o documento mais difícil de manter, pois uma alteração na API acarretará em uma mudança em vários lugares, o que torna fácil perder alguma coisa. Essa é uma balança difícil de se acertar.
- **Específico é melhor que genêrico.** Por exemplo, o componente a seguir `<BlogPost>` é melhor do que `<ComponentA>`.
- **Legibilidade é melhor que obscuridade.** Por exemplo, o componente a `<BlogPost>` é melhor do que `<CurrencyExchangeSettings>`.
- **Seja emocionalmente relevante.** Explicações e exemplos que se aproximam a algo que as pessoas têm experiência e se importam, serão sempre mais efetivas.
- **Sempre prefira simplicidade e uma linguagem clara ao invés de complexidade e jargões de linguagem.** Por exemplo:
  - "Você pode usar Vue com um elemento de script" ao invés de "a fim de iniciar o uso do Vue, uma opção possível, é injetar-lo através de um elemento de script HTML"
  - "Uma função que retorna uma função" ao invés de "função de ordem superior"
- **Evite palavras que invalidam o esforço**, como "fácil", "apenas", "obviamente", etc. para refência, veja [Palavras a serem evitadas em uma escrita educacional](https://css-tricks.com/words-avoid-educational-writing/).

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
