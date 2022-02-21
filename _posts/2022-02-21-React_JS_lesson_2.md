---
layout: post
title: React Basic 2/7
categories: [Tech]
---

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


