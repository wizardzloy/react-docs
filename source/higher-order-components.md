---
title: Connecting data
order: 3
---

Now that we have created an `ApolloClient` instance and attached it to our UI tree with `ApolloProvider`, we can start using the main function of `react-apollo`: adding GraphQL functionality to our UI components.

<h2 id="graphql">graphql</h2>

The `graphql` container is the recommended approach for fetching data or making mutations. It is a [Higher Order Component](https://facebook.github.io/react/blog/2016/07/13/mixins-considered-harmful.html#subscriptions-and-side-effects) for providing Apollo data to a component, or attaching mutations.

The basic usage of `graphql` is as follows:

```js
import React, { Component } from 'react';
import { graphql } from 'react-apollo';
import gql from 'graphql-tag';

// MyComponent is a "presentational" or apollo-unaware component,
// It could be a simple React class:
class MyComponent extends Component {
  render() {
    return <div>...</div>;
  }
}
// Or a stateless functional component:
const MyComponent = (props) => <div>...</div>;

// Initialize GraphQL queries or mutations with the `gql` tag
const MyQuery = gql`query MyQuery { todos { text } }`;
const MyMutation = gql`mutation MyMutation { addTodo(text: "Test 123") }`;

// We then can use `graphql` to pass the query results returned by MyQuery
// to MyComponent as a prop (and update them as the results change)
const MyComponentWithData = graphql(MyQuery)(MyComponent);

// Or, we can bind the execution of MyMutation to a prop
const MyComponentWithMutation = graphql(MyMutation)(MyComponent);
```

If you are using [ES2016 decorators](https://medium.com/google-developers/exploring-es7-decorators-76ecb65fb841#.nn723s5u2), you may prefer the decorator syntax:

```js
import React, { Component } from 'react';
import { graphql } from 'react-apollo';

@graphql(MyQuery)
@graphql(MyMutation)
class MyComponent extends Component {
  render() {
    return <div>...</div>;
  }
}
```

In this guide, we won't use the decorator syntax to make the code more familiar, but you can always use it if you prefer.

For details about the `graphql` higher-order-component, read about how to use it with [queries](/react/queries) and [mutations](/react/mutations).

<h2 id="withApollo">withApollo</h2>

`withApollo` is a simple higher order component which provides direct access to your `ApolloClient` instance as a prop to your wrapped component. This is useful if you want to do custom logic with apollo, such as calling one-off queries, without using the `graphql` container.

```js
import React, { Component } from 'react';
import { withApollo } from 'react-apollo';
import ApolloClient from 'apollo-client';

class MyComponent extends Component { ... }
MyComponent.propTypes = {
  client: React.PropTypes.instanceOf(ApolloClient).isRequired;
}

const MyComponentWithApollo = withApollo(MyComponent);

// or using ES2016 decorators:
@withApollo
class MyComponent extends Component { ... }
```

<h2 name='with-ref'>withRef</h2>

If you need to get access to the instance of the wrapped component, you can use `withRef` in the options.
This will allow a `getWrappedInstance` method on the returned component which will return the wrapped instance.

```js
import React, { Component } from 'react';
import { graphql } from 'react-apollo';

class MyComponent extends Component { ... }

const MyComponentWithUpvote = graphql(Upvote, {
  withRef: 'true'
})(MyComponent);

// MyComponentWithUpvote.getWrappedInstance() returns MyComponent instance
```