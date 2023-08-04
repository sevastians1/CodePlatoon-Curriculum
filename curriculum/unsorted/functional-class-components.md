# Functional Components vs Class Components

## Topics Covered / Goals
- More practice with React
- JSX Syntax & Inline / Conditional rendering options
- Functional Components -vs- Class Components

## Lesson

- [React Slides](../page-resources/React_Intro.pdf)

### React Practice

To get more practice with React, let's walk through a challenge called [Mute Button](https://github.com/quebecplatoon/react-mute-button) together. This will help us get a refresher on creating components, and using state and prop values in React.

To summarize the challenge's readme, we are trying to toggle between two different images on the page based on a user's clicks. The images can be found in the `icons` folder of the challenge.

After cloning our app down and opening the project, we are going to make it a new React app by running `create-react-app mute-button-app`. Once our React project is initialized, let's move the `icons` folder into the `mute-button-app/src` folder. 

Starting out, we're going to add a few items to our `src/App.js` file:

```javascript
// App.js

import "./App.css"
import { useState } from "react"

function App() {
  // states
  const [isMuted, setIsMuted] = useState(false) // create a state value (initialized to false), and an updating function for the new state value

  // render
  return (
    <div className="App">
      <h2>Mute Button App</h2>
      <hr />
    </div>
  )
}

export default App
```

Next, we will create a function called `toggleMute` that we'll attach to an event handler `onClick` for the button:

```javascript
// App.js

import "./App.css"
import { useState } from "react"

function App() {
  // states
  const [isMuted, setIsMuted] = useState(false)

  // event handlers
  const toggleMute = () => { // add this function
    let newMuteState = !isMuted
    console.log("setting new mute state value:", newMuteState)
    setIsMuted(newMuteState)
  }

  // render
  return (
    <div className="App">
      <h2>Mute Button App</h2>
      <hr />
      <button onClick={ toggleMute }> {/* set the new event handler here */}
        Toggle Mute
      </button>
    </div>
  )
}

export default App
```

Let's run this application and see what behavior we have implemented. We should see the new value that we're setting for the `isMuted` state being printed out to the console every time we click the "Toggle Mute" button.

Now, let's create another component to handle the mute button. Similar to Django, if we have a bunch of the same things (i.e., models, migrations, templates, views), then it's best to organize all the files by creating new separate folders. For us, we'll create a `components` folder under `src`. Note that we can name this new folder anything that we want to (i.e. it's not like Django where we have to use specific names for our folders here).

We're going to create a new component, `components/MuteButton.js`, with the following code:

```javascript
// MuteButton.js

function MuteButton(props) {
  // render
  return (
    <button>{ props.isMuted ? "ON" : "OFF" }</button>
  )
}

export default MuteButton
```

Notice that our component takes in `props` as the first parameter. As a reminder, `props` stands for "properties" and is an object that contains information passed down from the parent component own to this child component. In this case, we are anticipating that `MuteButton` will be a child component rendered by some parent component (in this case `App`), and that parent component will pass along the data `isMuted` down to `MuteButton`.

So with that, let's import our `MuteButton` component into `App.js` and call it in the render (the same way we would include an HTML element):

```javascript
// App.js

import "./App.css"
import { useState } from "react"
import MuteButton from "./components/MuteButton.js"

function App() {
  // states
  const [isMuted, setIsMuted] = useState(false)

  // event handlers
  const toggleMute = () => {
    let newMuteState = !isMuted
    console.log("setting new mute state value:", newMuteState)
    setIsMuted(newMuteState)
  }

  // render
  return (
    <div className="App">
      <h2>Mute Button App</h2>
      <hr />
      <MuteButton isMuted={ isMuted } />
    </div>
  )
}

export default App
```

Notice how we're passing `isMuted` down to the `MuteButton` when we render it. With the way we created it, the `MuteButton` component itself does not have any state, so it relies on `isMuted` being set at the parent component level and passed down.

However with this redesign, we've lost the click functionality! To fix this, similar to how we passed `isMuted` down as a prop value, we can also pass down our function `toggleMute` down to `MuteButton`, so that `MuteButton` call the function when it needs to. It is important to note that event handlers cannot be directly attached to React components (i.e. we can't just put attach an `onClick` to our `MuteButton` in `App`). Instead, we need to pass the entire function with the context of `App.js` down to `MuteButton`:

```javascript
// App.js

  // ...

  // inside App's render
  <MuteButton 
    isMuted={ isMuted } 
    toggleMute={ toggleMute } // new prop value being passed down
  /> 
```

The final `App.js` should now look like this:

```javascript
// App.js

import "./App.css"
import { useState } from "react"
import MuteButton from "./components/MuteButton.js"

function App() {
  // states
  const [isMuted, setIsMuted] = useState(false)

  // event handlers
  const toggleMute = () => {
    let newMuteState = !isMuted
    console.log("setting new mute state value:", newMuteState)
    setIsMuted(newMuteState)
  }

  // render
  return (
    <div className="App">
      <h2>Mute Button App</h2>
      <hr />
      <MuteButton isMuted={ isMuted } toggleMute={ toggleMute } />
    </div>
  )
}

export default App
```

Now that `toggleMute` is being passed down to our child component, we can access it via the `props` object and attach it to some `onClick` handlers. `MuteButton.js` should read something like:

```javascript
// MuteButton.js

function MuteButton(props) {
  // render
  return (
    <button onClick={ props.toggleMute }> {/* using the new props value here */}
      { props.isMuted ? "ON" : "OFF" }
    </button>
  )
}

export default MuteButton
```

At this point, we should be able to toggle between `OFF` and `ON` quite easily. Our final step is to display the image instead of the text on the page. If you haven't done so already, make sure you move the entire `icons` folder into the `src` folder. Now we can import images just like you would React components.

`MuteButton.js` should now look like this:

```javascript
// MuteButton.js

import OnIcon from "../icons/on.svg"
import OffIcon from "../icons/off.svg"

function MuteButton(props) {
  // render
  return (
    <button onClick={ props.toggleMute }>
    { 
      props.isMuted 
        ? <img src={ OffIcon } alt="AudioOff" /> 
        : <img src={ OnIcon } alt="AudioOn" />
    }
    </button>
  )
}

export default MuteButton
```

That's it! We can click the image it toggles between muted and not muted.

#### Destructuring

If we have a lot of props getting passed down, it gets ugly because we have to write `props` so many times. Because `props` is a javascript object, we can use a concept called [destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment). Let's apply this destructuring syntax within the return function of our `MuteButton` component:

```javascript
// MuteButton.js

import OnIcon from "../icons/on.svg"
import OffIcon from "../icons/off.svg"

function MuteButton(props) {
  // props
  const { isMuted, toggleMute } = props; // destructuring example
  
  // render
  return (
    <button onClick={ toggleMute }>
    { 
      isMuted 
        ? <img src={OffIcon} alt="AudioOff" />
        : <img src={OnIcon} alt="AudioOn" /> 
    }
    </button>
  )
}

export default MuteButton;
```

This makes is a bit easier to code and read. 

### JSX Syntax

Let's get more familiar with using JSX. If you recall, we can write JavaScript expressions by adding in curly brackets, `{` `}`, in our JSX code blocks. However, one big thing (that you may have noticed already) is that we can't always write raw JavaScript in our JSX snippets. The limitation for JSX is that we can only write single-line expressions... meaning we CAN NOT use such things as if-statements or for-loops inside of JSX code blocks. To do so, we either have to call a function or we can sometimes take advantage of single line expressions. 

#### Single-line Expressions

Some key single-line expression syntax that you should hopefully be familiar with by now: 
- ternary operators (single-line if-statements)
- .forEach array function (single-line for-loop)
- .map() array function (substitutes for for-loop)
- .filter() array function (substitutes for for-loop)
- .reduce() array function (substitutes for for-loop)
- functions (...if you need to write multi-line JS logic, create a new function and call it from JSX)

If you are not familiar with this yet, get familiar with them now because they can make coding a lot quicker when writing JSX! Take a look a simple example below:

- option 1: using a function with a for-loop
```javascript
function MyComponent(props) { 
  // render
  const renderData = () => {
    let elements = []

    for (let i = 0; i < props.someDataArray.length; i++) {
      elements.push(<li>{ props.someDataArray }</li>)
    }

    return elements
  }

  return (
    <ul>
      { renderData() }
    </ul>
  )
}

export default MyComponent
```

- option 2: using the .map() array function
```javascript
function MyComponent(props) { 
  // render
  return (
    <ul>
      { props.someDataArray.map(item => <li>{item}</li>) }
    </ul>
  )
}

export default MyComponent
```

Which do you think is quicker for a developer to create? Again, make use of single-line expressions when you can, or else create functions to handle more complicated / multi-line logic. 

#### Conditional rendering

We can also use JS syntax to optionally render or not render certain parts of our JSX code. The way we do this is either by using ternary operators and showing `null` when we want, or by using combinational logic. Take a look at the examples below:

```javascript
function MyComponent(props) { 
  // render
  return (
    <div>
      <p>Show this element all the time</p>
      { 
        props.showExtraStuff 
          ? <p>Show this element only if props.showExtraStuff is 'true'</p>
          : null // this will not render anything
      }
    </div>
  )
}

export default MyComponent
```

Or we can make this a bit simpler even using combinational logic:

```javascript
function MyComponent(props) { 
  // render
  return (
    <div>
      <p>Show this element all the time</p>
      { props.showExtraStuff && <p>Show this element only if props.showExtraStuff is 'true'</p> }
    </div>
  )
}

export default MyComponent
```

In the code example above, the `&&` will proceed to evaluate the second part of the expression ONLY IF the first part evaluates to true. This is known as "short-circuiting" in programming. 

### React Class-based Components

So far, we've become familiar with creating and using components with in our React applications. Specifically, we've been working with function-based components. But there is another syntactical type of components that we can create, called class-based components. As the name might imply, we can uses JavaScript class syntax instead of function syntax to define our components. **Key thing to remember:** Both function-based and class-based components achieve the EXACT same thing within a React application, just with different syntax. 

As of March 2019, the creators of React want to start migrating over to function-based components instead of class-based components for ease of development. They have very clearly stated that both are going to exist alongside each other, and so you will definitely need to understand working with both types of components. Newer projects will likely utilize functional components and older projects will use class-based components.
It's understandable that it can be a bit difficult to learn two different syntaxes, when just starting React. For our curriculum, know that we'll *mostly* be focusing on functional components, however we definitely need to be familiar with class-based components. 

#### JavaScript classes -vs- Python classes

As a refresher, let's first revisit the class syntax for creating classes in JavaScript, and compare that against Python class syntax. 

Below are example of a Python class and a JavaScript class:

```python
  class Person:
    def __init__(self, first_name, last_name): # must be called "__init__()"
      self.first_name = first_name
      self.last_name = last_name

    def get_full_name(self):
      return f"{self.first_name} {self.last_name}"

  class Student(Person): # inheritance
    def __init__(self, student_id, first_name, last_name):
      super().__init__(first_name, last_name)
      self.student_id = student_id
      
  einstein = Student(1, "Albert", "Einstein")
  print("id:", einstein.student_id, "- name:", einstein.get_full_name())
```

```javascript
  class Person {
    constructor(firstName, lastName) { // must be called "constructor()"
      this.firstName = firstName
      this.lastName = lastName
    }

    getFullName() { // notice that we DO NOT pass in the 'this' keyword
      return `${this.firstName} ${this.lastName}`
    }
  }

  class Student extends Person { // inheritance
    constructor(studentId, firstName, lastName) {
      super(firstName, lastName) // notice how we call the base constructor here
      this.studentId = studentId
    }
  }
  
  einstein = new Student(1, "Albert", "Einstein") // notice the use of the 'new' keyword
  console.log("id:", einstein.studentId, "- name:", einstein.getFullName())
```

A few things to note from the syntax shown above...
 - The `constructor()` method in JavaScript is akin to the `__init__()` instance method in Python. It is the first method that is automatically called when an instance is created. This method can take in parameters, and usually will be responsible for initializing anything that each class instance needs initialized. 
 - Instance methods in Javascript classes DO NOT take in `this` as the first parameter (unlike Python class instance methods which must take in a `self` parameter). The `this` keyword is a reserved keyword in JavaScript, and automatically accessible inside of instance methods. 
 - When creating an instance of a class in JavaScript, we must use the `new` keyword.

We aren't going to cover JavaScript OOP in much detail, but you can read more about it [here](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object-oriented_JS)


#### Creating Class-based Components

Let's convert our App and MuteButton components from above from function-based components to class-based components, to highlight the different syntax between the two:

- **class-based component version of `App.js`**
```javascript
// App.js

import "./App.css"
import { Component } from "react" // we need to import Component
import MuteButton from "./components/MuteButton.js"

class App extends Component {
  // states
  state = { // "state" is a reserved variable name for React components
    isMuted: false 
  }
  
  // event handlers
  toggleMute = () => {
    let newMuteState = !this.state.isMuted // notice how we use 'this'
    console.log("setting new mute state value:", newMuteState)
    this.setState({ isMuted: newMuteState }) // call setState with a object of key-values to update
  }

  // render
  render() {
    return (
      <div className="App">
        <h2>Mute Button App</h2>
        <hr />
        <MuteButton isMuted={ this.state.isMuted } toggleMute={ this.toggleMute } />
      </div>
    )
  }
}

export default App
```

- **class-based component version of `components/MuteButton.js`**
```javascript
// MuteButton.js

import { Component } from "react" // we need to import Component
import OnIcon from "../../icons/on.svg"
import OffIcon from "../../icons/off.svg"

class MuteButton extends Component {
  // props
  const { isMuted, toggleMute } = this.props; // notice how we use 'this'
  
  // render
  render() {
    return (
      <button onClick={ toggleMute }> {/* or could be this.props.toggleMute */}
      { 
        isMuted 
          ? <img src={OffIcon} alt="AudioOff" />
          : <img src={OnIcon} alt="AudioOn" /> 
      }
      </button>
    )
  }
}

export default MuteButton;
```

We introduced some new syntax above, so let's make sure we understand what we had to change from functional components to class components

- `function App { ... }` became `class App extends Component { ... }` 
  - We are inheriting from the base Component class from React. All React components must inherit from Component. 
- `const [isMuted, setIsMuted] = useState(false)` became `state = { isMuted: false }`
  - This is how we initialize state values in a class component. We don't need to use ```useState()``` like we had to in functional components. We MUST name the variable name of `state` and pass it an object of values!
- `setIsMuted(newMuteState)` became `this.setState({ isMuted: newMuteState })`
  - Again, we're no longer using the `useState()` hook. Instead, we call the predefined setState method and pass it whatever state value(s) we want updated
- `<MuteButton isMuted={ isMuted } toggleMute={ toggleMute } />` became `<MuteButton isMuted={ this.state.isMuted } toggleMute={ this.state.toggleMute } />`
  - We access state values by looking in the `this.state` object instance attribute. The `this` keyword refers to the instance if this component class.
- `return (...)` became a function `render() { return (...) }` 
  - Every class-based component must have a `render()` method, returning JSX. This is how the component will get rendered by React. 
- One final note: Instance methods DO NOT use a ```function``` keyword

## External Resources
- [Functional Components -vs- Class Components](https://www.geeksforgeeks.org/differences-between-functional-components-and-class-components-in-react/)

## Assignments
- [Mute Button](https://github.com/romeoplatoon/react-mute-button) ...from lecture
- [Whack-A-Mole](https://github.com/romeoplatoon/react-whack-a-mole)
- [Palindrome](https://github.com/romeoplatoon/react-palindrome)
- [Doors Game](https://github.com/romeoplatoon/react-doors-game)

