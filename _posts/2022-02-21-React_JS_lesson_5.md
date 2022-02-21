---
layout: post
title: React Basic 5/7
categories: [Tech]
---

**Fragment**

In react, adjacent JSX elements must be wrapped in an enclosing tag. 

Example:

```react
import React from 'react'

function ComponentName(){
    return ( 
        <h1> Hello, Jeet</h1>; 
        <p> This is a description </p>
        )
}

export default ComponentName
```

The above script WILL NOT WORK.

However, the script below will work. 

```react
import React from 'react'

function ComponentName(){
    return ( 
        <div>
        	<h1> Hello, Jeet</h1>; 
        	<p> This is a description </p>
        </div>
        )
}

export default ComponentName
```

To prevent addition of extra node in DOM, we can use `React.Fragment`

```react
import React from 'react'

function ComponentName(){
    return ( 
        <React.Fragment>
        	<h1> Hello, Jeet</h1>; 
        	<p> This is a description </p>
        </React.Fragment>
        )
}

export default ComponentName
```

*key* attribute is the only attribute that can be passed to react fragment.

Shortcut for `<React.Fragment>` is `<>` and that of  `</React.Fragment>` is `</>`





**Refs**

1. To create a *ref* we use `React.createRef()` method.
2. Attach this *ref* to our input element of our render method. 
3. Use it

```react
import React, {Component} from 'react'

class RefsDemo extends Component {
    constructor(props) {
        super(props)
        this.inputRef = React.createRef()
    }
    
    componentDidMount(){
        this.inputRef.current.focus()
        console.log(this.inputRef)
    }
    
    render(){
        return(
        	<div>
                <input type = "text" ref={this.inputRef}/>
            </div>
        )
    }
}
```

**Refs with Class Components**

Call a child function from a parent function

*Input.js*

```react
import React, {Component} from 'react'

class RefsDemo extends Component {
    constructor(props) {
        super(props)
        this.inputRef = React.createRef()
    }
    
    focusInput(){
        this.inputRef.current.focus()
    }
    
    render(){
        return(
        	<div>
                <input type = "text" ref={this.inputRef}/>
            </div>
        )
    }
}
```

*FocusInput.js*

```react
import React, { Component } from 'react'
import Input from './Input'

class FocusInput extends Component {
    
    constructor(props){
        supur(props)
        this.componentRef = React.createRef()
    }
    
    clickHandler = () => {
        this.componentRef.current.focusInput()  // focusInput is the fucntion in 'Input.js'
    }
    
    render(){
        return (
        	<div>
                <Input ref={this.componentRef} />
                <button onClick={this.clickHandler}> Focus Input </button>
            </div>
        )
    }
}
```

- Forwarding Refs

  (To allow the parent component to directly reference the native *input* element with some props loaded)

*FRInput.js*

```react
import React from 'react'

const FRInput = React.forwardRef((props, ref) => {
    return (
    	<div>
        	<input type="text" ref={ref} />
        </div>
    )
}) 

export default FRInput
```

The child element is basically forwarding this reference.

*FRParentInput.js*

```react
import React, { Component } from 'react'
import FRInput from './FRInput'

class FRParentInput extends Component {
    
    constructor(props){
        supur(props)
        this.inputRef = React.createRef()
    }
    
    clickHandler = () => {
        this.inputRef.current.focus()
    }
    
    render() {
        return(
        	<div>
            	<FRInput ref={this.inputRef}/>
                <button onClick={this.clickHandler}>Focus Input </button>
            </div>
        )
    }    
}
```



**Portals**

Usually everything you create, goes under the App component with `id='root'`. 

To create a  different node and attach components under that new node, react provides portals.

React Portals lets us create any component under any DOM node.

In the *index.html*,  create a new div under the div with id = root. 

`<div id='portal-root'> </div>` 

Now create a functional component to be added under this new div.

```react
import React from 'react'
import ReactDOM from 'react-dom'

function PortalDemo() {
    return ReactDOM.createPortal(
        <h1>
            Portals Demo
        </h1>, 
        document.getElementById('portal-root')
    )
}
```

*Ref:*

1. [Codevolution](https://www.youtube.com/playlist?list=PLC3y8-rFHvwgg3vaYJgHGnModB54rxOk3)


