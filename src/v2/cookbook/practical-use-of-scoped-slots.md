---
title: Uso prático de slots com escopo com o GoogleMaps
type: cookbook
order: 14
---

## Exemplo Base

Há situações em que você pode querer que o template dentro do slot consiga acessar os dados do componente filho que é responsável por renderizar o conteúdo do slot. Isso é particurlarmente útil quando você precisa da liberdade para criar templates customizados que usam propriedades do data do componente filho. Esse é um caso de uso típico para scoped slots.

Imagine um componente que configura e prepara uma API externa para ser utilizada em outro componente, mas não está diretamente ligado a nenhum template específico. Tal componente poderia então ser reutilizado em múltiplos lugares renderizando templates diferentes mas usando o mesmo objeto base com API específica.

Iremos criar um componente (`GoogleMapLoader.vue`) que:

1. Inicializa a [Google Maps API](https://developers.google.com/maps/documentation/javascript/reference/)
2. Cria os objetos `google` e `map`
3. Expõe estes objetos ao componente pai em que o `GoogleMapLoader` é utilizado

Abaixo há um exemplo de como isso pode ser alcançado. Iremos analizar o código parte a parte e ver o que realmente está acontecendo na próxima seção.

Vamos primeiramente estabelecer o template do nosso `GoogleMapLoader.vue`:

```html
<template>
  <div>
    <div class="google-map" ref="googleMap"></div>
    <template v-if="Boolean(this.google) && Boolean(this.map)">
      <slot
        :google="google"
        :map="map"
      />
    </template>
  </div>
</template>
```

Agora, nosso script precisa passar algumas props ao componente, o que nos permite preparar a [Google Maps API](https://developers.google.com/maps/documentation/javascript/reference/) e o [Objeto Map](https://developers.google.com/maps/documentation/javascript/reference/map#Map):

```js
import GoogleMapsApiLoader from 'google-maps-api-loader'

export default {
  props: {
    mapConfig: Object,
    apiKey: String,
  },

  data() {
    return {
      google: null,
      map: null
    }
  },

  async mounted() {
    const googleMapApi = await GoogleMapsApiLoader({
      apiKey: this.apiKey
    })
    this.google = googleMapApi
    this.initializeMap()
  },

  methods: {
    initializeMap() {
      const mapContainer = this.$refs.googleMap
      this.map = new this.google.maps.Map(
        mapContainer, this.mapConfig
      )
    }
  }
}
```

Essa é só uma parte de um exemplo completo, que você pode encontrar no Codesandbox abaixo.

<iframe src="https://codesandbox.io/embed/1o45zvxk0q" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>

## Exemplo do Mundo Real: Criando um componente Google Map Loader

### 1. Crie um componente que inicializa nosso mapa

`GoogleMapLoader.vue`

No template, nós criamos um container para o mapa que será utilizado para montar o objeto [Map](https://developers.google.com/maps/documentation/javascript/reference/map#Map) extraído da Google Maps API.

```html
<template>
  <div>
    <div class="google-map" ref="googleMap"></div>
  </div>
</template>
```

Em seguida, nosso script precisa receber props do componente pai que irão nos permitir inicializar o mapa. Estas props são:

- [mapConfig](https://developers.google.com/maps/documentation/javascript/reference/3/map#MapOptions): objeto de configuração do Google Maps
- [apiKey](https://developers.google.com/maps/documentation/javascript/get-api-key): Nossa api key pessoal do Google Maps

```js
import GoogleMapsApiLoader from 'google-maps-api-loader'

export default {
  props: {
    mapConfig: Object,
    apiKey: String,
  },
```

Então, nós inicializamos os valores google e map como nulos:

```js
  data() {
    return {
      google: null,
      map: null
    }
  },
```

No gaatilho `mounted` nós instanciamos os objetos `googleMapApi` e `Map` do `GoogleMapsApi` e atribuímos os valores de `google` e `map` às instâncias criadas:

```js
  async mounted() {
    const googleMapApi = await GoogleMapsApiLoader({
      apiKey: this.apiKey
    })
    this.google = googleMapApi
    this.initializeMap()
  },

  methods: {
    initializeMap() {
      const mapContainer = this.$refs.googleMap
      this.map = new this.google.maps.Map(mapContainer, this.mapConfig)
    }
  }
}
```

Até agora tudo bem. Com tudo isso feito, nós podemos continuar adicionando outros objetos ao map (Markers, Polylines, etc.) e utilizá-lo como um componente map comum.

Mas, quremos usar nosso componente `GoogleMapLoader` apenas como um loader que prepara o mapa — não queremos renderizar nada nele.

Para alcançar isso, nós precisamos permitir o componente pai que irá usar nosso `GoogleMapLoader` para acessar `this.google` e `this.map` que estão dentro do componente `GoogleMapLoader`. É aí que os [scoped slots](https://br.vuejs.org/v2/guide/components-slots.html#Scoped-Slots) brilham de verdade. Os Scoped slots nos permitem expor as propriedades de um componente filho para o componente pai. Isso pode parecer um Inception, mas fique comigo mais um minuto para entendermos melhor a capacidade dos scoped slots.

### 2. Crie um componente que utiliza o nosso componente inicializador

`TravelMap.vue`

No template, nós renderizamos o componente `GoogleMapLoader` e passamos as props que são obrigatórias para inicializar o mapa.

```html
<template>
  <GoogleMapLoader
    :mapConfig="mapConfig"
    apiKey="yourApiKey"
  />
</template>
```

Nossa tag script vai se parecer com isso:

```js
<script>
import GoogleMapLoader from './GoogleMapLoader'
import { mapSettings } from '@/constants/mapSettings'

export default {
  components: {
    GoogleMapLoader
  },

  computed: {
    mapConfig () {
      return {
        ...mapSettings,
        center: { lat: 0, lng: 0 }
      }
    },
  },
}
</script>
```

Ainda não há scoped slots, então vamos adicionar um.

### 3. Exponha as propriedades `google` e `map` para o componente pai ao adicionar um scoped slot

Finalmente, nós podemos adicionar um scoped slot que irá fazer o trabalho e nos permitirá acessar as props do componente filho no componente pai. Faremos isso adicionando a tag `<slot>` no componente filho e passando as props que queremos expor (usando a diretiva `v-bind` ou o facilitador `:nomeDaProp`). É como passar props para um componente filho, mas ao fazer isso na tag `<slot>` revertemos a direção do fluxo de dados para o componente pai.

`GoogleMapLoader.vue`

```html
<template>
  <div>
    <div class="google-map" ref="googleMap"></div>
    <template v-if="Boolean(this.google) && Boolean(this.map)">
      <slot
        :google="google"
        :map="map"
      />
    </template>
  </div>
</template>
```

Agora que temos o slot no componente filho, precisamos receber e consumir as props expostas no componente pai.

### 4. Receba as props expostas no componente pai utilizando o atributo `slot-scope`

Para receber as props no componente pai, nós declaramos um elemento template e usamos o atributo `slot-scope`. Esse atributo tem acesso ao objeto que carrega todas as props exposas do componente filho. Nós podemos pegar o objeto inteiro ou podemos [desestruturar o objeto](https://br.vuejs.org/v2/guide/components-slots.html#Destructuring-slot-scope) com apenas o que precisamos.

Vamos desestruturá-lo para pegar o que precisamos.

`TravelMap.vue`

```html
<GoogleMapLoader
  :mapConfig="mapConfig"
  apiKey="yourApiKey"
>
  <template slot-scope="{ google, map }">
  	{{ map }}
  	{{ google }}
  </template>
</GoogleMapLoader>
```

Mesmo que as props `google` e `map` não existam no escopo do `TravelMap`, o componente tem acesso a elas e nós podemos utilizá-las no template.

Você pode se perguntar: por que faríamos as coisas assim e pra que serve tudo isso?

Scoped slots nos permitem passar um template ao slot no lugar de um elemento renderizado. Ele é chamado de `scoped` slot porque ele terá acesso a certos dados do componente filho, mesmo que o template seja renderizado no escopo do componente pai. Isso nos dá a liberdade para preencher o template com conteúdo customizado do componente pai.

### 5. Crie factory components para Markers e Polylines

Agora que temos nosso mapa pronto nós iremos criar dois factory components que irão ser usados para adicionar elementos ao `TravelMap`.

`GoogleMapMarker.vue`

```js
import { POINT_MARKER_ICON_CONFIG } from '@/constants/mapSettings'

export default {
  props: {
    google: {
      type: Object,
      required: true
    },
    map: {
      type: Object,
      required: true
    },
    marker: {
      type: Object,
      required: true
    }
  },

  mounted() {
    new this.google.maps.Marker({
      position: this.marker.position,
      marker: this.marker,
      map: this.map,
      icon: POINT_MARKER_ICON_CONFIG
    })
  }
}
```

`GoogleMapLine.vue`

```js
import { LINE_PATH_CONFIG } from '@/constants/mapSettings'

export default {
  props: {
    google: {
      type: Object,
      required: true
    },
    map: {
      type: Object,
      required: true
    },
    path: {
      type: Array,
      required: true
    }
  },

  mounted() {
    new this.google.maps.Polyline({
      path: this.path,
      map: this.map,
      ...LINE_PATH_CONFIG
    })
  }
}
```

Ambos recebem `google`, que nós usamos para extrair o objeto obrigatório (Marker ou Polyline) assim como `map` é uma referência ao mapa em que queremos colocar nosso elemento.

Cada componente também espera uma prop extra para criar um elemento correspondente. Nesse caso, nós temos `market` e `path`, respectivamente.

No gatilho mounted, nós criamos um elemento (Marker/Polyline) e o anexamos ao nosso mapa ao passar a propriedade `map` ao construtor do objeto.

Ainda há mais um passo...

### 6. Adicione elementos ao mapa

Vamos usar nossos factory components para adicionar elementos ao nosso mapa. Nós precisamos renderizar os factory components e passar os objetos `google` e `map` para que os dados fluam para os lugares corretos.

Nós também precisamos prover os dados que são requeridos pelo elemento em si. Nesse caso, estes são o objeto `marker` com a posição do marker e o objeto `path` com as coordenadas da Polyline.

Aí vamos nós, integrando os data points diretamente no template:

```html
<GoogleMapLoader
  :mapConfig="mapConfig"
  apiKey="yourApiKey"
>
  <template slot-scope="{ google, map }">
    <GoogleMapMarker
      v-for="marker in markers"
      :key="marker.id"
      :marker="marker"
      :google="google"
      :map="map"
    />
    <GoogleMapLine
      v-for="line in lines"
      :key="line.id"
      :path.sync="line.path"
      :google="google"
      :map="map"
    />
  </template>
</GoogleMapLoader>
```

Nós precisamos importar os factory components obrigatórios no nosso script e arrumar os dados que serão passados aos markers e lines:

```js
import { mapSettings } from '@/constants/mapSettings'

export default {
  components: {
    GoogleMapLoader,
    GoogleMapMarker,
    GoogleMapLine
  },

  data () {
    return {
      markers: [
      { id: 'a', position: { lat: 3, lng: 101 } },
      { id: 'b', position: { lat: 5, lng: 99 } },
      { id: 'c', position: { lat: 6, lng: 97 } },
      ],
      lines: [
        { id: '1', path: [{ lat: 3, lng: 101 }, { lat: 5, lng: 99 }] },
        { id: '2', path: [{ lat: 5, lng: 99 }, { lat: 6, lng: 97 }] }
      ],
    }
  },

  computed: {
    mapConfig () {
      return {
        ...mapSettings,
        center: this.mapCenter
      }
    },

    mapCenter () {
      return this.markers[1].position
    }
  },
}
```

## Quando devemos evitar esse padrão

Pode ser tentador criar uma solução bem compexa baseada nesse exemplo, mas em certo ponto nós podemos chegar à situação onde essa abstração se torna uma entidade independente vivendo dentro do nosso código fonte. Se nós chegarmos a esse ponto pode ser interessante considerar uma extração para um add-on.

## Encerramento

Isso é tudo. Com todo esse código criado agora nós podemos reutilizar o componente `GoogleMapLoader` como base para todos os nossos mapas ao passar templates diferentes para cada um deles. Imagine que você precisa criar outro mapa com Markers diferentes ou apenas Markers sem Polylines. Ao usar o padrão acima isso se torna muito fácil, já que apenas precisamos passar um conteúdo diferente para o componente `GoogleMapLoader`.

Esse padrão não é estritamente conectado ao Google Maps; ele pode ser utilizado com qualquer biblioteca para criar o componente base e expor a API da biblioteca que pode ser então utilizada no componente que que chamou o componente base.
