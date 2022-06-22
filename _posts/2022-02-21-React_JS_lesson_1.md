---
layout: post
title: React Basic 1/7 2/7 3/7
categories: [Tech]
---

### 1/7

**npx**

npx is a package runner that gets installed when one installs node.

`npx create-react-app <project-name>`

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

--------------



### 2/7

**JSX**

Instead of making our own element manually we can use React elements

```react
import React from 'react'

const Hello = () => {
    return React.createElement('div', 'null', React.createElement('h1', 'null', 'Hello World'))
}

export default Hello
```

React.createElement can take in any number of recursively made element in it.

The 3rd parameter of React.createElement takes in {key: 'value'} pairs. These will be the attribute to the element that it creates.

```react
import React from 'react'

const Hello = () => {
    return React.createElement('div', {id: 'hello', class: 'dummyClass'}, React.createElement('h1', 'null', 'Hello World'))
}

export default Hello
```



**Props**

```react
import React from 'react'

const Display = (props) => { 
    console.log(props)
    return <h1> Hello, {props.name}</h1>; 
}

export default ComponentName
```

```react
class App extends Component {
    render(){
        return (
        <div className="App"> 
            <Display name='Jeet'/>
            <Display name='Peter'/>
            <Display name='Jane'/>
        </div>
        )
    }
}
```

We can also retrieve children of a component using props.children:

```react
import React from 'react'

const Display = (props) => { 
    console.log(props)
    return (
        <div>
        <h1> Hello, {props.name}</h1>; 
        {props.children}
        </div>
        
}

export default ComponentName
```

```react
class App extends Component {
    render(){
        return (
        <div className="App"> 
            <Display name='Jeet'> This is Jeet </Display>
            <Display name='Peter'> This is Peter </Display>
            <Display name='Jane'> This is Jane </Display>
        </div>
        )
    }
}
```



**Props *vs* State**

Props gets passed to the component wherease state is managed within the component.

Props are declared in function parameter, variables are declared in the function body of a state

Props are immutable by the children, state is managed within the component and thus the component has full control to change the state.

the only place where `this.state` can be used directly is the constructor, in all other places it has to be used in `this.setState`

```react
import React, { Component } from 'react'

class Message extends Component {
    constructor() {
        super()
        this.state = {
            message: 'Welcome here!'
        }
    }
    render(){
        return <h1> {this.state.message} </h1>
    }
}
export default Message
```

Use a button to change the state:

```react
import React, { Component } from 'react'

class Message extends Component {
    constructor() {
        super()
        this.state = {
            message: 'Welcome here!'
        }
    }
    changeMessage() {
        this.setState({
            message: 'Thank You!'
        })
    }
    render(){
        return (
            <div>
            <h1> {this.state.message} </h1>
            <button onClick={()=> this.changeMessage()}> ClickHere </button>
			</div>
        )
    }
}
export default Message
```

Destructuring props and state:

```react
const {prop1, prop2} = this.props
const {state1, state2} = this.state
```



Whenever you want to update something based on the previous state, you need to pass a function in the 1st argument of `setState` method instead of an object, and another function in the 2nd argument which is a callback function.

Here is how you can use callback function to generate fibonacci numbers on the click of a button:

```react
import React, { Component } from 'react'

class Counter extends Component {
    constructor(props) {
        super(props)
    
        this.state = {
             count1: 0,
             count2: 1,
             count3: 0
        }
    }
    
    increment(){
        this.setState( (previousState)=> ({
            count3: previousState.count1 + previousState.count2,
        }),  
        () => {this.state.count1  = this.state.count2; this.state.count2  = this.state.count3}
        )
        
    }

    render() {
        return (
            <div>
               <div> { this.state.count3 } </div>
               <button onClick={ () => this.increment() }> Increment </button>
            </div>
        )
    }
}

export default Counter
```

*Ref:*

1. [Codevolution](https://www.youtube.com/playlist?list=PLC3y8-rFHvwgg3vaYJgHGnModB54rxOk3)

-------



### 3/7

**Styling using CSS**

*Stylesheet.js*

```react
import React from 'react'
import './myStyles.css'

function Stylesheet(props){
    let classname = props.primary ? 'primary' : ''
    return (
    <div>
        <h1 className={`${classname} font-xl`}> Stylesheets </h1>
    </div>
    )
}

export default Stylesheet
```

*myStyles.css*

```css
.primary {
    color: orange;
}

.font-xl {
    font-size: 72px;
}
```

**Styling using inline style**

*Inline.js*

```react
import React from 'react'

const heading =  {
    fontSize: '72px',
    // HAVE to use camelCase for the corresponding css style name.
    color: 'blue'
}

function Inline(){
    return (
    	<div>
            <h1 style={heading}>Inline</h1>
    	</div>
    )
}

export default Inline
```

**Styling as a CSS Module**

(Available for React scripts V2 or higher)

```react
import React from 'react'
import cssmodule from './myStyles.module.css'

const heading =  {
    fontSize: '72px',
    // HAVE to use camelCase for the corresponding css style name.
    color: 'blue'
}

function Inline(){
    return (
    	<div>
            <h1 style={cssmodule.success}>Inline</h1>
    	</div>
    )
}

export default Inline
```

*myStyles.module.css*

```css
.success {
    color: 'green'
}
```

----------------



**Form Handling**

```react
this.state = {
    email: ''
}

this.changeEmailHandler = (event) => {
    this.setState({email: event.target.value})
}
```

```react
<input type='text' value={this.state.email} onChange={this.changeEmailHandler} />
```



*Form.js*

```react
import React, { Component } from 'react'

class Form from Component {
    
    constructor(props){
        super(props)
        
        this.state = {
            username: ''
        }
    }
    
    handleUsernameChange = (event) => {
        this.setState({
            username: event.target.value
        })
    }
    
    render() {
        return (
        	<form>
            	<div>
                    <label>Username</label>
                    <input type='text' 
                        value={this.state.username} 
                        onChange={this.handleUsernameChange}/>
            	</div>
            </form>
        )
    }
}

export default Form
```



For any event, we can write an eventHandler function that triggers `onChange`. We can capture these value using `event.target.value` and use `setState` to reassign the particular state. `event` is a default parameter that react generates and passes to any function that is called when an event occurs. 

*Ref:*

1. [Codevolution](https://www.youtube.com/playlist?list=PLC3y8-rFHvwgg3vaYJgHGnModB54rxOk3)
