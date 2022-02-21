---
layout: post
title: React Basic 4/7
categories: [Tech]
---

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


