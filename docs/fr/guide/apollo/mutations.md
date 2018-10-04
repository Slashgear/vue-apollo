# Mutations


Les mutations sont des requêtes qui modifient l'état de vos données sur votre serveur apollo.

Utilisez `this.$apollo.mutate()` pour envoyer une mutation GraphQL.

Pour plus d'informations, visitez la [documentation apollo](https://www.apollographql.com/docs/react/api/apollo-client.html#ApolloClient.mutate). Vous pouvez consulter un [exemple d'application](https://github.com/Akryum/vue-apollo-todos) axé sur les mutations.

:::warning
Vous ne devriez pas envoyer les champs `__typename` dans les variables, il n'est donc pas recommandé d'envoyer directement un objet de résultat Apollo.
:::

```js
methods: {
  addTag() {
    // Nous sauvegardons la saisie de l'utilisateur en cas d'erreur
    const newTag = this.newTag
    // On nettoie le champ pour donner un effet dynamique à l'utilisateur
    this.newTag = ''
    // appelle la mutation GraphQL
    this.$apollo.mutate({
      // Requête
      mutation: gql`mutation ($label: String!) {
        addTag(label: $label) {
          id
          label
        }
      }`,
      // Paramètres
      variables: {
        label: newTag,
      },
      // Met à jour le cache avec le résultat
      // La requête sera mise à jour avec la réponse optimiste
      // puis avec le résultat réel de la mutation
      update: (store, { data: { addTag } }) => {
        // Lis les données de notre cache pour cette requête.
        const data = store.readQuery({ query: TAGS_QUERY })
        // Ajoute notre tag de la mutation à la fin
        data.tags.push(addTag)
        // Ecris nos données dans le cache.
        store.writeQuery({ query: TAGS_QUERY, data })
      },
      // UI optimiste
      // Sera traité comme un "faux" résultat dès que la demande sera faite
      // afin que l'interface utilisateur puisse réagir rapidement et que l'utilisateur soit heureux
      optimisticResponse: {
        __typename: 'Mutation',
        addTag: {
          __typename: 'Tag',
          id: -1,
          label: newTag,
        },
      },
    }).then((data) => {
      // Résultat
      console.log(data)
    }).catch((error) => {
      // Erreur
      console.error(error)
      // On restore l'état initial
      this.newTag = newTag
    })
  },
},
```

## Exemple côté serveur

```js
export const schema = `
type Tag {
  id: Int
  label: String
}

type Query {
  tags: [Tag]
}

type Mutation {
  addTag(label: String!): Tag
}

schema {
  query: Query
  mutation: Mutation
}
`

// Générateur de mots aléatoires
import faker from 'faker'

// Générons des tags
var id = 0
var tags = []
for (let i = 0; i < 42; i++) {
  addTag(faker.random.word())
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
  Mutation: {
    addTag(root, { label }, context) {
      console.log(`adding tag '${label}'`)
      return addTag(label)
    },
  },
}
```
