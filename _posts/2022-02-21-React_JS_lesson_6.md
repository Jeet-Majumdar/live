---
layout: post
title: React Basic 6/7
categories: [Tech]
---

**Higher Order Component - HOC**

A pattern where a function takes a component as an argument and returns a new component.

In code:

```react
const NewComponent = higherOrderComponent( originalComponent )
```

Example:

`const IronMan = withSuit( TonyStark )`



Lets create our HOC

*withCounter.js*

```react
import React from 'react'

const UpdatedComponent = (OriginalComponent, incrementNumber) => {
    class NewComponent extends React.Component {
        
        constructor(props){
            super(props)
            
            this.state = {
                count: 0
            }
        }
        
        incrementCount = () => {
            this.setState(prevState => {
                return {count: prevState.count + incrementNumber}
            })
        }
        
        render() {
            return<OriginalComponent name="Jeet" count={this.state.count} incrementCount={this.incrementCount} {...this.props} />
        }
    }
    return NewComponent
}

export default UpdatedComponent
```

*HoverCounter.js*

```react
import React, { Component } from 'react'
import UpdatedComponent from 'withCounter'

class HoverCounter extends Component {
    
    render(){
        const { name, count, incrementCount } = this.props
        return (
        	<h2 onMouseOver={incrementCount}>
                {name} Hovered {count} times
        	</h2>
        )
    }
}

export default UpdatedComponent(HoverCounter, 5)
```



**Render Props**

The term "render props" refers to a technique for sharing code beween React components using a *prop whose value is a function*

Example 1:

*App.js*

```react
import React, { Component } from 'react'
import './App.css'
import ClickCounterTwo from './Component/ClickCounterTwo'
import User from './Components/User'

class App extends Component {
    render(){
        return (
        	<div className="App">
            	<ClickCounterTwo />
                <User name={(isLoggedIn) => isLoggedIn ? 'Viswas' : 'Guest'} />
            </div>
        )
    }
}

export default App
```



*User.js*

```react
import React, { Component } from 'react'

class User extends Component {
    
    render(){
        return (
        	<div>
            	{this.props.name(false)}
            </div>
        )
    }
}

export default User
```



Example 2:

Create a props with the name *render* and just define the function arguments

*Counter.js*

```react
import React, { Component } from 'react'

class Counter extends Component {
    
    construnctor(props){
        super(props)
        this.state = {
            count: 0
        }
    }
    
    imcrementCount = () => {
        this.setState(prevState => {
            return { count: prevState.count + 1}
        })
    }
    
    render(){
        return(
            <div>
                {this.props.render(this.state.count, this.incrementCount)}
            </div>
            )
    }
}

export default Counter
```

Now create the function with the same name *render* i,e Render props that will use these parameters:

*App.js*

```react
import React, { Component } from 'react'
import './App.css'
import ClickCounterTwo from './Component/ClickCounterTwo'
import Counter from './Components/Counter'

class App extends Component {
    render(){
        return (
        	<div className="App">
            	<Counter render={(count, incrementCount) => (<ClickCounterTwo count={count} incrementCount={incrementCount} />) } 
                    />
            </div>
        )
    }
}

export default App
```

*ClickCounterTwo.js*

```react
import React, { Component } from 'react'

class App extends Component {
    render(){
        const { count, incrementCount } = this.props
        return <button onClick={incrementCount}> Clicked {count} times </button>
    }
}

export default ClickCountTwo
```



**Context**

Context provides a way to pass data through the component tree without having to pass props down manually at every level.

1. Create the Context

2. Provide a context value

3. Consume the context value



Wrap the root Component in a Provider Context, and then use it in any child Component using Consumer Context

AppComponent (source)

​	C

​		E

​			F  (target)

*userContext.js*

```react
import React from 'react'

const UserContext = React.createContext()
// to provide a default value to the contexts, use
// const UserContext = React.createContext("dafault_value")

const UserProvider = UserContext.Provider
const UserConsumer = UserContext.Consumer

export { UserProvider, UserConsumer }
```

*App.js*

```react
import React, { Component } from 'react'
import './App.css'
import ComponentC from './Components/ComponentC'
import { UserProvider } from './Components/userContext'

class App extends Component {
    render() {
        return (
        	<div className="App">
            	<UserProvider value="Jeet">
                	<ComponentC />
                </UserProvider>
            </div>
        )
    }
}
```

*ComponentF.js*

```react
import React, { Component  } from 'react'
import { UserConsumer } from './userContext'

class ComponentF extends Component {
    render(){
        return(
        	<UserConsumer>
            	{
                    (username) => {
                        return <div>Hello {username}</div>
                    }
                }
            </UserConsumer>
        )
    }
}
export default ComponentF
```

Intermediate Components:

*ComponentC.js*

```react
import React, { Component  } from 'react'
import ComponentE from './ComponentE'

class ComponentC extends Component {
    render(){
        return <ComponentE />
    }
}
export default ComponentC
```

*ComponentE.js*

```react
import React, { Component  } from 'react'
import ComponentF from './ComponentE'

class ComponentE extends Component {
    render(){
        return <ComponentF />
    }
}
export default ComponentE
```



For Multiple Contexts:

```react
function Context(){
    return(
    	<ThemeContext.Consumer>
        	{theme => (
            	<UserContext.Consumer>
                	{user => (
                    	<ProfilePage user={user} theme={theme} />
                    )}
                </UserContext.Consumer>
            )}
        </ThemeContext.Consumer>
        );
}
```

*Ref:*

1. [Codevolution](https://www.youtube.com/playlist?list=PLC3y8-rFHvwgg3vaYJgHGnModB54rxOk3)


