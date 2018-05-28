# graphql-resolvers-chain
`graphql-resolvers-chain` is a framework/server agnostic resolvers creation tool for GraphQL

This project provide a **middleware**-like approach to GraphQL servers
enabling the composition of resolvers chains through a simple and expressive API.

## Installing

#### npm
```
npm i graphql-resolvers-chain --save
```

#### yarn

```
yarn add graphql-resolvers-chain
```

## Usage
### Create your first resolver using the `chain`, `protect` or `apply` api

#### `chain` api

Create a new resolver chaining the ones in the supplied array. *The order matters from left to right*.
The last resolver being the one return the final expect value.
```chain([1, 2, 3, 4])``` 

```javascript
import { chain } from 'graphql-resolvers-chain';
import { isAuthenticated, isAdmin, isTopContributer } from './auth/resolvers';
import * as UserResolvers from './user/resolvers';

const getMe = chain([isAuthenticated, UserResolvers.getMe])

const Query = {
  getMe
}

const resolvers = {
  Query,
}
```

#### `apply` api
Applies all the `resolvers` to all of those in the `to` object.
- `resolvers` (*The order matters from left to right*)
```javascript
const allUserResolvers = apply({ resolvers: [isAuthenticated], to: UserResolvers })
const Query = {
  ...allUserResolvers
}
```

#### `protect` api
The protect key work take three arguments:
- `it` (Optional) a resolver function, if supplied `protect` returns a new resolver function
- `all`(Optional) an object with resolvers, if supplied `protect` returns an object with the exact same resolvers names as keys
- `using` a list of resolvers to apply(*The order matters from left to right*).

```javascript
const getMe = protect({
  it: UserResolvers.getMe,
  using: [isAuthenticated]
});

const adminResolvers = protect({
  all: { PostResolvers.deletePost, PostResolvers.correctPost },
  using: [isAuthenticated, isAdmin]
});

const Query = {
  getMe
  ...adminResolvers
}
```