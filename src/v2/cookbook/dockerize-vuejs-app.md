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

Portanto, distribuir nossa aplicação Vue.js como uma imagem Docker ajuda a reduzir, se não, remover completamente, as diferenças entre o serviço executando no laptop de um desenvolvedor e no ambiente de produção ou qualquer ambiente que possamos imaginar.

### Efeitos da Entrega Contínua

Usando a disciplina da [Entrega Contínua](https://martinfowler.com/bliki/ContinuousDelivery.html) construímos nosso software de uma maneira que pode potencialmente ser lançado para produção a qualquer momento. Tal prática de engenharia é permitida pelo que é comumente  chamado de [canalização de entrega contínua](https://martinfowler.com/bliki/DeploymentPipeline.html). O propósito de uma canalização de entrega contínua é dividir os passos de construção em estágios (e.g. compilação, testes de unidade, testes de integração, testes de performance, etc.) e permitir que cada estágio verifique a construção do nosso artefato sempre que nosso software mude. Finalmente, cada estágio aumenta nossa confiança na prontidão de produção da construção do nosso artefato e, portanto, reduz o risco de quebrar coisas em produção (ou em qualquer outro ambiente que importe).

Portanto, criar uma imagem Docker para nossa aplicação Vue.js é uma boa escolha já que ela representa nosso artefato ao final da construção, este mesmo artefato pode então ser verificado pela nossa canalização de entrega contínua e pode potencialmente ser lançado em produção com confiança.

## Padrões Alternativos

Se sua empresa ainda não gosta de Docker e Kubernetes ou  se você apenas quer publicar seu MVP, talvez "dockerizar" sua aplicação Vue.js não seja o que você precisa.

As alternativas comuns são:
* utilizar uma plataforma _all-in-one_ como [Netlify](https://www.netlify.com/);
* hospedar seu SPA no [Amazon S3](https://aws.amazon.com/s3/) e servi-lo com [Amazon CloudFront](https://aws.amazon.com/cloudfront/) (veja [esse](https://serverless-stack.com/chapters/deploy-the-frontend.html) link para um guia detalhado).
