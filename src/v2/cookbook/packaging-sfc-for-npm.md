---
title: Publicando Componentes no npm
type: cookbook
order: 12
---

## Exemplo Base

Componentes Vue, por natureza, são feitos para serem reutilizados. E isso é fácil quando o componente é usado apenas em um único aplicativo. Mas, como você pode escrever um componente uma vez e usá-lo em vários _sites_ e aplicativos? Talvez a solução mais fácil seja via npm.

Ao empacotar seu componente para compartilhar via npm, pode ser importado ou requerido em um processo de compilação para uso em aplicativos Web completos:

```js
import MyComponent from 'my-component';

export default {
  components: {
    MyComponent,
  },
  // resto do componente
}
```

Ou mesmo serem usados via `<script>` diretamente no navegador:

```html
  <script src="https://unpkg.com/vue"></script>
  <script src="https://unpkg.com/my-component"></script>
  ...
  <my-component></my-component>
  ...
```

Isso não apenas ajuda a evitar componentes copiados/colados por toda parte, mas também permite que você retribua um pouco à comunidade Vue!

## Não Posso Compartilhar Arquivos `.vue` Diretamente?

Vue permite que os componentes sejam escritos em único arquivo. Já que um componente Single-File é somente um único arquivo, você poderia perguntar:

> "Por que as pessoas não podem usar meu arquivo `.vue` diretamente? Essa não é a maneira mais simples de compartilhar componentes?"

É verdade, você pode compartilhar arquivos `.vue` diretamente, e qualquer um pode usar uma [distribuição do Vue](https://br.vuejs.org/v2/guide/installation.html#Explicacao-das-Distribuicoes) contendo o compilador do próprio Vue, permitindo utiliza-lo imediatamente. Além disso, a construção de SSR usa a concatenação de Strings como uma otimização, portanto, o arquivo `.vue` pode ser preferido neste cenário (consulte [Utilização com SSR](#SSR-Usage) para obter mais detalhes). No entanto, isso exclui qualquer pessoa que deseje usar o componente diretamente em um navegador através da _tag_ `<script>`, qualquer pessoa que use uma compilação somente para tempo de execução, ou qualquer pessoa que utiliza processos de compilação que não entendam o que fazer com arquivos `.vue`.

Criar adequadamente o seu SFC (_Single-File Component_) para distribuição via npm permite que o seu componente seja compartilhado de uma forma que esteja pronto para uso em qualquer lugar!

## Criando Components para npm

Para os propósitos desta seção, assuma a seguinte estrutura de arquivos:

```
package.json
build/
   rollup.config.js
src/
   wrapper.js
   my-component.vue
dist/
```

<p class="tip">Ao longo deste documento, são feitas referências ao arquivo package.json listado acima. O arquivo usado nesses exemplos foi gerado manualmente e incluirá a configuração mínima necessária para a discussão/tarefa em questão. É provável que o seu próprio arquivo package.json contenha muito mais do que o listado aqui.</p>

### Como o npm Sabe Qual a Versão para um Navegador/Empacotador?

O arquivo package.json usado pelo npm realmente requer apenas uma versão (`main`), mas, como se constata, não estamos limitados a isso. Nós podemos endereçar os casos de uso mais comuns especificando 2 versões adicionais (`module` e `unpkg`), e fornecer acesso ao próprio arquivo `.vue` usando o campo `browser`. Um package.json de exemplo seria assim:

```json
{
  "name": "my-component",
  "version": "1.2.3",
  "main": "dist/my-component.umd.js",
  "module": "dist/my-component.esm.js",
  "unpkg": "dist/my-component.min.js",
  "browser": {
    "./sfc": "src/my-component.vue"
  },
  ...
}
```

Quando webpack 2+, Rollup ou outras ferramentas modernas de compilação forem usadas, pegarão a versão `module`. Aplicações legadas usariam a compilação `main`. A compilação `unpkg` poderá ser usada diretamente nos navegadores. De fato, o CDN [unpkg](https://unpkg.com) usa isso quando alguém insere o URL de seu módulo no serviço deles!

### Utilização com SSR

Você deve ter notado algo interessante - os navegadores não usarão a versão `browser`. Isso porque, na verdade, esse campo destina-se a permitir que os autores forneçam [dicas para os empacotadores](https://github.com/defunctzombie/package-browser-field-spec#spec), que, por sua vez, criam seus próprios pacotes para uso do lado do cliente. Com um pouco de criatividade, esse campo nos permite mapear um apelido para o arquivo `.vue` em si. Por exemplo:

```js
import MyComponent from 'my-component/sfc'; // Observe o '/sfc'
```

Empacotadores compatíveis vêem a definição `browser` em package.json e traduzem requisições de `my-component/sfc` para `my-component/src/my-component.vue`, resultando no arquivo original `.vue`. Agora, o processo de renderização no lado do servidor pode usar as otimizações de concatenação de Strings necessárias para otimização de desempenho.

<p class="tip">Nota: Ao usar diretamente os componentes `.vue`, preste atenção em qualquer tipo de pré-processamento requerido pelas _tags_ `script` e `style`. Essas dependências serão repassadas aos usuários. Considere fornecer SFCs 'puros' para manter as coisas o mais leve possível.</p>

### Como Versionar do Componente?

Não há necessidade de escrever seu módulo várias vezes. É possível preparar todas as 3 versões do seu módulo em uma única etapa, em questão de segundos. Este exemplo aqui usa [Rollup](https://rollupjs.org) devido à sua configuração mínima, mas é possível uma configuração semelhante com outras ferramentas de compilação - mais detalhes sobre essa decisão podem ser encontrados [aqui](https://medium.com/webpack/webpack-and-rollup-the-same-but-different-a41ad427058c). A seção package.json `scripts` pode ser atualizada com uma única entrada para cada destino de compilação, bem como um _script_ `build` mais genérico que os executa todos em um único passo. O arquivo de exemplo do package.json, agora, se apresenta dessa forma:

```json
{
  "name": "my-component",
  "version": "1.2.3",
  "main": "dist/my-component.umd.js",
  "module": "dist/my-component.esm.js",
  "unpkg": "dist/my-component.min.js",
  "browser": {
    "./sfc": "src/my-component.vue"
  },
  "scripts": {
    "build": "npm run build:umd & npm run build:es & npm run build:unpkg",
    "build:umd": "rollup --config build/rollup.config.js --format umd --file dist/my-component.umd.js",
    "build:es": "rollup --config build/rollup.config.js --format es --file dist/my-component.esm.js",
    "build:unpkg": "rollup --config build/rollup.config.js --format iife --file dist/my-component.min.js"
  },
  "devDependencies": {
    "rollup": "^1.17.0",
    "rollup-plugin-buble": "^0.19.8",
    "rollup-plugin-commonjs": "^10.0.1",
    "rollup-plugin-vue": "^5.0.1",
    "vue": "^2.6.10",
    "vue-template-compiler": "^2.6.10"
    ...
  },
  ...
}
```

<p class="tip">Lembre-se, se você tiver um arquivo package.json existente, ele provavelmente conterá muito mais do que isto. Apenas ilustramos um ponto de partida. Além disso, os <i>pacotes</i> listados em devDependencies (não suas versões) são os requisitos mínimos para rollup para criar as três compilações separadas (umd, es e unpkg) mencionadas. À medida que versões mais novas se tornam disponíveis, elas devem ser atualizadas conforme necessário.</p>

Nossas mudanças no package.json estão completas. Em seguida, precisamos de um pequeno empacotador para exportar/instalar automaticamente o SFC real, além de uma configuração mínima do Rollup, e estaremos prontos!

### Como Empacoto o Componente?

Dependendo de como seu componente está sendo usado, ele precisa ser exposto como um módulo [CommonJS/UMD](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#c33a), um módulo [JavaScript ES6](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#4f5e) ou, no caso de uma _tag_ `<script>`, será automaticamente carregado no Vue via `Vue.use (...)` para que esteja imediatamente disponível para a página. Isso é feito por um simples arquivo wrapper.js que manipula a exportação e a instalação automática do módulo. Esse wrapper, na sua totalidade, é assim:

```js
// Importa o componente
import component from './my-component.vue';

// Declara a função de instalação executada pelo Vue.use()
export function install(Vue) {
  if (install.installed) return;
  install.installed = true;
  Vue.component('MyComponent', component);
}

// Cria a definição do módulo para Vue.use()
const plugin = {
  install,
};

// Auto-instala quando o Vue é encontrado (no navegador via <script>)
let GlobalVue = null;
if (typeof window !== 'undefined') {
  GlobalVue = window.Vue;
} else if (typeof global !== 'undefined') {
  GlobalVue = global.Vue;
}
if (GlobalVue) {
  GlobalVue.use(plugin);
}

// Para permitir o uso como um módulo exportável (npm/webpack/etc.)
export default component;
```

Observe que a primeira linha importa diretamente seu SFC e a última linha o exporta inalterado. Como indicado pelos comentários no restante do código, este nosso empacotador fornece uma função `install` para o Vue e, em seguida, tenta detectar o Vue no ambiente e instalar automaticamente o componente. Com 90% do trabalho realizado, é hora de correr até o final!

### Como Compilar com o Rollup?

Com a seção package.json `scripts` pronta e o empacotador do SFC no lugar, tudo o que resta é garantir que o Rollup esteja configurado corretamente. Felizmente, isso pode ser feito com um pequeno arquivo rollup.config.js de 16 linhas:

```js
import commonjs from 'rollup-plugin-commonjs'; // Converte módulos CommonJS para ES6
import vue from 'rollup-plugin-vue'; // Manipula arquivos .vue
import buble from 'rollup-plugin-buble'; // Transpila com considerável suporte a navegadores
export default {
  input: 'src/wrapper.js', // Caminho relativo ao package.json
  output: {
    name: 'MyComponent',
    exports: 'named',
  },
  plugins: [
    commonjs(),
    vue({
      css: true, // Dinamicamente injeta CSS como uma tag <style>
      compileTemplate: true, // Explicitamente converte template para função render
    }),
    buble(), // Transpila para ES5
  ],
};
```

Este arquivo de configuração é um exemplo, contém as configurações mínimas para empacotar seu SFC para npm. Há espaço para personalização, como extrair CSS para um arquivo separado, usando um pré-processador CSS, embaralhando a saída JS com um processo de _uglify_, etc.

Além disso, vale a pena observar o `name` dado ao componente ali. Este é um nome PascalCase que o componente receberá, e deve corresponder ao nome `kebab-case` exibido em outras partes desta receita.

### Isso Substituirá Meu Processo de Desenvolvimento?

A configuração aqui não serve para substituir o processo de desenvolvimento que você usa atualmente. Se você tem atualmente uma configuração de webpack com _hot-reloading_, continue usando-a! Se você está começando do zero, sinta-se à vontade para instalar o [Vue CLI 3](https://github.com/vuejs/vue-cli/), que lhe dará toda a experiência de desenvolvimento desejada:

```bash
vue serve --open src/my-component.vue
```

Em outras palavras, faça todo o seu desenvolvimento da maneira que estiver confortável. As coisas descritas nesta receita são mais como 'retoques' para publicação do que um processo completo de desenvolvimento.

## Quando Evitar o Padrão

Empacotar Componentes Single-File desta maneira pode não ser uma boa ideia em determinados cenários. Esta receita não entra em detalhes sobre como os próprios componentes são escritos. Alguns componentes podem fornecer efeitos colaterais, como diretivas, ou estender outras bibliotecas com funcionalidade adicional. Nesses casos, você precisará avaliar se as alterações necessárias a essa receita são muito extensas ou não.

Além disso, preste atenção a quaisquer dependências que seu SFC possa ter. Por exemplo, se você precisar de uma biblioteca de terceiros para classificação ou comunicação com uma API, o Rollup poderá transferir esses pacotes para o código final se não estiver configurado corretamente. Para continuar usando essa receita, você precisaria configurar o Rollup para excluir esses arquivos da saída e atualizar sua documentação para informar seus usuários sobre essas dependências que eles precisarão instalar/importar por conta própria.

## Padrões Alternativos

No momento em que esta receita foi escrita, o Vue CLI 3 estava em _beta_. Esta versão da CLI vem com um modo de construção `library` embutido, que cria versões CommonJS e UMD de um componente. Isso pode ser adequado para seus casos de uso, embora você ainda precise certificar-se de que seu arquivo package.json aponte para `main` e `unpkg` corretamente. Além disso, não haverá saída `module` do ES6 a menos que esse recurso seja adicionado ao CLI antes de seu lançamento, ou via um _plugin_.

## Agradecimentos

Esta receita é resultado de uma palestra relâmpago dada por [Mike Dodge](https://twitter.com/webdevdodge) no VueConf.us, em março de 2018. Ele publicou um utilitário para o npm que irá rapidamente organizar um exemplo de SFC através desta receita. Você pode baixar o utilitário [vue-sfc-rollup](https://www.npmjs.com/package/vue-sfc-rollup) a partir do npm. Você também pode [clonar o repositório](https://github.com/team-innovation/vue-sfc-rollup) e personalizá-lo.
