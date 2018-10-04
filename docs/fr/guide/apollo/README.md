# Apollo dans les composants Vue

Pour déclarer vos requêtes dans vos composants Vue, ajoutez un objet `apollo`:

```js
new Vue({
    apollo: {
        // Options spécifiques à Apollo
    },
})
```

You can access the [apollo-client](https://www.apollographql.com/docs/react/) instances with `this.$apollo.provider.defaultClient` or `this.$apollo.provider.clients.<key>` (for [Multiple clients](../multiple-clients.md)) in all your vue components.

## Les requêtes

Dans l'objet `apollo`, ajouter un attribut pour toutes les propriétés que vous souhaitez alimenter par le résultat de votre requête Apollo.

```js
import gql from 'graphql-tag'

export default {
  apollo: {
    // Cette requête simple mettra à jour la propriété 'hello' du composant
    hello: gql`query { hello }`,
  },
}
```

Plus de détails dans la [section Requêtes](./queries.md).

## Les mutations

Utilisez `this.$apollo.mutate` pour déclencher vos mutations:

```js
methods: {
  async addTag() {
    // Appelle la mutation
    const result = await this.$apollo.mutate({
      // La requête
      mutation: gql`mutation ($label: String!) {
        addTag(label: $label) {
          id
          label
        }
      }`,
      // Les paramètres
      variables: {
        label: this.newTag,
      },
    })
  }
}
```

Plus de détails dans la [section Mutations](./mutations.md).

## Les options spéciales

Les options spéciales commencent par un `$` dans l'objet `apollo`.

- `$skip` pour bloquer toutes les requêtes et souscriptions (voir plus bas)
- `$skipAllQueries` pour bloquer toutes les requêtes (voir plus bas)
- `$skipAllSubscriptions` pour bloquer toutes les souscriptions (voir plus bas)
- `$deep` pour surveiller avec `deep: true` on the properties above when a function is provided
- `$error` pour capturer les erreurs avec un comportement global (voir l'option avancée `error` pour les requêtes)
- `$query` pour appliquer des options par défaut aux requêtes.

Exemple:

```vue
<script>
export default {
  data () {
    return {
      loading: 0,
    }
  },
  apollo: {
    $query: {
      loadingKey: 'loading',
    },
    query1: { ... },
    query2: { ... },
  },
}
</script>
```

Vous pouvez définir dans le provider apollo un ensemble d'options qui s'appliquent dans toutes les requêtes. Par exemple:

```js
const apolloProvider = new VueApollo({
  defaultClient: apolloClient,
  defaultOptions: {
    // Ces options s'appliquent dans toutes les requêtes de composant
    $query: {
      loadingKey: 'loading',
      fetchPolicy: 'cache-and-network',
    },
  },
})
```

## Passer une requête

Vous pouvez désactiver toutes les requêtes pour le composant avec `skipAllQueries`, toutes les souscriptions avec `skipAllSubscriptions` et les deux avec `skipAll`:

```js
this.$apollo.skipAllQueries = true
this.$apollo.skipAllSubscriptions = true
this.$apollo.skipAll = true
```

Vous pouvez aussi déclarer ces propriétés dans l'option `apollo` du composant. Elle peuvent être booléennes:

```js
apollo: {
  $skipAll: true
}
```

Ou des fonctions reactives:

```js
apollo: {
  $skipAll () {
    return this.foo === 42
  }
}
```