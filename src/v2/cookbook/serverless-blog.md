---
title: Criar um Blog com ButterCMS
type: cookbook
order: 5
---

## Introdução

Então, você concluiu o seu site com Vue, parabéns! Mas agora você quer adicionar um _blog_ que rapidamente se encaixe ao seu site, e quer evitar ter um servidor a mais apenas para hospedar uma instância do WordPress (ou qualquer outro gerenciador de conteúdo com um banco de dados apenas para este fim). Você apenas quer que seja possível adicionar alguns componentes Vue destinados ao _blog_, algumas rotas e ter tudo isso funcionando, certo? O que você está procurando é um _blog_ que seja inteiramente construído através de uma _API_, que você possa consumir diretamente a partir da sua aplicação Vue. Esse tutorial vai lhe ensinar exatamente como fazer isso, vamos lá!

Nós vamos construir rapidamente um _blog_ utilizando um sistema de gerenciador de conteúdo, ou _CMS_ (_Content Management System_), com Vue. Será utilizado o [ButterCMS](https://buttercms.com/), um _CMS_ que nos permite utilizar uma interface gráfica e integrar o conteúdo que queremos com um aplicativo Vue. Você pode utilizar o ButterCMS para projetos Vue novos ou que já existam.

![Painel de Controle Butter](https://user-images.githubusercontent.com/160873/36677285-648798e4-1ad3-11e8-9454-d22fca8280b7.png "Painel de Controle Butter")

## Instalando

Execute o seguinte comando:

```bash
npm install buttercms --save
```

O ButterCMS também pode ser carregado através do _CDN_:

```html
<script src="https://cdnjs.buttercms.com/buttercms-1.1.0.min.js"></script>
```

## Início Rápido

Configure o _token_ da sua _API_:

```javascript
var butter = require('buttercms')('seu_token_da_api');
```

Usando _ES6_:

```javascript
import Butter from 'buttercms';
const butter = Butter('seu_token_da_api');
```

Usando _CDN_:

```html
<script src="https://cdnjs.buttercms.com/buttercms-1.1.0.min.js"></script>
<script>
  var butter = Butter('seu_token_da_api');
</script>
```

Importe o ButterCMS em qualquer componente que você queira utilizá-lo. Então execute no _console_:

```javascript
butter.post.list({page: 1, page_size: 10}).then(function(response) {
  console.log(response)
})
```

Essa requisição à _API_ obtém as postagens do seu _blog_. A sua conta vem com uma postagem de exemplo que você verá na resposta da requisição.

## Exibindo as Postagens

Para exibir as postagens, vamos criar uma rota `/blog` (usando o Vue Router) na nossa aplicação e adquirir as postagens do _blog_ a partir da _API_ do ButterCMS, assim como a rota `/blog/:slug` exibirá as postagens individuais.

Veja [a documentação](https://buttercms.com/docs/api/?javascript#blog-posts) do ButterCMS para mais informações, assim como, por exemplo, filtrar postagens por categoria ou autor. A resposta da requisição também inclui alguns metadados que usaremos para paginação.

`router/index.js:`

```javascript
import Vue from 'vue'
import Router from 'vue-router'
import BlogHome from '@/components/BlogHome'
import BlogPost from '@/components/BlogPost'

Vue.use(Router)

export default new Router({
  mode: 'history',
  routes: [
    {
      path: '/blog/',
      name: 'blog-home',
      component: BlogHome
    },
    {
      path: '/blog/:slug',
      name: 'blog-post',
      component: BlogPost
    }
  ]
})
```

Em seguida, crie `components/BlogHome.vue` que vai ser a página inicial do seu _blog_ e listará as últimas postagens dele.

```html
<script>
  import { butter } from '@/buttercms'
  export default {
    name: 'blog-home',
    data() {
      return {
        page_title: 'Blog',
        posts: []
      }
    },
    methods: {
      getPosts() {
        butter.post.list({
          page: 1,
          page_size: 10
        }).then(res => {
          this.posts = res.data.data
        })
      }
    },
    created() {
      this.getPosts()
    }
  }
</script>

<template>
  <div id="blog-home">
      <h1>{{ page_title }}</h1>
      <!-- Cria `v-for` e aplica um `key`, usando uma combinação do slug e index. -->
      <div
        v-for="(post,index) in posts"
        :key="post.slug + '_' + index">
        <router-link :to="'/blog/' + post.slug">
          <article class="media">
            <figure>
              <!-- Vincula results usando um `:` -->
              <!-- Usa um `v-if`/`else` caso seja `featured_image` -->
              <img
                v-if="post.featured_image"
                :src="post.featured_image"
                alt="">
              <img
                v-else
                src="http://via.placeholder.com/250x250"
                alt="">
            </figure>
            <h2>{{ post.title }}</h2>
            <p>{{ post.summary }}</p>
          </article>
        </router-link>
      </div>
  </div>
</template>
```

Veja o resultado (note que adicionamos o _CSS_ do [Bulma](http://bulma.io/)):

![Lista de Postagens do ButterCMS](https://user-images.githubusercontent.com/160873/36868500-1b22e374-1d5e-11e8-82a0-20c8dc312716.png)

Agora vamos criar `components/BlogPost.vue`, a página de uma postagem do _blog_.

```html
<script>
  import { butter } from '@/buttercms'
  export default {
    name: 'blog-post',
    data() {
      return {
        post: {}
      }
    },
    methods: {
      getPost() {
        butter.post.retrieve(this.$route.params.slug)
          .then(res => {
            this.post = res.data
          }).catch(res => {
            console.log(res)
          })
      }
    },
    created() {
      this.getPost()
    }
  }
</script>

<template>
  <div id="blog-post">
    <h1>{{ post.data.title }}</h1>
    <h4>{{ post.data.author.first_name }} {{ post.data.author.last_name }}</h4>
    <div v-html="post.data.body"></div>

    <router-link
      v-if="post.meta.previous_post"
      :to="/blog/ + post.meta.previous_post.slug"
      class="button">
      {{ post.meta.previous_post.title }}
    </router-link>
    <router-link
      v-if="post.meta.next_post"
      :to="/blog/ + post.meta.next_post.slug"
      class="button">
      {{ post.meta.next_post.title }}
    </router-link>
  </div>
</template>
```

Aqui vai uma prévia de como ficou:

![Detalhes da Postagem do ButterCMS](https://user-images.githubusercontent.com/160873/36868506-218c86b6-1d5e-11e8-8691-0409d91366d6.png)

Agora nossa aplicação está trazendo todas as postagens do _blog_ e podemos navegar através delas de forma individual. Contudo, os botões que exibem a postagem anterior e a seguinte não estão funcionando.

É importante notar que, quando usamos rotas com parâmetros, quando o usuário navega de `/blog/foo` para `/blog/bar`, a mesma instância do componente será utilizada. Ambas as rotas exibindo o mesmo componente é mais eficiente do que destruir uma instância antiga e criar uma nova.

<p class="tip">Fique atento que, usando um componente deste jeito, significará que os gatilhos do ciclo de vida do componente não serão chamados. Veja a documentação do roteador do Vue para aprender mais sobre isso em [combinando rotas dinâmicas](https://router.vuejs.org/en/essentials/dynamic-matching.html).</p>

Para corrigir isso, precisamos observar o objeto `$route` e chamar o `getPost()` quando a rota for alterada.

Atualize a seção `<script>` em `components/BlogPost.vue`:

```html
<script>
  import { butter } from '@/buttercms'
  export default {
    name: 'blog-post',
    data() {
      return {
        post: null
      }
    },
    methods: {
      getPost() {
        butter.post.retrieve(this.$route.params.slug)
          .then(res => {
            this.post = res.data
          }).catch(res => {
            console.log(res)
          })
      }
    },
    watch: {
      $route(to, from) {
        this.getPost()
      }
    },
    created() {
      this.getPost()
    }
  }
</script>
```

Com isso, a sua aplicação agora possui um _blog_ que pode ser, facilmente, atualizado através da interface gráfica fornecida pelo ButterCMS.

## Categorias, Marcações e Autores

Use a _API_ do ButterCMS para categorias, marcações e autores para filtrar o conteúdo do seu _blog_. Veja a documentação do ButterCMS para mais informações sobre isso:

* [Categorias](https://buttercms.com/docs/api/?ruby#categories)
* [Marcações](https://buttercms.com/docs/api/?ruby#tags)
* [Autores](https://buttercms.com/docs/api/?ruby#authors)

Aqui está um exemplo de como listar todas as categorias do _blog_ e, também, de como obter as postagens organizadas por categoria. Chame esses métodos no gatilho `created()` do ciclo de vida do componente.

```javascript
methods: {
  // ...
  getCategories() {
    butter.category.list()
      .then(res => {
        console.log('Lista de Categorias:')
        console.log(res.data.data)
      })
  },
  getPostsByCategory() {
    butter.category.retrieve('example-category', {
        include: 'recent_posts'
      })
      .then(res => {
        console.log('Postagens de categoria específica:')
        console.log(res)
      })
  }
},
created() {
  // ...
  this.getCategories()
  this.getPostsByCategory()
}
```

## Padrões Alternativos

Um padrão alternativo a ser considerado, especialmente se você preferir escrever apenas em _Markdown_, é usar algo como o [Nuxtent](https://nuxtent.now.sh/guide/writing#async-components). _Nuxtent_ permite que você use um componente Vue dentro de um arquivo _Markdown_. Essa seria uma abordagem bem parecida à abordagem de um _site_ estático (por exemplo, usando _Jekyll_), onde as postagens do _blog_ são escritas em arquivos _Markdown_. O _Nuxtent_ possui uma boa integração entre o Vue e o _Markdown_, permitindo que você viva 100% no mundo do Vue.

## Considerações Finais

É isso! Você agora possui um _blog_ completamente funcional alimentado por um _CMS_ executando junto à sua aplicação. Esperamos que esse tutorial tenha lhe ajudado e feito o seu desenvolvimento com Vue ainda mais agradável.
