# Requêtes

Dans l'objet `apollo`, ajoutez un attribut à chaque propriété que vous souhaitez alimenter avec le résultat d'une requête Apollo. Chacun d'entre eux deviendra une requête smart.

## Une requête simple

Utilisez `gql` pour écrire vos requêtes GraphQL:

```js
import gql from 'graphql-tag'
```

Placez la requête [gql] (https://github.com/apollographql/graphql-tag) directement comme valeur:

```js
apollo: {
  // Requête simple qui mettra à jour la propriété 'hello' vue
  hello: gql`{hello}`,
},
```

Vous pouvez ensuite accéder à la requête avec `this.$apollo.queries.<name>`.

Vous pouvez initialiser la propriété dans le hook `data` de votre composant vue:

```js
data () {
  return {
    // Initialisez vos données apollo
    hello: '',
  },
},
```

Côté serveur, ajoutez le schéma et le résolveur correspondants:

```js
export const schema = `
type Query {
  hello: String
}

schema {
  query: Query
}
`

export const resolvers = {
  Query: {
    hello(root, args, context) {
      return "Hello world!"
    },
  },
}
```

Pour plus d'informations, visitez la [documentation apollo] (https://www.apollographql.com/docs/apollo-server/).

Vous pouvez ensuite utiliser votre propriété comme d'habitude dans votre composant vue:

```vue
<template>
  <div class="apollo">
    <h3>Hello</h3>
    <p>
      {{hello}}
    </p>
  </div>
</template>
```

## Requête avec paramètres


Vous pouvez ajouter des variables (paramètres de lecture) à votre requête `gql` en déclarant` query` et `variables` dans un objet:

```js
// Options spécifiques à Apollo
apollo: {
  // Requête avec paramètres
  ping: {
    // requête gql
    query: gql`query PingMessage($message: String!) {
      ping(message: $message)
    }`,
    // paramètres statiques
    variables: {
      message: 'Meow',
    },
  },
},
```

Vous pouvez utiliser les options apollo `watchQuery` dans l'objet, comme par exemple:
 - `fetchPolicy`
 - `pollInterval`
 - ...

Voir la [documentation apollo] (https://www.apollographql.com/docs/react/api/apollo-client.html#ApolloClient.watchQuery) pour plus de détails.

Par exemple, vous pouvez ajouter l'option `fetchPolicy` apollo comme ceci:

```js
apollo: {
  // Query with parameters
  ping: {
    query: gql`query PingMessage($message: String!) {
      ping(message: $message)
    }`,
    variables: {
      message: 'Meow'
    },
    // option
    fetchPolicy: 'cache-and-network',
  },
},
```

De nouveau, vous pouvez initialiser votre propriété dans votre composant vue:

```js
data () {
  return {
    // Initialize your apollo data
    ping: '',
  }
},
```

Server-side, add the corresponding schema and resolver:

```js
export const schema = `
type Query {
  ping(message: String!): String
}

schema {
  query: Query
}
`

export const resolvers = {
  Query: {
    ping(root, { message }, context) {
      return `Answering ${message}`
    },
  },
}
```

Et utilisez-le ensuite dans votre composant vue:

```vue
<template>
  <div class="apollo">
    <h3>Ping</h3>
    <p>
      {{ ping }}
    </p>
  </div>
</template>
```

## État de chargement

Vous pouvez afficher un état de chargement grâce à `$apollo.loading`:

```vue
<div v-if="$apollo.loading">Loading...</div>
```

Ou pour cette requête spécifique `ping`:

```vue
<div v-if="$apollo.queries.ping.loading">Loading...</div>
```

## Fonction d'option

Vous pouvez utiliser une fonction qui sera appelée une fois lors de la création du composant. Elle doit renvoyer l'objet option:

```js
// Options spécifiques à Apollo
apollo: {
  // Requête avec paramètres
  ping () {
    // Ceci est appelé une fois lors de la création du composant
    // Il doit renvoyer l'objet option
    return {
      // requête gql
      query: gql`query PingMessage($message: String!) {
        ping(message: $message)
      }`,
      // Paramètres statiques
      variables: {
        message: 'Meow',
      },
    }
  },
},
```

::: tip
Cela fonctionne également pour [subscriptions] (./ subscriptions.md).
:::


## Définition de requête réactive

Vous pouvez utiliser une fonction pour l'option `query`. Ceci mettra à jour automatiquement la définition de la requête graphql:

```js
// La balise sélectionnée peut être une balise aléatoire ou la dernière balise ajoutée.
featuredTag: {
  query () {
    // Ici, vous pouvez accéder à l'instance de composant avec 'this'
    if (this.showTag === 'random') {
      return gql`{
        randomTag {
          id
          label
          type
        }
      }`
    } else if (this.showTag === 'last') {
      return gql`{
        lastTag {
          id
          label
          type
        }
      }`
    }
  },
  // Nous avons besoin de cela pour assigner la valeur de la propriété du composant 'FeaturedTag'
  update: data => data.randomTag || data.lastTag,
},
```

::: tip
Cela fonctionne également pour [subscriptions] (./ subscriptions.md).
:::

## Paramètres réactifs

Utilisez plutôt une fonction pour rendre les paramètres réactifs avec les propriétés de vue:

```js
// Options spécifiques à Apollo
apollo: {
  // Query with parameters
  ping: {
    query: gql`query PingMessage($message: String!) {
      ping(message: $message)
    }`,
    // Reactive parameters
    variables() {
      // Use vue reactive properties here
      return {
          message: this.pingInput,
      }
    },
  },
},
```

Cela va extraire à nouveau la requête chaque fois qu'un paramètre est modifié, par exemple:

```vue
<template>
  <div class="apollo">
    <h3>Ping</h3>
    <input v-model="pingInput" placeholder="Enter a message" />
    <p>
      {{ping}}
    </p>
  </div>
</template>
```

## Ignorer la requête

Si la requête est ignorée, elle sera désactivée et le résultat ne sera plus mis à jour. Vous pouvez utiliser l'option `skip`:

```js
// Options spécifiques à Apollo
apollo: {
  tags: {
    // Requête GraphQL
    query: gql`query tagList ($type: String!) {
      tags(type: $type) {
        id
        label
      }
    }`,
    // Variables reactive
    variables() {
      return {
        type: this.type,
      }
    },
    // Désactiver la requête
    skip() {
      return this.skipQuery
    },
  },
},
```


Ici, skip sera appelé automatiquement lorsque la propriété du composant skipQuery sera modifiée.

Vous pouvez également accéder directement à la requête et définir la propriété skip:

```js
this.$apollo.queries.tags.skip = true
```

## Exemple de requête réactive

Voici un exemple de requête réactive utilisant polling:

```js
// Options spécifiques à Apollo
apollo: {
  // 'tags' data property on vue instance
  tags: {
    query: gql`query tagList {
      tags {
        id,
        label
      }
    }`,
    pollInterval: 300, // ms
  },
},
```

Voici à quoi ressemble le côté serveur:

```js
export const schema = `
type Tag {
  id: Int
  label: String
}

type Query {
  tags: [Tag]
}

schema {
  query: Query
}
`

// Générateur de faux mots
import casual from 'casual'

// Let's generate some tags
var id = 0
var tags = []
for (let i = 0; i < 42; i++) {
  addTag(casual.word)
}

function addTag(label) {
  let t = {
    id: id++,
    label,
  }
  tags.push(t)
  return t
}

export const resolvers = {
  Query: {
    tags(root, args, context) {
      return tags
    },
  },
}
```

## Ajouter manuellement une requête smart

Vous pouvez ajouter manuellement une requête intelligente avec la méthode `$apollo.addSmartQuery(key, options)`:

```js
created () {
  this.$apollo.addSmartQuery('comments', {
    // même options qu'au dessus
  })
}
```

::: tip
En interne, cette méthode est appelée pour chaque entrée de requête dans l'option `apollo` du composant.
:::

## Options avancées

Il y a encore plus d'options spécifiques à vue-apollo, voir [API Reference](../../api/smart-query.md).
