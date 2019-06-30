# Noact Overview & Initial Implementation Spec
> NoAct is a react based framework for writing backend servers.

### What is NoAct supposed to be?

NoAct's name comes from the idea of combining NOde with reACT.

NoAct provides a way for building backend applications, using the same knowledge and tools we use for our frontend apps. It will include components for rendering Servers/Routes and passing props to them using the same JSX we love; NoAct will handle everything.

It's somehow like react-native, but for servers.

Imagine you could do something like this:

| :warning:Caution: API is not finalized yet! |
| --- |
```jsx
import React from 'react';
import NoAct, { HTTPServer } from 'noact';

function App() {
    <HTTPServer port={3001}>
        <Route path="/hello/:name">
            {match => { 
              if(match.path.name)
                return <Response>Hello {match.path.name}</Response>
              else
                return <Response status={400}>You should provide a name</Response>
            }}
        </Route>
        <Route path="/count">
            {() => (<MyDynamicResponse />)}
        </Route>
    </HTTPServer>    
}

function MyDynamicResponse() {
    const [count, setCount] = React.useState(0);
    React.useEffect(() => setCount(c => c + 1));
    return <Response>You are {count}th person querying this address</Response>
}

NoAct.render(<App />);
```
This is what NoAct tries to make possible.