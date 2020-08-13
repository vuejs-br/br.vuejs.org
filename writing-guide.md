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

## Escrita e Gramática

### Modelo

- **Cabeçalhos (titulos) devem descrever problemas**, não soluções. Por exemplo, um cabeçalho menos efetivo seria usando "props", porque isso descreve uma solução. Um jeito melhor para um cabeçalho seria "Passando Dados com Props", isso inclui um contexto para possíveis soluções. Usuários não se atentam para explicação antes de terem ideia do que se trata e quando poderá usar isso.
- **Quando você souber, diga**  no começo com links ou fontes para que seja de conhecimento comum.
- **Apresente apenas um conceito por vez, sempre que possível** (incluindo código e texto). Mesmo se muitas pessoas forem capaz de entender o que apresentou, muitas outras também não conseguirão entender e, mesmo aqueles que não ficaram perdidos, poderão perder bastante tempo tentando entender sua lógica.
- **Evite avisos e observações sempre que possível.** É mais viável criar exemplos com casos específicos.
- **Evite utlizar mais que duas dicas ou advertências por página..** Se você considera necessário adicionar mais que duas dicas ou avisos na página, considere adicionar uma sessão para evidenciar os erros. O guia é feito para ser rápido e direto, dicas e advêrtencias podem acabar distraindo ou confundindo o usuário logo nos conceitos básicos. 
- **Evite soar aturotário.** Exemplo: "Você deve usar X, porque isso é uma boa prática". " X é melhor porque da a você uma melhor divisão de tarefas." Ao invés desses exemplos, demonstre estudos especificando artigos ou várias tarefas resolvidas, assim monstando o porquê usar X método.
- **Quando for ensinar algo, tenha em mente quais conceitos apresentam melhor eficácia e eficiência.** Isso quer dizer que, os melhores metodos são os práticos e rápidos que demandem menos esforços para aprender. Isso ajuda os iniciantes a se sentirem entusiasmados, inteligentes, e motivados, dessa forma, seu desgaste se torna lento.
- **A menos que o contexto necessite de um modelo de string ou algo especifico, apenas escreva codigos que sejam multiplataforma. Ex: Vuem Vuex, etc**
- **Não fale, mostre !** Por exemplo, "Para usar a página do Vue, você pode adicionar isso ao HTML ( então, moste o script), ao invés de: "Para usar a página do Vue, você pode adicionar um script com um atributo src, o resultado deve retornar um link para o compilador do Vue."
- **Evite, ao máximo, humor (para documentos em inglês)**, especialmente sarcasmo, piadas geográficas ou culturais. Quando é traduzido pode não saor bem em outras culturas.
- **Nunca tente fazer algo que não está ao seu alcance.**
- **Em vários casos, prefira links entre as sessões dos docs de forma que evite a repetição desnecessária do mesmo conteúdo.**  Algumas repetições são invevitáveis e, às vezes, essenciais para o aprendizado. No entando, quanto mais repetições, mais complicado fica a documentação para atualizar, porque uma pequena mudança no API requer mudanças em vários outros locais, fácil de esquecer algo. Isso é algo dificil de conciliar.
- **Seja bastante especéfico, tente ser menos genérico !.** Exemplo, um `<BlogPost>` é um exemplo melhor que `<ComponentA>`.
- **Descrição é o melhor caminho para o esclarecimento** Por exemplo, um <link> tem explicação melhor que <a `<BlogPost>` component example is better than `<DetalhesSobreMoedas>`.
- **Seja compreensível**.  Explicações e exemplos que relatam alguma experiência de vida ou demonstra sensibilidade sobre o assunto será sempre mais efetivo. 
- **Sempre dê preferência a uma linguagem simples e de fácil entendimento, evitando jargõe.** Exemplo:
  - "Você pode usar Vue com um script" ao invés de "Com a intenção de fazer uso do Vue, uma das opções cabíveis a ser tomada é a introdução de um script através da reconfiguração do codígo fonte, implementando um script."
  - "Uma função que retorne outra função" ao invés de "função de ordem superior".
- **Evite palavras que causam desmotivação**, como "fácil", "apenas", "obviamente", etc. Para referência, veja [Words To Avoid in Educational Writing](https://css-tricks.com/words-avoid-educational-writing/).

### Gramática

- **Evite abreviações** Na escrita e no código (ex. `atributo` melhor que `atrb`, 'mensagem' melhor que `msg`), caso esteja sendo especifico a uma referência, um API (`$attrs`) - por exemplo-. Abreviações com simbolos, inclusos no teclado padrão ( `@`, `#`, `&`) são plausivéis. 
- **Quando fizer referência direta a um exemplo, use dois pontos (`:`) para terminar uma sentença**, ao invés do ponto final. (`.`).
- **Use virgulas para separar elementos.** (ex. "a, b, e c" instead of "a, b e c"). ![Why the Oxford comma is important](https://raw.githubusercontent.com/vuejs/vuejs.org/master/src/images/oxford-comma.jpg)
- **Quando for fazer referência a algum projeto, priorize usar o modelo de escrita Inglês.** Exemplo, "webpack" e "npm", abombos disconsideram as regras ("Sempre começar com letras maiúsculas", "projetos devem começar com letras maiúsculas", e "siglas sempre em maisculas"). Ao contrário, utilize sempre "WebPack e NPM" para deixar mais consistente na documentação e evitar frases do tipo: "Se você não quiser usar o Vue CLI, pode usar o werbpack ou Rollup instalando pelo npm ou Yarn."
- **Utilize caixa alta no cabeçalho (título)** - Até o momento, desde que nós fazemos isso em toda a documentação. Há uma procura com diferença entre maiúscula e minúsculas (apenas a primeiras letras vão em caixa alta - maiúsculo- ex: Guia Para Documentação) além de ser bastante legivel, reduz a sobrecarga cognitiva dos escritores de documentação, uma vez que eles não irão lembrar de colocar as palavras "e", "com", and "sobre" em maiúsculo.
- **Não use emojis (a não ser em discussões).** Emnojis são legais e amigaveis, mas pode ser uma distração na documentação e pode haver vários significados a depender da região.

## Interação e Comunicação

- **Prática leva a perfeição** Os primeiros rascunhos sempre serão ruins, mas faz parte do processo. É bastante dificil evitar a prograssão do Ruim > Legal > Bom > Perfeito > Inspirador > Inalcançável
- **Espere até que algo esteja "bom" para publicar.** A comunidade pode ajudar a dificultar as coisas.
- **Tente não ficar na defensiva quando receber um feedback** Nosso código pode ser bastante pessoal para nós, mas se nós ficarmos com raiva com quem quer nos ajudar, deixar o código melhor, eles irão parar de nos dar  feedback ou limitar o que dizer a você.
- **Revise seu trabalho antes de mostrar aos outros.** Se você postar um trabalho com vários erros de ortografia, você irá receber mais feedbacks a respeito da ortografia do que sobre o código.
- **Quando você pedir um feedback deixe claro que:**
  - **você está tentando fazer**
  - **Seus medos são**
  - **O que você está tentado alcançar**
- **Quando alguém reporta um erro, há quase sempre um erro**, mesmo se a solução não seja a correta. Continue fazendo perguntas para aprender mais.
- Pessoas precisam se sentir seguras para fazer perguntas e contribuir. Aqui está como você pode fazer isso:

  - **Obrigado as pessoas que contribuiram ou revisaram, mesmo os grosseiros.** Exemplo:
    - "Boa pergunta!"
    - "Obrigado por dispor do seu tempo para explicar. 🙂"
    - "Na verdade isso é intencional, mas obrigado pelo seu tempo e por contribuir. 😊"
  - **Ouça o que as pessoas tem a dizer e se pergunte se você tem certeza se está entendendo corretamente.** Isso ajuda as pessoas a terem certeza a respeito do que está sentindo e suas experiências, enquanto você compreende se está entendo eles corretamente.
  - **Use bastante emojis positivos e empáticos.** Isso sempre parecerá estranho, mas é melhor do que aparentar ser maldoso ou impaciente.
  - **Estabeleça regras e limites** Se alguém for abusivo/inapropriado, responda apenas com maturidade e bondade, mas você precisa deixar claro que isso não é aceitável e, se isso continuar, medidas serão tomadas de acordo com as regras de contuta.

## Recursos

### Software

- [Grammarly](https://www.grammarly.com/): Aplicativo Desktop e extensão do navegador para verificar ortografia e gramática (embora a verificação gramatical não capte tudo e, ocasionalmente, mostre um falso postivo).

- [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker): Uma extensão para o VS Code para ajudá-lo a verificar a ortografia nos exemplos de marcações (*markdown*) e códigos.

### Livros

- [On Writing Well](https://www.amazon.com/Writing-Well-30th-Anniversary-Nonfiction-ebook/dp/B0090RVGW0) (ver [citações populares](https://www.goodreads.com/work/quotes/1139032-on-writing-well-the-classic-guide-to-writing-nonfiction))
- [Bird by Bird](https://www.amazon.com/Bird-Some-Instructions-Writing-Life/dp/0385480016) (ver [citações populares](https://www.goodreads.com/work/quotes/841198-bird-by-bird-some-instructions-on-writing-and-life))
- [Cognitive Load Theory](https://www.amazon.com/Cognitive-Explorations-Instructional-Performance-Technologies/dp/144198125X/)
