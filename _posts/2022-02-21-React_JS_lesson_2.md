---
layout: post
title: React Basic 4/7 5/7 6/7 7/7
categories: [Tech]

---

### 4/7

**Lifecycle Methods** 

*ONLY available for a Class Component. DO NOT exist for a functional component.*

- Mounting : When an instance of a component is being created and inserted into the DOM

  *constructor, static getDerivedStateFromProps, render, componentDidMount*

- Updating: When a component is being re-rendered as a result of changes to either its props or state

  *static getDerivedStateFromProps, shouldComponentUpdate, render, getSnapshotBeforeUpdate, componentDidUpdate*

- Unmounting: When a component is being removed from the DOM

  *componentWillUnmount*

- Error Handling: When there is an error during rendering, in a lifecycle method, or in the constructor of any child component

  *static getDerivedStateFromError, componentDidCatch*



------------

**Mounting**

- *constructor(props)*
  - called whenever a new component is created
  - Initializing state and binding the event handlers
  - have to call `super(props)` which will call the base `class Constructor` and overwrite `this.state` 
  - only place where you can directly change `state` by assigning. In all other scenarios, one has to use `this.setState` 
  - Never make http requests from within a constructor
- *static getDerivedStateFromProps(props, state)*
  - Used when we want to initialize the initial `state` of a component with the data that is passed in `props` 
  - Since this is a static method, this method has no access to `this` keyword.
  - Never make http requests from within a constructor
- *render()*
  - only *required* method
  - read `props` and `state` and return the JSX
  - Never change state, or interact with DOM or make ajax calls
  - children components lifecycle methods are also executed
- *componentDidMount()*
  - called only once
  - invoked immediately after a component and all its children components have been rendered to the DOM
  - Perfect place to interact with DOM or perform any ajax calls to load data



Example:

Component A = parent.

Component B = Child of A

Mounting Lifecycle chronology:

> 1. LifecycleA constructor
> 2. LifecycleA getDerivedStateFromProps
> 3. LifecycleA render
> 4. LifecycleB constructor
> 5. LifecycleB getDerivedStateFromProps
> 6. LifecycleB render
> 7. LifecycleB componentDidMount
> 8. LifecycleA componentDidMount

--------

**Updating**

- *static getDerivedStateFromProps()*
  - called every time a component is rendered
  - set the state
  - DO NOT make HTTP request
- *shouldComponentUpdate(nextProps, nextState)*
  - dictates if the components should re-render or not
  - used for performance optimization
  - DO NOT call the *setState* method or make HTTP request
- *render()*
  - ONLY required method
  - read `props` and `state` and return the JSX
  - Never change state, or interact with DOM or make ajax calls
- *getSnapshotBeforeUpdate(prevProps, prevState)*
  - called right before the changes from the virtual DOM are to be reflected in the DOM
  - Capture some information from the DOM (Example: capture users scroll position and after the update, maintain that scroll position by performing some calculation)
  - returns *null* or a value.
  - returned value (a snapshot) will be passed as the third parameter to the next method
- *componentDidUpdate(prevProps, prevState, snapshot)* 
  - called after the render is finished in the re-render cycles
  - guaranteed to be called only once in every re-render cycle
  - can make ajax call or http requests

Updating Lifecycle chronology:

After the mounting lifecycle is completed:

> 1. LifecycleA getDerivedStateFromProps
> 2. LifecycleA shouldComponentUpdate
> 3. LifecycleA render
> 4. LifecycleB getDerivedStateFromProps
> 5. LifecycleB shouldComponentUpdate
> 6. LifecycleB render
> 7. LifecycleB getSnapshotBeforeUpdate
> 8. LifecycleA getSnapshotBeforeUpdate
> 9. LifecycleB componentDidUpdate
> 10. LifecycleA componentDidUpdate

--------

-----------

**Unmounting**

- *componentWillUnmount()*
  - this method is invoked immediately before a component is unmounted and destroyed
  - cancelling any network requests, removing event handlers, cancelling any subscriptions and also invalidating timers
  - NOT call the `setState` method

---------

**Error Handling**

- *static getDerivedStateFromError(error)*
- *componentDidCatch(error, info)*


*Ref:*

1. [Codevolution](https://www.youtube.com/playlist?list=PLC3y8-rFHvwgg3vaYJgHGnModB54rxOk3)

-----



### 5/7

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

-----



### 6/7

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

-------



### 7/7

**React and HTTP Library - axios**

Axios is one of the http libraries used to handle GET and POST request in React

*PosForm.js*

```react
import React, { Component } from 'react'
import axios from 'axios'
class PostForm extends Component {
	constructor(props) {
		super(props)

		this.state = {
			userId: '',
			title: '',
			body: ''
		}
	}

	changeHandler = e => {
		this.setState({ [e.target.name]: e.target.value })
	}

	submitHandler = e => {
		e.preventDefault()
		console.log(this.state)
		axios.post('https://jsonplaceholder.typicode.com/posts', this.state)
			.then(response => {
				console.log(response)
			})
			.catch(error => {
				console.log(error)
			})
	}

	render() {
		const { userId, title, body } = this.state
		return (
			<div>
				<form onSubmit={this.submitHandler}>
					<div>
						<input
							type="text"
							name="userId"
							value={userId}
							onChange={this.changeHandler}
						/>
					</div>
					<div>
						<input
							type="text"
							name="title"
							value={title}
							onChange={this.changeHandler}
						/>
					</div>
					<div>
						<input
							type="text"
							name="body"
							value={body}
							onChange={this.changeHandler}
						/>
					</div>
					<button type="submit">Submit</button>
				</form>
			</div>
		)
	}
}

export default PostForm
```

*PostList.js*

```react
import React, { Component } from 'react'
import axios from 'axios'
class PostList extends Component {
	constructor(props) {
		super(props)

		this.state = {
      posts: [],
      errorMsg: ''
		}
	}

	componentDidMount() {
		axios.get('https://jsonplaceholder.typicode.com/posts')
			.then(response => {
				console.log(response)
				this.setState({ posts: response.data })
			})
			.catch(error => {
        console.log(error)
        this.setState({errorMsg: 'Error retrieving data'})
			})
	}

	render() {
		const { posts, errorMsg } = this.state
		return (
			<div>
				List of posts
				{posts.length
					? posts.map(post => <div key={post.id}>{post.title}</div>)
          : null}
        {errorMsg ? <div>{errorMsg}</div> : null}
			</div>
		)
	}
}

export default PostList
```

*App.js*

```react
import React, { Component } from 'react'
import './App.css'
import PostList from './components/PostList'
import PostForm from './components/PostForm'

class App extends Component {
	render() {
		return (
			<div className="App">
				<PostForm />
				{/* <PostList /> */}
			</div>
		)
	}
}

export default App
```

*Ref:*

1. [Codevolution](https://www.youtube.com/playlist?list=PLC3y8-rFHvwgg3vaYJgHGnModB54rxOk3)

