---
title: Dockerizando sua Aplicação Vue
type: cookbook
order: 13
---

<p class="tip">**Nota da Equipe de Tradução**
Este arquivo ainda não foi traduzido! Leia a versão original em inglês a seguir e, se puder, colabore com sua tradução: acesse [nosso projeto no GitHub](https://github.com/vuejs-br/br.vuejs.org/issues), avise que irá contribuir e inicie a tradução. Sua participação é muito importante!</p>

## Exemplo Base

Eis que você construiu sua primeira aplicação usando o incrível [template webpack Vue.js](https://github.com/vuejs-templates/webpack)  e agora você quer muito impressionar seus colegas demonstrando que você também pode rodá-lo em um contêiner Docker.

Vamos começar criando um `Dockerfile` na pasta raiz do nosso projeto:

```docker
FROM node:9.11.1-alpine

# instala um servidor http simples para servir conteúdo estático
RUN npm install -g http-server

# faz da pasta 'app' o diretório atual de trabalho
WORKDIR /app

# copia os arquivos 'package.json' e 'package-lock.json' (se disponível)
COPY package*.json ./

# instala dependências do projeto
RUN npm install

# copia os arquivos e pastas do para o diretório atual de trabalho (i.e. pasta 'app')
COPY . .

# compila a aplicação de produção com minificação
RUN npm run build

EXPOSE 8080
CMD [ "http-server", "dist" ]
```

It may seem reduntant to first copy `package.json` and `package-lock.json` and then all project files and folders in two separate steps but there is actually [a very good reason for that](http://bitjudo.com/blog/2014/03/13/building-efficient-dockerfiles-node-dot-js/) (spoiler: it allows us to take advantage of cached Docker layers).

Pode parecer redundante primeiro copiar os arquivos `package.json` e `package-lock.json` e depois todos os arquivos e pastas do projeto em dois passos separados, porém existe [uma razão muito boa](http://bitjudo.com/blog/2014/03/13/building-efficient-dockerfiles-node-dot-js/) para isso (_spoiler_: nos permite tirar vantagem das camadas armazenadas em cache do Docker)

Agora vamos compilar a imagem Docker da nossa aplicação Vue.js:

```bash
docker build -t vuejs-cookbook/dockerize-vuejs-app .
```

Finalmente, vamos executar nossa aplicação Vue.js em um contêiner Docker:

```bash
docker run -it -p 8080:8080 --rm --name dockerize-vuejs-app-1 vuejs-cookbook/dockerize-vuejs-app
```

Devemos conseguir acessar nossa aplicação Vue.js em `localhost:8080`.

## Exemplo do Mundo Real

In the previous example, we used a simple, zero-configuration command-line [http server](https://github.com/indexzero/http-server) to serve our Vue.js app which is perfectly ok for quick prototyping and _may_ even be ok for simple production scenarios. After all, the documentation says:

> It is powerful enough for production usage, but it's simple and hackable enough to be used for testing, local development, and learning.

Nevertheless, for realistically complex production use cases, it may be wiser to stand on the shoulders of some giant like [NGINX](https://www.nginx.com/) or [Apache](https://httpd.apache.org/) and that is exactly what we are going to do next: we are about to leverage NGINX to serve our Vue.js app because it is considered to be one of the most performant and battle-tested solutions out there.

Let's refactor our `Dockerfile` to use NGINX:

 ```docker
# build stage
FROM node:9.11.1-alpine as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# production stage
FROM nginx:1.13.12-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

Ok, let's see what's going on here:
* we have split our original `Dockerfile` in multiple stages by leveraging the Docker [multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/) feature;
* the first stage is responsible for building a production-ready artifact of our Vue.js app;
* the second stage is responsible for serving such artifact using NGINX.

Now let's build the Docker image of our Vue.js app:

```bash
docker build -t vuejs-cookbook/dockerize-vuejs-app .
```

Finally, let's run our Vue.js app in a Docker container:

```bash
docker run -it -p 8080:80 --rm --name dockerize-vuejs-app-1 vuejs-cookbook/dockerize-vuejs-app
```

We should be able to access our Vue.js app on `localhost:8080`.

## Additional Context

If you are reading this cookbook, chances are you already know why you decided to dockerize your Vue.js app. But if you simply landed on this page after hitting the Google's `I'm feeling lucky` button, let me share with you a couple of good reasons for doing that.

Today's modern trend is to build applications using the [Cloud-Native](https://pivotal.io/cloud-native) approach which revolves mainly around the following buzzwords:
* Microservices
* DevOps
* Continuous Delivery

Let's see how these concepts actually affect our decision of dockerizing our Vue.js app.

### Effects of Microservices

By adopting the [microservices architectural style](https://martinfowler.com/microservices/), we end up building a single application as a suite of small services, each running in its own process and communicating with lightweight mechanisms. These services are built around business capabilities and independently deployable by fully automated deployment machinery.

So, committing to this architectural approach most of the time implies developing and delivering our front-end as an independent service.

### Effects of DevOps

The adoption of [DevOps](https://martinfowler.com/bliki/DevOpsCulture.html) culture, tools and agile engineering practices has, among other things, the nice effect of increasing the collaboration between the roles of development and operations. One of the main problem of the past (but also today in some realities) is that the dev team tended to be uninterested in the operation and maintenance of a system once it was handed over to the ops team, while the latter tended to be not really aware of the system's business goals and, therefore, reluctant in satisfying the operational needs of the system (also referred to as "whims of developers").

So, delivering our Vue.js app as a Docker image helps reducing, if not removing entirely, the difference between running the service on a developer's laptop, the production environment or any environment we may think of.

### Effects of Continuous Delivery

By leveraging the [Continuous Delivery](https://martinfowler.com/bliki/ContinuousDelivery.html) discipline we build our software in a way that it can potentially be released to production at any time. Such engineering practice is enabled by means of what is normally called [continuous delivery pipeline](https://martinfowler.com/bliki/DeploymentPipeline.html). The purpose of a continuous delivery pipeline is to split our build into stages (e.g. compilation, unit tests, integration tests, performance tests, etc.) and let each stage verify our build artifact whenever our software changes. Ultimately, each stage increases our confidence in the production readiness of our build artifact and, therefore, reduces the risk of breaking things in production (or any other environment for that matters).

So, creating a Docker image for our Vue.js app is a good choice here because that would represent our final build artifact, the same artifact that would be verified against our continuous delivery pipeline and that could potentially be released to production with confidence.

## Alternative Patterns

If your company is not into Docker and Kubernetes just yet or you simply want to get your MVP out the door, maybe dockerizing your Vue.js app is not what you need.

Common alternatives are:
* leveraging an all-in-one platform like [Netlify](https://www.netlify.com/);
* hosting your SPA on [Amazon S3](https://aws.amazon.com/s3/) and serving it with [Amazon CloudFront](https://aws.amazon.com/cloudfront/) (see [this](https://serverless-stack.com/chapters/deploy-the-frontend.html) link for a detailed guide).
