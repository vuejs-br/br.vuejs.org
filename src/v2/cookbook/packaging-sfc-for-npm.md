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

Vue já permite que componentes sejam escritos num arquivo simples. Porque um Componente de Arquivo simples, ou Single File Componente (SFC) já é somente um arquivo, você deve perguntar:

> "Porque as pessoas não usam o meu arquivo `.vue` diretamente? esse não é a maneira mais simples de compartilhar components?"

É verdade, você pode compartilhar arquivos `.vue` diretamente, e todos usando um [Vue build](https://vuejs.org/v2/guide/installation.html#Explanation-ofDifferent-Builds) contendo o compilador Vue pode consumir imediatamente. Também, o construtor SSR usa concatenação de string como uma otimização, então o arquivo `.vue` deve ser preferido nesse cenário (veja [Empacotando componentes para npm > uso SSR](@SSR-Usage) para mais detalhes). Entretanto, isso exclue todos que deseja usar o componente diretamente no navegador via tag `<script>`, todos que usam um construtor somente de execução, ou construir procesos que não entendem o que fazer com arquivos `.vue`.

Empacotando propriamente seu SFC para distribuir através do npm habilita seu componente para ser compartilhado de um modo pronto para ser utilizado em todo lugar!

## Empacotando componentes para npm

Para o propósito dessa secçao, assumiremos a seguinte estrutura:

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
--------------------------------------------------- 
The package.json file used by npm really only requires one version (`main`), but as it turns out, we aren't limited to that. We can address the most common use cases by specifying 2 additional versions (`module` and `unpkg`), and provide access to the `.vue` file itself using the `browser` field. A sample package.json would look like this:

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

When webpack 2+, Rollup, or other modern build tools are used, they will pick up on the `module` build. Legacy applications would use the `main` build, and the `unpkg` build can be used directly in browsers. In fact, the [unpkg](https://unpkg.com) cdn automatically uses this when someone enters the URL for your module into their service!

### SSR Usage

You might have noticed something interesting - browsers aren't going to be using the `browser` version. That's because this field is actually intended to allow authors to provide [hints to bundlers](https://github.com/defunctzombie/package-browser-field-spec#spec) which in turn create their own packages for client side use. With a little creativity, this field allows us to map an alias to the `.vue` file itself. For example:

```js
import MyComponent from 'my-component/sfc'; // Note the '/sfc'
```

Compatible bundlers see the `browser` definition in package.json and translate requests for `my-component/sfc` into `my-component/src/my-component.vue`, resulting in the original `.vue` file being used instead. Now the SSR process can use the string concatenation optimizations it needs to for a boost in performance.

<p class="tip">Note: When using `.vue` components directly, pay attention to any type of pre-processing required by `script` and `style` tags. These dependencies will be passed on to users. Consider providing 'plain' SFCs to keep things as light as possible.</p>

### How do I make multiple versions of my component?

There is no need to write your module multiple times. It is possible to prepare all 3 versions of your module in one step, in a matter of seconds. The example here uses [Rollup](https://rollupjs.org) due to its minimal configuration, but similar configuration is possible with other build tools - more details on this decision can be found [here](https://medium.com/webpack/webpack-and-rollup-the-same-but-different-a41ad427058c). The package.json `scripts` section can be updated with a single entry for each build target, and a more generic `build` script that runs them all in one pass. The sample package.json file now looks like this:

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

<p class="tip">Remember, if you have an existing package.json file, it will likely contain a lot more than this one does. This merely illustrates a starting point. Also, the <i>packages</i> listed in devDependencies (not their versions) are the minimum requirements for rollup to create the three separate builds (umd, es, and unpkg) mentioned. As newer versions become available, they should be updated as necessary.</p>

Our changes to package.json are complete. Next, we need a small wrapper to export/auto-install the actual SFC, plus a mimimal Rollup configuration, and we're set!

### What does my packaged component look like?

Depending on how your component is being used, it needs to be exposed as either a [CommonJS/UMD](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#c33a) javascript module, an [ES6 javascript](https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc#4f5e) module, or in the case of a `<script>` tag, it will be automatically loaded into Vue via `Vue.use(...)` so it's immediately available to the page. This is accomplished by a simple wrapper.js file which handles the module export and auto-install. That wrapper, in its entirety, looks like this:

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

Notice the first line directly imports your SFC, and the last line exports it unchanged. As indicated by the comments in the rest of the code, the wrapper provides an `install` function for Vue, then attempts to detect Vue and automatically install the component. With 90% of the work done, it's time to sprint to the finish!

### How do I configure the Rollup build?

With the package.json `scripts` section ready and the SFC wrapper in place, all that is left is to ensure Rollup is properly configured. Fortunately, this can be done with a small 16 line rollup.config.js file:

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

This sample config file contains the minimum settings to package your SFC for npm. There is room for customization, such as extracting CSS to a separate file, using a CSS preprocessor, uglifying the JS output, etc.

Also, it is worth noting the `name` given the component here. This is a PascalCase name that the component will be given, and should correspond with the kebab-case name used elsewhere throughout this recipe.

### Will this replace my current development process?

The configuration here is not meant to replace the development process that you currently use. If you currently have a webpack setup with hot module reloading (HMR), keep using it! If you're starting from scratch, feel free to install [Vue CLI 3](https://github.com/vuejs/vue-cli/), which will give you the whole HMR experience config free:

```bash
vue serve --open src/my-component.vue
```

In other words, do all of your development in whatever way you are comfortable. The things outlined in this recipe are more like 'finishing touches' than a full dev process.

## When to Avoid this Pattern

Packaging SFCs in this manner might not be a good idea in certain scenarios. This recipe doesn't go into detail on how the components themselves are written. Some components might provide side effects like directives, or extend other libraries with additional functionality. In those cases, you will need to evaluate whether or not the changes required to this recipe are too extensive.

In addition, pay attention to any dependencies that your SFC might have. For example, if you require a third party library for sorting or communication with an API, Rollup might roll those packages into the final code if not properly configured. To continue using this recipe, you would need to configure Rollup to exclude those files from the output, then update your documentation to inform your users about these dependencies.

## Alternative Patterns

At the time this recipe was written, Vue CLI 3 was itself in beta. This version of the CLI comes with a built-in `library` build mode, which creates CommonJS and UMD versions of a component. This might be adequate for your use cases, though you will still need to make sure your package.json file points to `main` and `unpkg` properly. Also, there will be no ES6 `module` output unless that capability is added to the CLI before its release or via plugin.

## Acknowledgements

This recipe is the result of a lightning talk given by [Mike Dodge](https://twitter.com/mgdodgeycode) at VueConf.us in March 2018. He has published a utility to npm which will quickly scaffold a sample SFC using this recipe. You can download the utility, [vue-sfc-rollup](https://www.npmjs.com/package/vue-sfc-rollup), from npm. You can also [clone the repo](https://github.com/team-innovation/vue-sfc-rollup) and customize it.
