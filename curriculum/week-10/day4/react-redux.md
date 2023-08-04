# News Site V

Lorem ipsum

## Topics Covered / Goals
- Learn to manage global application state with redux

## Lesson

<!-- look at last year's react context lecture -->
<!-- talk about lifting up state and how complex apps could get and the possible need for redux -->
<!-- what could go wrong if not using redux -->

### Redux
> Redux is a state container for javascript apps that works alongside React( or any other front-end library ) and allows us to give our applications global state access. 

> Data added in this global state will not be affected by individual component lifecycle and will remain available for components to use throughout the app's runtime. 

> The whole global state of your app will be stored in an object tree inside a single store. 

> The only way to change the state tree is to create an action, an object describing what happened, and dispatch it to the store. To specify how state gets updated in response to an action, you write pure reducer functions that calculate a new state based on the old state and the action.

<!-- install redux -->
```sh
npm install @reduxjs/toolkit

npm install react-redux
```

<!-- redux setup and scaffolding -->

<!-- determine what the state will look like  -->

<!-- implement a simple action -->

<!-- implement api call in redux actions -->

<!-- call action to populate data -->

<!-- access data in components to print -->
