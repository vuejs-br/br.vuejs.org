---
title: Publicando Componentes no npm
type: cookbook
order: 12
---

<p class="tip">**Nota da Equipe de Tradução**
Este arquivo ainda não foi traduzido! Leia a versão original em inglês a seguir e, se puder, colabore com sua tradução: acesse [nosso projeto no GitHub](https://github.com/vuejs-br/br.vuejs.org/issues), avise que irá contribuir e inicie a tradução. Sua participação é muito importante!</p>

## Exemplo Base

Componentes Vue por natureza têm o objetivo de serem reusados. Isso se torna fácil quando o componente somente é utilizado como uma simples aplicação. Mas agora você pode escrever um componente uma vez e usa-lo em multiplas sites/aplicações? Talvez a solução mais fácil é via npm.

Ao empacotar seu component para ser compartilhado via npm, ele pode ser importado/requerido num processo de construção para ser utilizado em aplicativos web completos:

```js
import MyComponent from 'my-component';

export default {
  components: {
    MyComponent,
  },
  // resto do componente
}
```
Ou mesmo utilizado diretamente no navegador através da tag`<script>`:

```html
  <script src="https://unpkg.com/vue"></script>
  <script src="https://unpkg.com/meu-componente"></script>
  ...
  <my-component></my-component>
  ...
```
Isso não somente ajuda a evitar copiar/colar os compoentes, mas também permite que você retribua a comunidade Vue!

## Não posso simplesmente compartilhar arquivos `.vue` diretamente?

Vue já permite que componentes sejam escritos num arquivo simples. Porque um Componente de Arquivo Único, ou Single File Componente (SFC) já é somente um arquivo, você deve perguntar:

> "Porque as pessoas não usam o meu arquivo `.vue` diretamente? esse não é a maneira mais simples de compartilhar components?"

É verdade, você pode compartilhar arquivos `.vue` diretamente, e todos usando um [Vue build](https://vuejs.org/v2/guide/installation.html#Explanation-ofDifferent-Builds) contendo o compilador Vue pode consumir imediatamente. Também, o construtor SSR usa concatenação de string como uma otimização, então o arquivo `.vue` deve ser preferido nesse cenário (veja [Empacotando componentes para npm > uso SSR](@SSR-Usage) para mais detalhes). Entretanto, isso exclue todos que deseja usar o componente diretamente no navegador via tag `<script>`, todos que usam um construtor somente de execução, ou construir procesos que não entendem o que fazer com arquivos `.vue`.

Empacotando propriamente seu SFC para distribuir através do npm habilita seu componente para ser compartilhado de um modo pronto para ser utilizado em todo lugar!

## Empacotando componentes para npm

Para o propósito dessa seçao, assumiremos a seguinte estrutura:

```
package.json
build/
   rollup.config.js
src/
   wrapper.js
   my-component.vue
dist/
```

<p class="tip">Ao longo deste documento, referencias serão feitas ao arquivo package.json listado abaixo. O arquivo utilizado nesses exemplos são gerados manualmente, e incluirá a configuração mínima necessária para a discussão / tarefa em questão. É provável que o seu próprio arquivo package.json contenha muito mais do que o listado aqui. </p>
  
### Como o npm sabe qual versão será veiculada para um processo de navegador / criação?

O arquivo package.json usado pelo npm realmente requer somente uma versão (`main`), mas como constatado, não estamos limitados a isso. Nós podemos endereçar os mais comuns casos de uso especificando 2 versões adicional (`module` e `unpkg`), e fornecer acesso ao proprio arquivo `.vue` usando o campo` browser`. Um exemplo package.json seria assim:

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

Quando o webpack 2+, Rolar, ou outras ferramentas modernas de compilação são usadas, eles vao pegar na compilação do `module`. Os aplicativos legados usariam a compilação `main` e a compilação` unpkg` pode ser usada diretamente nos navegadores. De fato, o cdn [unpkg] (https://unpkg.com) usa isso automaticamente quando alguém insere a URL do seu módulo em seu serviço!

### Uso do SSR 

Você deve ter notado algo interessante - navegadores não utilizarão a versão `browser`. Isso é poruqe esse campo atualmente serve para permitir que autores informem [hints to bundlers](https://github.com/defunctzombie/package-browser-field-spec#spec) que em troca cria seus próprios pacotes para uso no lado do cliente. Com uma pequena criatividade, esse campo nos permite mapear um alias para o  próprio arquivo `.vue`. Por exemplo:

```js
import MyComponent from 'my-component/sfc'; // Note the '/sfc'
```
Empacotadores compatíveis vêem a definição `browser` em package.json e traduzem requisições para` my-component / sfc` em `my-component / src / my-component.vue`, resultando no uso do arquivo original` .vue`. Agora, o processo de SSR pode usar as otimizações de concatenação de strings necessárias para um aumento no desempenho.

<p class="tip">Nota: Ao usar components `.vue` diretamente, preste atenção em todo tipo de préprocessamento requerido pelas tags `script` e `style`. Essas dependencias serão passads para os usuarios. Considere fornecer SFCs 'simples'('plain') para manter as coisas o mais leves possível.</p>

### Como eu crio para criar multiplas versões do meu componente?

Não há necessidade de escrever multiplas vezes o seu. É possivel preparar todas as 3 versões do seu módulo em somente um passo, numa questão de segundos. O exemplo aqui usa [Rollup](https://rollupjs.org) devido a sua minima configuração, mas configurações semelhantes são possíveis com outras ferrametas de compilação - mais dealhes desse assunto podem ser encontrados [aqui](https://medium.com/webpack/webpack-and-rollup-the-same-but-different-a41ad427058c). A seção `scripts` do package.json pode ser atualizada com uma simples entrada para cada destino de compilação, e uma um script `build` mais genérico que roda todos de uma vez só. O arquivo package.json de exemplo agora se parece com isso:

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
    "rollup": "^0.57.1",
    "rollup-plugin-buble": "^0.19.2",
    "rollup-plugin-vue": "^3.0.0",
    "vue": "^2.5.16",
    "vue-template-compiler": "^2.5.16",
    ...
  },
  ...
}
```

<p class="tip">Lembre-se, se você tiver um arquivo package.json existente, ele provavelmente conterá muito mais do que este. Isso apenas ilustra um ponto de partida. Além disso, os <i> pacotes </i> listados em devDependencies (não suas versões) são os requisitos mínimos para rollup para criar as três compilações separadas (umd, es e unpkg) mencionadas. À medida que versões mais novas se tornam disponíveis, elas devem ser atualizadas conforme necessário.</p>

Nossas mudanças no package.json estão completas. Em seguida, precisamos de um pequeno wrapper para exportar / instalar automaticamente o SFC real, além de uma configuração minimas para Rollup, e estamos prontos!

### Como é meu componente empacotado?

Dependendo de como seu componente está sendo usado, ele precisa ser exposto como [CommonJS/UMD](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#c33a) módulo javascript, um módulo [ES6 javascript](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#4f5e), ou no caso de uma tag `<script>`, ele será automaticamente carregfado dentro do Vue através do `Vue.use(...)` então é automaticamente disponível para a página. Isso é feito por um simples arquivo wrapper.js que manipula a exportação e a instalação automática do módulo Esse wrapper, na sua totalidade, é assim:

```js
// Import vue component
import component from './my-component.vue';

// Declare install function executed by Vue.use()
export function install(Vue) {
	if (install.installed) return;
	install.installed = true;
	Vue.component('MyComponent', component);
}

// Create module definition for Vue.use()
const plugin = {
	install,
};

// Auto-install when vue is found (eg. in browser via <script> tag)
let GlobalVue = null;
if (typeof window !== 'undefined') {
	GlobalVue = window.Vue;
} else if (typeof global !== 'undefined') {
	GlobalVue = global.Vue;
}
if (GlobalVue) {
	GlobalVue.use(plugin);
}

// To allow use as module (npm/webpack/etc.) export component
export default component;
```
Observe que a primeira linha importa diretamente seu SFC e a última linha o exporta inalterada. Como indicado pelos comentários no restante do código, o wrapper fornece uma função `install` para o Vue, em seguida, tenta detectar o Vue e instalar automaticamente o componente. Com 90% do trabalho realizado, é hora de correr até o final!

### Como eu configuro o build do Rollup?

Com a seção package.json `scripts` pronta e o wrapper SFC no lugar, tudo o que resta é garantir que o Rollup esteja configurado corretamente. Felizmente, isso pode ser feito com um pequeno arquivo rollup.config.js de 16 linhas:

```js
import vue from 'rollup-plugin-vue'; // Handle .vue SFC files
import buble from 'rollup-plugin-buble'; // Transpile/polyfill with reasonable browser support
export default {
    input: 'src/wrapper.js', // Path relative to package.json
    output: {
        name: 'MyComponent',
        exports: 'named',
    },
    plugins: [
        vue({
            css: true, // Dynamically inject css as a <style> tag
            compileTemplate: true, // Explicitly convert template to render function
        }),
        buble(), // Transpile to ES5
    ],
};
```
Este arquivo de configuração de amostra contém as configurações mínimas para empacotar seu SFC para npm. Há espaço para personalização, como extrair CSS para um arquivo separado, usando um pré-processador de CSS, uglificando a saída JS, etc.

Além disso, vale a pena observar o `nome` dado o componente aqui. Este é um nome PascalCase que o componente receberá, e deve corresponder ao nome kebab-case usado em outras partes desta receita.

### Isso irá substituir meu processo de desenvolvimento atual?

A configuração aqui não serve para substituir o processo de desenvolvimento que você usa atualmente. Se você tem atualmente uma configuração de webpack com o módulo de recarga (HMR), continue usando-a! Se você está começando do zero, fique à vontade para instalar [Vue CLI 3](https://github.com/vuejs/vue-cli/), que lhe dará toda a experiência do HMR livre de configuração:

```bash
vue serve --open src/my-component.vue
```

Em outras palavras, faça todo o seu desenvolvimento da maneira que você estiver confortável. As coisas descritas nesta receita são mais como 'retoques' do que um processo completo de desenvolvimento.

## Quando evitar esse padrão

Empacotar SFCs dessa maneira pode não ser uma boa ideia em certos cenários. Essa receita não entra em detalhes de como os componentes são escritos. Em alguns componentes podem ocorrer efeitos adversos como directivas, ou herdade outras bibliotecas com funcionalidades adicionais. Nesses casos, você precisará avaliar se as alterações necessárias a essa receita são muito extensas ou não.

Além disso, preste atenção a quaisquer dependências que seu SFC possa ter. Por exemplo, se você precisar de uma biblioteca de terceiros para classificação ou comunicação com uma API, o Rollup poderá transferir esses pacotes para o código final se não estiver configurado corretamente. Para continuar usando essa receita, você precisaria configurar o Rollup para excluir esses arquivos da saída e atualizar sua documentação para informar seus usuários sobre essas dependências.

## Padrões alternativos

No momento em que esta receita foi escrita, o Vue CLI 3 estava em beta. Esta versão da CLI vem com um modo de construção `library` embutido, que cria versões CommonJS e UMD de um componente. Isso pode ser adequado para seus casos de uso, embora você ainda precise certificar-se de que seu arquivo package.json aponte para o `main` e` unpkg` corretamente. Além disso, não haverá saída `module` do ES6 a menos que esse recurso seja adicionado ao CLI antes de seu lançamento ou via plugin.

## Agradecimentos

Esta receita é o resultado de uma lightning talk dada por [Mike Dodge] (https://twitter.com/mgdodgeycode) no VueConf.us em março de 2018. Ele publicou um utilitário para o npm que irá rapidamente organizar um exemplo (scaffold) de SFC usando este receita. Você pode fazer o download do utilitário [vue-sfc-rollup] (https://www.npmjs.com/package/vue-sfc-rollup), a partir do npm. Você também pode [clonar o repositório] (https://github.com/team-innovation/vue-sfc-rollup) e personalizá-lo.
