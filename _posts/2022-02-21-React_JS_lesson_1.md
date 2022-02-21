---
layout: post
title: React Basic 1/7
categories: [Tech]
---

**npx**

npx is a package runner that gets installed when one installs node.

`npx create-react-ap <project-name>`

**npm**

npm is used to install the package globally using,

`npm install create-react-app -g`

then,

`create-react-app <project-name>`



-------------------------------------------------------

Typically one need not touch the index.html file in the public folder inside the project folder.

Specifically the `div` tag with `id=root`,in the `body` tag is the important tag in index.html file.

----------------

The starting point of our app is `index.js`

Here we specify the root component and DOM element which will be controlled by react.

**Components**

Describe a part of the UI. They are reusable and can be nested inside other components.

Components in ReactJs:

Receive: Properties (props)

Returns: Html (JSX) that describe the UI



**Component Types**



1. Stateless Functional component 

   Example: JavaScript Functions

   ```react
   function Hello(props){
       return (<h1> Hello, {props.name}</h1>;)
   }
   ```

2. Stateless Class component

   Class extends Component class from react library that contains a render method which returns an HTML

   Example:

   ```react
   class Hello extends React.Component {
       render(){
           return (<h1> Hello, {this.props.name}</h1>;)
       }
   }
   ```



**Create Components** (ComponentName.js)

```react
import React from 'react'

function ComponentName(){
    return <h1> Hello, Jeet</h1>;
}

export default ComponentName
```

**Connect Component to main App (App.js)**

```javascript
import ComponentName from './folder_path/ComponentName'
```

Use `ComponentName` as a custom html tag.

```react
class App extends Component {
    render(){
        return (
        <div className="App"> 
            <ComponentName> </ComponentName>
        </div>
        )
    }
}
```



Rewriting ComponentName.js using arrow function syntax (ES6 convention):

```javascript
import React from 'react'

const ComponentName = () => <h1> Hello, Jeet</h1>;

export default ComponentName
```

----------------

**Class Components**

Components in ReactJs:

Receive: Properties (props)

Returns: Html (JSX) that describe the UI

Also: An internal state

**Create *Class* Component**

Whenever we want to create a class component, we need to include *2* imports: React and Component class from react

`import React, { Component } from 'react'`

For a class to become React component, we need to include *2* simple steps:

1. It should extend the Component class from react
2. Class has to implement a render method which will return Null of some html

```react
import React, { Component } from 'react'

class Welcome extends Component {
    render() {
        return <h1> Class Component </h1>
    }
}

export default Welcome
```

**Connect *Class* Component to main App (App.js)**

```react
import Welcome from './folder_path/Welcome'
```

Use `ComponentName` as a custom html tag.

```react
class App extends Component {
    render(){
        return (
        <div className="App"> 
            <Welcome> </Welcome>
        </div>
        )
    }
}
```

*Ref:*

1. [Codevolution](https://www.youtube.com/playlist?list=PLC3y8-rFHvwgg3vaYJgHGnModB54rxOk3)


