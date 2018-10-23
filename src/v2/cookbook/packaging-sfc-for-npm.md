---
title: Publicando Componentes no npm
type: cookbook
order: 12
---

## Exemplo Base

Vue components ou componentes vue, por natureza, são feitos para serem reutilizados. E isso é fácil quando o componente é usado apenas em um único aplicativo. Mas como você pode escrever um componente uma vez e usá-lo em vários sites/aplicativos? Talvez a solução mais fácil seja via npm.

Ao empacotar seu componente para ser compartilhado via npm, ele pode ser importado/requerido em um processo de compilação para uso em aplicativos web completos:

```js
import MyComponent from 'my-component';

export default {
  components: {
    MyComponent,
  },
  // rest of the component
}
```

Ou serem usados via `<script>`  tag diretamente no browser:

```html
  <script src="https://unpkg.com/vue"></script>
  <script src="https://unpkg.com/my-component"></script>
  ...
  <my-component></my-component>
  ...
```

Isso não apenas ajuda a evitar componentes serem copiados/colados em toda parte, mas também permite que você devolver um pouco à comunidade Vue!

## Não posso simplesmente compartilhar arquivos `.vue` diretamente?

O Vue já permite que os componentes sejam escritos em único arquivo. Porque um Single File Component(Um único component em arquivo) é somente um único arquivo, você deve perguntar :

> "Por que as pessoas não podem usar meu arquivo `.vue` diretamente? Essa não é a maneira mais simples de compartilhar componentes?"

É verdade, você pode compartilhar arquivos `.vue` diretamente, e qualquer um pode usar um [build em Vue](https://vuejs.org/v2/guide/installation.html#Explanation-of-Different-Builds) contendo o compilador do próprio Vue, podendo utiliza-lo imediatamente. Além disso, a construção do SSR usa a concatenação de strings como uma otimização, portanto, o arquivo `.vue` pode ser preferido neste cenário (consulte [Packaging Components for npm > SSR Usage](#SSR-Usage) para obter mais detalhes). No entanto, isso exclui qualquer pessoa que deseje usar o componente diretamente em um navegador através da tag `<script>`, qualquer um que use uma compilação somente em tempo de execução ou construa processos que não entendam o que fazer com arquivos `.vue`.

Criar adequadamente o seu SFC(Single File Component) para distribuição via npm permite que o seu componente seja compartilhado de uma forma que esteja pronta para uso em qualquer lugar!

## Criando components para o npm

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

<p class="tip"> Ao longo deste documento, são feitas referências ao arquivo package.json listado acima. O arquivo usado nesses exemplos foi gerado manualmente e incluirá a configuração mínima necessária para a discussão/tarefa em questão. É provável que o seu próprio arquivo package.json contenha muito mais do que o listado aqui.</p>

### Como o npm sabe qual versão será veiculada para um browser/build process?

O arquivo package.json usado pelo npm realmente requer apenas uma versão (`main`), mas, como se constata, não estamos limitados a isso. Nós podemos endereçar os casos de uso mais comuns especificando 2 versões adicionais (`module` e `unpkg`), e fornecer acesso ao próprio arquivo `.vue` usando o campo `browser`. Um exemplo package.json seria assim:

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

Quanto ao webpack 2+, o Rollup ou outras ferramentas modernas de compilação, são usadas, eles vão pegar o módulo compilado. Os aplicativos legados usariam a compilação `main` e a compilação `unpkg` pode ser usada diretamente nos navegadores. De fato, o cdn [unpkg] (https://unpkg.com) usa isso automaticamente quando alguém insere o URL do seu módulo em seu serviço!

### Uso do SSR

Você deve ter notado algo interessante - os navegadores não usarão a versão do `browser`. Isso porque, na verdade, esse campo destina-se a permitir que os autores forneçam [dicas para os bundlers](https://github.com/defunctzombie/package-browser-field-spec#spec), que, por sua vez, criam seus próprios pacotes para uso do lado do cliente. Com um pouco de criatividade, esse campo nos permite mapear um alias para o arquivo `.vue` em si. Por exemplo:

```js
import MyComponent from 'my-component/sfc'; // Note the '/sfc'
```

Bundlers compatíveis vêem a definição `browser` em package.json e traduzem requisições para` my-component / sfc` em `my-component / src / my-component.vue`, resultando no arquivo original` .vue` sendo usado . Agora, o processo de SSR pode usar as otimizações de concatenação de strings necessárias para um aumento no desempenho.

<p class="tip"> Nota: Ao usar diretamente os componentes `.vue`, preste atenção em qualquer tipo de pré-processamento requerido pelas tags` script` e `style`. Essas dependências serão repassadas aos usuários. Considere fornecer SFCs 'simples' para manter as coisas o mais leves possível.</p>

### Como eu faço o versionamento do meu component?

Não há necessidade de escrever seu módulo várias vezes. É possível preparar todas as 3 versões do seu módulo em uma etapa, em questão de segundos. O exemplo aqui usa [Rollup](https://rollupjs.org) devido a sua configuração mínima, mas é possível uma configuração semelhante com outras ferramentas de compilação - mais detalhes sobre essa decisão podem ser encontrados [aqui] (https://medium.com/webpack/webpack-and-rollup-o-mesmo-mas-diferente-a41ad427058c). A seção package.json `scripts` pode ser atualizada com uma única entrada para cada destino de compilação, e um script `build` mais genérico que os executa todos em um único passo. O arquivo de exemplo do package.json, agora, se apresenta dessa forma:

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

<p class="tip">Lembre-se, se você tiver um arquivo package.json existente, ele provavelmente conterá muito mais do que este. Isso apenas ilustra um ponto de partida. Além disso, os <i> pacotes </ i> listados em devDependencies (não suas versões) são os requisitos mínimos para rollup para criar as três compilações separadas (umd, es e unpkg) mencionadas. À medida que versões mais novas se tornam disponíveis, elas devem ser atualizadas conforme necessário.</p>

Nossas mudanças no pacote.json estão completas. Em seguida, precisamos de um pequeno wrapper para exportar/instalar automaticamente o SFC real, além de uma configuração mínima do Rollup, e estamos prontos!

### Como é meu componente empacotado?


Dependendo de como seu componente está sendo usado, ele precisa ser exposto como um javascript [CommonJS / UMD](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#c33a) módulo, um módulo [ES6 javascript](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#4f5e), ou no caso de uma tag `<script>`, será automaticamente carregado no Vue via `Vue.use (...)`, por isso está imediatamente disponível para a página. Isso é feito por um simples arquivo wrapper.js que manipula a exportação e a instalação automática do módulo. Esse wrapper, na sua totalidade, é assim:

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

Este arquivo de configuração é um exemplo, contém as configurações mínimas para empacotar seu SFC para npm. Há espaço para personalização, como extrair CSS para um arquivo separado, usando um pré-processador de CSS, uglificando a saída JS, etc.


Além disso, vale a pena observar o `nome` dado o componente aqui. Este é um nome do PascalCase que o componente receberá, e deve corresponder ao nome do `kebab-case` em outras partes desta receita.

### Isso substituirá meu atual processo de desenvolvimento?

A configuração aqui não serve para substituir o processo de desenvolvimento que você usa atualmente. Se você tem atualmente uma configuração de webpack com o módulo de recarga (HMR), continue usando-a! Se você está começando do zero, sinta-se à vontade para instalar o [Vue CLI 3](https://github.com/vuejs/vue-cli/), que lhe dará toda a experiência do HMR:

```bash
vue serve --open src/my-component.vue
```

Em outras palavras, faça todo o seu desenvolvimento da maneira que estiver confortável. As coisas descritas nesta receita são mais como 'retoques' do que um processo completo de desenvolvimento.

## Quando evitar esse padrão

Empacotar SFCs dessa maneira pode não ser uma boa ideia em determinados cenários. Esta receita não entra em detalhes sobre como os próprios componentes são escritos. Alguns componentes podem fornecer efeitos colaterais como diretivas ou estender outras bibliotecas com funcionalidade adicional. Nesses casos, você precisará avaliar se as alterações necessárias a essa receita são muito extensas ou não.

Além disso, preste atenção a quaisquer dependências que seu SFC possa ter. Por exemplo, se você precisar de uma biblioteca de terceiros para classificação ou comunicação com uma API, o Rollup poderá transferir esses pacotes para o código final se não estiver configurado corretamente. Para continuar usando essa receita, você precisaria configurar o Rollup para excluir esses arquivos da saída e atualizar sua documentação para informar seus usuários sobre essas dependências.

## Padrões Alternativos

No momento em que esta receita foi escrita, o Vue CLI 3 estava em beta. Esta versão da CLI vem com um modo de construção `library` embutido, que cria versões CommonJS e UMD de um componente. Isso pode ser adequado para seus casos de uso, embora você ainda precise certificar-se de que seu arquivo package.json aponte para o `main` e` unpkg` corretamente. Além disso, não haverá saída `module` do ES6 a menos que esse recurso seja adicionado ao CLI antes de seu lançamento ou via plugin.

## Agradecimentos

Esta receita é o resultado de uma conversa relâmpago dada por [Mike Dodge](https://twitter.com/mgdodgeycode) no VueConf.us em março de 2018. Ele publicou um utilitário para o npm que irá rapidamente organizar um exemplo de SFC usando este receita. Você pode fazer o download do utilitário [vue-sfc-rollup] (https://www.npmjs.com/package/vue-sfc-rollup), a partir do npm. Você também pode [clonar o repositório](https://github.com/team-innovation/vue-sfc-rollup) e personalizá-lo.
