---
layout: post
title: React Basic 3/7
categories: [Tech]
---

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


