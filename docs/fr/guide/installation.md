# Installation

## Le plugin Vue CLI

J'ai créer un plugin for [vue-cli](http://cli.vuejs.org), vous pouvez ainsi ajouter Apollo (avec un server GraphQL optionnel!) littérallement en deux minutes! ✨🚀

Dans votre projet vue-cli 3:

```bash
vue add apollo
```

Vous pouvez alors passer cette section de la documentation: [Usage basique](./apollo/).

[Plus d'informations](https://github.com/Akryum/vue-cli-plugin-apollo)

## Apollo Boost

Apollo Boost est un façon d'ajouter un client Apollo avec aucune configration. Cela inclue du paramètrage par défaut recommandé tel que `InMemoryCache` et `HttpLink`. Apollo Boost est parfait pour commencer à développer rapidement:

Installer: 

```
npm install --save vue-apollo graphql apollo-boost
```

Ou:

```
yarn add vue-apollo graphql apollo-boost
```

### Apollo client

Dans vos applications, créer une instance `ApolloClient` et installer le plugin `VueApollo`:

```js
import Vue from 'vue'
import ApolloClient from "apollo-boost"
import VueApollo from "vue-apollo"

const apolloProvider = new VueApollo({
  defaultClient: new ApolloClient({
    uri: "https://api.graphcms.com/simple/v1/awesomeTalksClone"
  })
})

Vue.use(VueApollo)
```



## Installation manuelle

Si vous désirez avoir un contrôle plus fin et installer ces packages manuellement.

```
npm install --save vue-apollo graphql apollo-client apollo-link apollo-link-http apollo-cache-inmemory graphql-tag
```

Ou:

```
yarn add vue-apollo graphql apollo-client apollo-link apollo-link-http apollo-cache-inmemory graphql-tag
```

### Le client Apollo

In your app, create an `ApolloClient` instance and install the `VueApollo` plugin:

```js
import Vue from 'vue'
import { ApolloClient } from 'apollo-client'
import { HttpLink } from 'apollo-link-http'
import { InMemoryCache } from 'apollo-cache-inmemory'
import VueApollo from 'vue-apollo'

const httpLink = new HttpLink({
  // You should use an absolute URL here
  uri: 'http://localhost:3020/graphql',
})

// Create the apollo client
const apolloClient = new ApolloClient({
  link: httpLink,
  cache: new InMemoryCache(),
  connectToDevTools: true,
})

const apolloProvider = new VueApollo({
  defaultClient: apolloClient,
})

// Install the vue plugin
Vue.use(VueApollo)
```

## Le provider Apollo

The provider holds the Apollo client instances that can then be used by all the child components. Inject it into your components with `provide`:

```js
new Vue({
  el: '#app',
  apolloProvider,
  render: h => h(App),
})
```
