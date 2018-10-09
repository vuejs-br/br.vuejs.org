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

No exemplo anterior, nós usamos [servidor http](https://github.com/indexzero/http-server) simples de zero configuração para servir nossa aplicação Vue.js, o que é perfeitamente adequado para para prototipação rápida e _pode_ ser adequado para ambientes de produção simples. Até porque, a documentação diz:

> É poderoso o suficiente para uso em produção, mas é simples e customizável o suficiente para ser usado em testes, desenvolvimento local, e aprendizado.

Apesar disso, para casos de uso reais e complexos, pode ser sensato se sustentar sobre ombros gigantes como o [NGINX](https://www.nginx.com/) ou [Apache](https://httpd.apache.org/) e é exatamente o que faremos em seguida: estamos prestes a usar o NGINX para servir nossa aplicação Vue.js pois ele é considerado como uma das soluções com melhor desempenho e mais bem tetado disponíveis.

Vamos refatorar nosso `Dockerfile` para usar o NGINX:

 ```docker
# estágio de compilação
FROM node:9.11.1-alpine as build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# estágio de produção
FROM nginx:1.13.12-alpine as production-stage
COPY --from=build-stage /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

Certo, vamos ver o que está acontecendo aqui:
* nós dividimos nosso `Dockerfile` original em múltiplos estágios utilizando a funcionalidade de [compilação multiestágio](https://docs.docker.com/develop/develop-images/multistage-build/) do Docker;
* o primeiro estágio é responsável por compilar um artefato pronto para produção da nossa aplicação Vue.js;
* o segundo estágio é responsável por servir tal artefato usando NGINX.

Agora vamos compilar a imagem Docker da nossa aplicação Vue.js:

```bash
docker build -t vuejs-cookbook/dockerize-vuejs-app .
```

Finalmente, vamos executar nossa aplicação Vue.js em um contêiner Docker:

```bash
docker run -it -p 8080:80 --rm --name dockerize-vuejs-app-1 vuejs-cookbook/dockerize-vuejs-app
```

Devemos ser capazes de acessar nossa aplicação Vue.js em `localhost:8080`.

## Conteúdo Adicional

Se você está lendo este livro de receitas, ha grandes chances que você já saiba  o motivo de "dockerizar" sua aplicação Vue.js. Mas se você aterrissou nessa página depois de apertar o botão `Estou com sorte` no Google, deixe-me compartilhar com você algumas boas razões pra fazer isso.

A tendencia atual é construir aplicações usando a abordagem [Cloud-Native](https://pivotal.io/cloud-native) que resume a maioria das seguintes palavras da moda:
* Microsserviços
* _DevOps_
* Entrega Contínua

Vamos ver como esses conceitos realmente afetam nossa decisão de “dockerizar” nossa aplicação Vue.js.

### Efeito dos Microsserviços

Adotando o [estilo arquitetural de microsserviços](https://martinfowler.com/microservices/), acabamos por construir uma única aplicação como um conjunto de pequenos serviços, cada um executando nos seus próprios processos e se comunicando com mecanismos leves. Esses serviços são construídos em torno de capacidades de negócios e implantados independentemente por maquinários totalmente automáticos de implantação.

So, committing to this architectural approach most of the time implies developing and delivering our front-end as an independent service.

### Efeito do DevOps

A adoção da cultura [DevOps](https://martinfowler.com/bliki/DevOpsCulture.html),ferramentas e praticas de engenharia ágil tem, entre outras coisas, o excelente efeito de aumentar a colaboração entre os desenvolvedores e a equipe de operação. Um dos principais problemas do passado (mas também de hoje em alguns cenários) é que o time de desenvolvedores tinham a tendencia de serem desinteressados na operação e manutenção de um sistema uma vez que já tenha sido entregue ao time de operação, enquanto este tinha a tendencia de não estar a parte das metas da lógica de negócio do sistema, e portanto, eram relutantes em satisfazer as necessidades operacionais do sistema. (também conhecido como "caprichos de desenvolvedores")

So, delivering our Vue.js app as a Docker image helps reducing, if not removing entirely, the difference between running the service on a developer's laptop, the production environment or any environment we may think of.

### Effects of Continuous Delivery

By leveraging the [Continuous Delivery](https://martinfowler.com/bliki/ContinuousDelivery.html) discipline we build our software in a way that it can potentially be released to production at any time. Such engineering practice is enabled by means of what is normally called [continuous delivery pipeline](https://martinfowler.com/bliki/DeploymentPipeline.html). The purpose of a continuous delivery pipeline is to split our build into stages (e.g. compilation, unit tests, integration tests, performance tests, etc.) and let each stage verify our build artifact whenever our software changes. Ultimately, each stage increases our confidence in the production readiness of our build artifact and, therefore, reduces the risk of breaking things in production (or any other environment for that matters).

So, creating a Docker image for our Vue.js app is a good choice here because that would represent our final build artifact, the same artifact that would be verified against our continuous delivery pipeline and that could potentially be released to production with confidence.

## Alternative Patterns

If your company is not into Docker and Kubernetes just yet or you simply want to get your MVP out the door, maybe dockerizing your Vue.js app is not what you need.

Common alternatives are:
* leveraging an all-in-one platform like [Netlify](https://www.netlify.com/);
* hosting your SPA on [Amazon S3](https://aws.amazon.com/s3/) and serving it with [Amazon CloudFront](https://aws.amazon.com/cloudfront/) (see [this](https://serverless-stack.com/chapters/deploy-the-frontend.html) link for a detailed guide).
