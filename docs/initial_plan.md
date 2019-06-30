# Noact Overview & Initial Implementation Spec

> NoAct is a react based framework for writing backend servers.

### What is NoAct supposed to be?

NoAct's name comes from the idea of combining NOde with reACT.  
NoAct provides a way for building backend applications, using the same knowledge and tools we use for our frontend apps. It will include components for rendering Servers/Routes and passing props to them using the same JSX we love; NoAct will handle everything.  
It's somehow like react-native, but for servers.  
Imagine you could do something like this:

| :warning:Caution: API is not finalized yet! |
| ------------------------------------------- |


```jsx
import React from 'react';
import NoAct, { HTTPServer } from 'noact';

function App() {
  <HTTPServer port={3001}>
    <Route path="/hello/:name">
      {match => {
        if (match.path.name)
          return <Response>Hello {match.path.name}</Response>;
        else return <Response status={400}>You should provide a name</Response>;
      }}
    </Route>
    <Route path="/count">{() => <MyDynamicResponse />}</Route>
  </HTTPServer>;
}

function MyDynamicResponse() {
  const [count, setCount] = React.useState(0);
  React.useEffect(() => setCount(c => c + 1));
  return <Response>You are {count}th person querying this address</Response>;
}

NoAct.render(<App />);
```

This is what NoAct tries to make possible.

### React in backend AKA NoAct

In this part, I'll try to discuss some concepts and APIs of react and their meanings in the backend realm.

#### React.createElement

It's the same as the original createElement, creating an _object_ representation of what the user describes in JSX and should get rendered.

#### React.Component

Logical isolation of a single part of our backend program. We will favor functional components over class components, because of it's cleaner more simple syntax.

#### React.memo

Provides a simple way to cache some requests. For example, consider the following code:

```jsx
const UserResponse = React.memo(({ id }) => {
  const user = Some_fictional_get_user_from_database(id);
  return <Response>{JSON.stringify(user)}</Response>;
});

function UserRoutes() {
  return (
    <>
      <Route path="/:id">{match => <UserResponse id={match.id} />}</Route>
    </>
  );
}
```

It wont query the database again for second consecutive request to the '/:id' path with the same id.  
Although, original React. The memo provides a single layer of cache, but we should find a way to provide many more layers.  
Also, I see some difficulties with cache invalidation. Maybe the Suspense/react-cache will be more suitable for this kind of use cases as it provides ways for both having multiple layers of cache and custom cache invalidation logic.

#### React.Fragment

Same as original react apps, used for having multiple siblings in a jsx without a parent element. Does not affect the outcome.

#### React.createRef/React.forwardRef

What would we have a ref for? When is it needed? Where can it help us in the backend?  
For example, what we achieve by having reference to the responses passed to the clients? Cache invalidation? Maybe. Does a reference to a response equals the user's session? How can it help? I don't know.  
These are things I believe need more planning and considerations.  
So this part is a TODO.

### Implementation

The implementation details are not clear to me, either. We probably should use something like what the react-native did, defining new importable components, with the corresponding renderer which knows how to interact with different aspects of a backend application.
We probably should re-use the reconciler part of the react (at least for now) and play with the renderer parts. Here are some excellent resources I think worth mentioning:

https://blog.atulr.com/react-custom-renderer-1/  
https://blog.atulr.com/react-custom-renderer-2/  
https://blog.atulr.com/react-custom-renderer-3/  
nothing is better than the code itself: [ReactDOM](https://github.com/facebook/react/tree/master/packages/react-dom), [ReactDOMHostConfig](https://github.com/facebook/react/blob/master/packages/react-dom/src/client/ReactDOMHostConfig.js)

And for the backend part, I think before facing limitations, we should use the express package itself under the hood.
