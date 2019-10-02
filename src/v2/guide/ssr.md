---
title: Renderizando no Lado do Servidor
type: guide
order: 503
---

## Guia Completo de SSR

Renderização no lado do servidor, ou simplesmente SSR (do inglês _Server-Side Rendering_), é um tópico particularmente extenso. Criamos um guia independente apenas sobre a criação de aplicações Vue renderizadas a partir do servidor. Este guia é bem aprofundado para aqueles que já estão familiarizados com o desenvolvimento _client-side_ com Vue, desenvolvimento _server-side_ com Node.js e utilização de Webpack. Confira em [ssr.vuejs.org](https://ssr.vuejs.org/).

## Nuxt.js

Configurar corretamente todos os aspectos de uma aplicação pronta para produção utilizando renderização pelo servidor pode ser uma tarefa árdua. Felizmente, existe um excelente projeto da comunidade que visa tornar tudo isso mais fácil: [Nuxt.js](https://nuxtjs.org/). Nuxt.js é um _framework_ de alto nível criado por cima do ecossistema Vue que fornece uma experiência extremamente simplificada para escrever aplicações universais. Melhor ainda, você pode utilizá-lo como um gerador de páginas estáticas (páginas HTML geradas a partir de Componentes _Single-File_)! Nós recomendamos fortemente experimentá-lo.

## Quasar Framework SSR + PWA

[Quasar Framework](https://quasar.dev) gerará um aplicativo SSR (com opcional entrega de PWA) que aproveita seu sistema de _build_ de ponta, configuração sensata e extensibilidade pelo desenvolvedor para facilitar o desenho e construção de sua ideia. Com mais de cem componentes específicos compatíveis com "Material Design 2.0", você pode decidir quais executar no servidor, quais estarão disponíveis no navegador - e até gerenciar as _tags_ `<meta>` do seu _site_. Quasar é um ambiente de desenvolvimento baseado em Node.js e webpack que incrementa e agiliza o rápido desenvolvimento de aplicativos SPA, PWA, SSR, Electron e Cordova - tudo a partir de uma única base de código.
