---
title: Dockerizando sua Aplicação Vue
type: cookbook
order: 13
---

## Exemplo Base

Eis que você construiu sua primeira aplicação usando o incrível [template webpack Vue](https://github.com/vuejs-templates/webpack) e agora quer muito impressionar seus colegas demonstrando que você também pode rodá-lo em um contêiner Docker.

Vamos começar criando um `Dockerfile` na pasta raiz do projeto:

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

# copia arquivos e pastas para o diretório atual de trabalho (pasta 'app')
COPY . .

# compila a aplicação de produção com minificação
RUN npm run build

EXPOSE 8080
CMD [ "http-server", "dist" ]
```

Pode parecer redundante primeiro copiar os arquivos `package.json` e `package-lock.json` e depois todos os arquivos e pastas do projeto em dois passos separados, porém existe [uma razão muito boa](http://bitjudo.com/blog/2014/03/13/building-efficient-dockerfiles-node-dot-js/) para isso (_spoiler_: permite tirar vantagem das camadas armazenadas em cache do Docker).

Agora vamos compilar a imagem Docker da nossa aplicação Vue:

```bash
docker build -t vuejs-cookbook/dockerize-vuejs-app .
```

Finalmente, vamos executar a aplicação Vue em um contêiner Docker:

```bash
docker run -it -p 8080:8080 --rm --name dockerize-vuejs-app-1 vuejs-cookbook/dockerize-vuejs-app
```

Devemos conseguir acessar nossa aplicação Vue em `localhost:8080`.

## Exemplo do Mundo Real

No exemplo anterior, nós usamos [servidor http](https://github.com/indexzero/http-server) simples com zero configurações para servir nossa aplicação Vue, o que é perfeitamente adequado para para prototipação rápida e _pode_ ser adequado para ambientes de produção simples. Até porque, a documentação diz:

> É poderoso o suficiente para uso em produção, mas é simples e customizável o suficiente para ser usado em testes, desenvolvimento local, e aprendizado.

Apesar disso, para casos de uso reais e complexos, pode ser sensato se sustentar sobre ombros gigantes como o [NGINX](https://www.nginx.com/) ou [Apache](https://httpd.apache.org/) e é exatamente o que faremos em seguida: estamos prestes a usar o NGINX para servir nossa aplicação Vue, pois ele é considerado uma das soluções com melhor desempenho e estabilidade dentre as disponíveis.

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
* dividimos nosso `Dockerfile` original em múltiplos estágios, utilizando a funcionalidade de [compilação multiestágio](https://docs.docker.com/develop/develop-images/multistage-build/) do Docker;
* o primeiro estágio é responsável por compilar um artefato pronto para produção da nossa aplicação Vue;
* o segundo estágio é responsável por servir tal artefato usando NGINX.

Agora vamos compilar a imagem Docker da nossa aplicação Vue:

```bash
docker build -t vuejs-cookbook/dockerize-vuejs-app .
```

Finalmente, vamos executar nossa aplicação Vue em um contêiner Docker:

```bash
docker run -it -p 8080:80 --rm --name dockerize-vuejs-app-1 vuejs-cookbook/dockerize-vuejs-app
```

Devemos ser capazes de acessar nossa aplicação Vue em `localhost:8080`.

## Conteúdo Adicional

Se você está lendo este livro de receitas, há grandes chances de que você já saiba o motivo para "dockerizar" sua aplicação Vue. Mas, se você aterrissou nessa página depois de apertar o botão `Estou com sorte` no Google, deixe-me compartilhar com você algumas boas razões pra fazer isso.

A tendência atual é construir aplicações usando a abordagem [Cloud-Native](https://pivotal.io/cloud-native), a qual resume a maioria das seguintes palavras da moda:
* Microsserviços
* _DevOps_
* Entrega Contínua

Vamos ver como esses conceitos realmente afetam nossa decisão de “dockerizar” nossa aplicação Vue.

### Efeito dos Microsserviços

Adotando o [estilo arquitetural de microsserviços](https://martinfowler.com/microservices/), acabamos por construir uma única aplicação como um conjunto de pequenos serviços, cada um executando nos seus próprios processos e se comunicando com mecanismos leves. Esses serviços são construídos em torno de capacidades de negócios e implantados independentemente, por maquinários completamente automáticos de implantação.

Então, aderir a este estilo arquitetural, na maioria das vezes, implica em desenvolver e entregar nosso _front-end_ como um serviço independente.

### Efeito do DevOps

A adoção da cultura [DevOps](https://martinfowler.com/bliki/DevOpsCulture.html),ferramentas e práticas de engenharia ágil tem, entre outras coisas, o excelente efeito de aumentar a colaboração entre os desenvolvedores e a equipe de operação. Um dos principais problemas do passado (mas também de hoje, em alguns cenários) é que o time de desenvolvedores tinham a tendência de serem desinteressados na operação e manutenção de um sistema, uma vez que já havia sido entregue ao time de operação, enquanto este tinha a tendência de não estar a par das metas da lógica de negócio do sistema, e portanto, eram relutantes em satisfazer as necessidades operacionais do sistema (também conhecidas como "caprichos de desenvolvedores").

Portanto, distribuir nossa aplicação Vue como uma imagem Docker ajuda a reduzir, senão remover completamente, as diferenças entre o serviço executando no _laptop_ de um desenvolvedor e o serviço no ambiente de produção, ou qualquer ambiente que possamos imaginar.

### Efeitos da Entrega Contínua

Usando a disciplina da [Entrega Contínua](https://martinfowler.com/bliki/ContinuousDelivery.html) construímos nosso _software_ de uma maneira que pode potencialmente ser lançado para produção a qualquer momento. Tal prática de engenharia é permitida pelo que é comumente  chamado de [canalização de entrega contínua](https://martinfowler.com/bliki/DeploymentPipeline.html). O propósito de uma canalização de entrega contínua (ou, no inglês, _deployment pipeline_) é dividir os passos de construção em estágios (como compilação, testes de unidade, testes de integração, testes de performance, etc.) e permitir que cada estágio verifique a construção do nosso artefato sempre que nosso _software_ é modificado. Finalmente, cada estágio aumenta nossa confiança na prontidão de produção da construção do nosso artefato e, portanto, reduz o risco de quebrar coisas em produção (ou em qualquer outro ambiente que importe).

Portanto, criar uma imagem Docker para nossa aplicação Vue é uma boa escolha, já que ela representa nosso artefato ao final da construção. Este mesmo artefato pode, então, ser verificado pela nossa canalização de entrega contínua e pode, potencialmente, ser lançado em produção com confiança.

## Padrões Alternativos

Se sua empresa ainda não gosta de Docker e Kubernetes ou se você apenas quer publicar seu produto mínimo viável, talvez "dockerizar" sua aplicação Vue não seja o que você precisa.

As alternativas comuns são:
* utilizar uma plataforma _all-in-one_ como [Netlify](https://www.netlify.com/);
* hospedar seu SPA no [Amazon S3](https://aws.amazon.com/s3/) e servi-lo com [Amazon CloudFront](https://aws.amazon.com/cloudfront/) (veja [este artigo](https://serverless-stack.com/chapters/deploy-the-frontend.html) para um guia detalhado).
