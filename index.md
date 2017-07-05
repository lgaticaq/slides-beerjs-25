title: Beerjs #18
author:
  name: Leonardo Gatica
  twitter: lgaticaq
  url: https://about.me/lgatica
  email: lgatica@protonmail.com
output: index.html
theme: juanbrujo/cleaver-beerjs
style: style.css
controls: true

--

<h1>Demo de Express + mongoose + GraphQL</h1>

--

# Dependencias

```shell
npm i express express-graphql graphql mongoose
```

--

# Model

```javascript
const mongoose = require('mongoose')
const Schema = new mongoose.Schema({
  address: String,
  commune: String,
  country: String,
  latitude: Number,
  longitude: Number,
  name: String,
  phone: String,
  region: String
})
module.exports = mongoose.model('Bombero', Schema)
```

--

# Type

```javascript
const { GraphQLString, GraphQLFloat, GraphQLObjectType } = require('graphql')
module.exports = new GraphQLObjectType({
  name: 'Bombero',
  fields: () => ({
    address: { type: GraphQLString },
    commune: { type: GraphQLString },
    country: { type: GraphQLString },
    latitude: { type: GraphQLFloat },
    longitude: { type: GraphQLFloat },
    name: { type: GraphQLString },
    phone: { type: GraphQLString },
    region: { type: GraphQLString }
  })
})
```

--

# Schema

```javascript
const { GraphQLSchema, GraphQLObjectType } = require('graphql')
const { bomberos } = require('./mutations')
const Query = new GraphQLObjectType({
  name: 'BomberoSchema',
  fields: () => ({
    bomberos: bomberos
  })
})
module.exports = new GraphQLSchema({ query: Query })
```

--

# Mutations

```javascript
const { GraphQLList, GraphQLString } = require('graphql')
const Bombero = require('./model')
const BomberoType = require('./type')
const bomberos = {
  type: new GraphQLList(BomberoType),
  args: {
    commune: { type: GraphQLString },
    region: { type: GraphQLString },
    country: { type: GraphQLString }
  },
  resolve: (root, args) => Bombero.find(args).exec()
}
module.exports = { bomberos: bomberos }
```

--

# app

```javascript
const express = require('express')
const graphqlHTTP = require('express-graphql')
const schema = require('./schema')
const app = express()
...
app.use('/graphql', graphqlHTTP(req => ({schema, graphiql: true})))
...
```

--

# Test

```bash
curl -XGET -H "Content-Type:application/graphql" \
  -d 'query { bomberos { name } }' \
  https://example-graphql-glmgpqnytt.now.sh/graphql
```

```json
{
  "data": {
    "bomberos": [
      ...
      { "name": "Cuerpo de Bomberos de Romeral" },
      ...
    ]
  }
}
```

--

# Test con filtro

```bash
curl -XGET -H "Content-Type:application/graphql" \
  -d 'query { bomberos(region: "X Región") { name } }' \
  https://example-graphql-glmgpqnytt.now.sh/graphql
```

```json
{
  "data": {
    "bomberos": [
      ...
      { "name": "Cuerpo de Bomberos de San Juan De La Costa" },
      ...
    ]
  }
}
```
