# DOM Events

## Topics Covered / Goals
- Understand what the DOM (Document Object Model) is
- Understand how to use JavaScript to manipulate the DOM
- Understand how to handle browser events

## Lesson

### JavaScript
We've been using [node](https://en.wikipedia.org/wiki/Node.js) to run our javascript code in the terminal. This is a relatively new development for Javascript. Originally, it was created to run exclusively in the browser so that developers could add behavior to web pages. Since then, it has become the primary language of the web. Let's look at how Javascript runs in the browser, and how we can use it to make our web pages more dynamic.

### The DOM
Before we can start using Javascript on our front end, we need to understand what the DOM is and how it works. When the browser receives a webpage (HTML and CSS) it breaks it up into a tree like structure called the Document Object Model (DOM).

![The DOM](../page-resources/DOM_example.png)

Each element in our HTML document is represented as a 'node' in the DOM. We can use Javascript to access these nodes and manipulate them. Let's start with a simple HTML file. Create a file called `index.html` and paste the following code:

```HTML
<!DOCTYPE html>
<html>
  <head>
    <title>My HTML Page</title>
    <script>
      const showGreeting = () => {
        let nameInput = document.getElementById("input-name")
        let greetingOutput = document.getElementById("output")
        if (nameInput && greetingOutput) {
          greetingOutput.innerHTML = "Hello " + nameInput.value + "!"
        }
      }
    </script>
  </head>   
  <body>
    <input id="input-name" placeholder="name"/>
    <button onclick="showGreeting();">Submit</button>
    <div>
      <p id="output"></p>
    </div>
  </body>
</html>
```

### External JavaScript

We have included our JavaScript logic internally to our HTML document. This usually isn't the best organization, so let's move our logic to a separate file ("scripts.js") and link it externally...
```HTML
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>My HTML Page</title>
    <script src="scripts.js" defer></script>
  </head>   
  <body>
    <input id="input-name" placeholder="name"/>
    <button onclick="showGreeting();">Submit</button>
    <div>
      <p id="output"></p>
    </div>
  </body>
</html>
```

```javascript
// scripts.js
const showGreeting = () => {
  let nameInput = document.getElementById("input-name")
  let greetingOutput = document.getElementById("output")
  if (nameInput && greetingOutput) {
    greetingOutput.innerHTML = "Hello " + nameInput.value + "!"
  }
}
```

Note that we have a `defer` keyword included in our `script` tag. This tells the browser to only load the javascript logic after the HTML document has fully loaded. This is usually preferred, since we may want to interact with the DOM when the javascript logic loads and executes, so we need to ensure that the DOM has fully been created. 

### Accessing elements from the DOM
We're using the `getElementById()` method on the `document` object in the code above, to retrieve the element model that we're interested in. Common methods for retrieving items from the DOM are:
- `document.getElementById()`
  - retrieves the [first] element object that matches the specified `id` attribute
  - returns a single element (if found)
  - ids on a given html pages should always be unique!
- `document.getElementsByClassName()` 
  - retrieves every element that matches the specified `class` attribute 
  - returns an array element objects
  - can only be used for a single class name lookup
- `document.getElementsByTagName()` 
  - retrieves every element that matches the specified tag name
  - returns an array of element objects
- `document.querySelector()` 
  - very similar to getElementById() 
  - retrieves the [first] element object that matched the css selector
  - returns a single element (if found)
  - generally use with id-selectors
- `document.querySelectorAll()` 
  - similar to getElementsByClassName() and getElementsByTagName()
  - retrieves every element object that matches the css selector 
  - returns an array of element objects
  - can be used with any combination of class and/or tag names! (CSS combinators)

Let's add some more elements to our HTML page:
```html
<!-- index.html -->

    <!-- in body -->
    <hr />
    <button onclick="updateColor('#DD0000')">Red</button>
    <button onclick="updateColor('#0088FF')">Blue</button>
    <hr />

    <div id="parent-div">
      <div id="one" class="alpha apple">
        This is the first div.
      </div>
      <div id="two" class="alpha avacado">
        This is the second div.
      </div>
      <div id="three" class="beta banana">
        This is the third div.
      </div>
    </div>
```

And now let's see different ways we can access elements using the methods mentioned earlier, by modifying our `scripts.js` file:

- document.getElementById()
```javascript
// scripts.js
const updateColor = (color) => {
  let e = document.getElementById("one") // will return the [*first*] element that has an id equal to 'one'
  if (e) {
    e.style.color = color
  }
}
```

- document.getElementsByClassName()
```javascript
// scripts.js
const updateColor = (color) => {
  let elems = document.getElementsByClassName("alpha") // will return *every* element that has 'alpha' as a listed class name
  for (let e of elems) {
    e.style.color = color
  }
}
```

- document.getElementsByTagName()
```javascript
// scripts.js
const updateColor = (color) => {
  let elems = document.getElementsByTagName("div") // will return *every* div element on the page
  for (let e of elems) {
    e.style.color = color
  }
}
```

- document.querySelector()
```javascript
// scripts.js
const updateColor = (color) => {
  let e = document.querySelector("#one") // will return the [*first*] element that has an id equal to 'one', similar to getElementById() 
  if (e) {
    e.style.color = color
  }
}
```

- document.querySelectorAll()
```javascript
// scripts.js
const updateColor = (color) => {
  let elems = document.querySelectorAll(".alpha") // will return *every* element that has 'alpha' as a listed class name, similar to getElementsByClassName() 
  for (let e of elems) {
    e.style.color = color
  }
}
```

### Creating/Inserting elements into the DOM

In addition to manipulating existing nodes from the DOM, we can use JavaScript to create new elements and insert them into the DOM manually. This allows our pages to be more dynamic, rather than always having to create our HTML elements statically. The main document method to create a new element, is aptly named `createElement()`. 

Let's add a new button to our HTML page:
```html
<!-- index.html -->
    <!-- in body -->
    <button onclick="insertNewElement()">Insert New Div</button>
```

And let's also add in our new javascript code:

```javascript
// scripts.js
const insertNewElement = () => {
  let newDiv = document.createElement("div") // this creates a new div element
  newDiv.innerHTML = "This is a newly created div!"
  newDiv.className = "alpha added"

  let parentDiv = document.getElementById("parent-div")
  if (parentDiv) {
    parentDiv.appendChild(newDiv) // this attaches our new div element into the DOM, by nesting it under an existing element
  }
}
```

Notice that we have to create our new element and then also attach it to an element that already exists in the DOM, in order for it to show up. Don't forget this step!

We've only covered a few methods and properties that can be accessed through an element object. You can view a full list of properties and methods for an HTML element object [here](https://www.w3schools.com/jsref/dom_obj_document.asp)


### Events

The browser is always listening, always keeping track of user behavior. It can tell you when a user has clicked on something, when the mouse enters or leaves an element's bounds, the location of the mouse at any given time, and much more. We've already seen the use of the `onclick` event attribute, which specifies how a click event is handled when generated by the user. You can find a full list of event attributes [here](https://www.w3schools.com/tags/ref_eventattributes.asp). 

We can add event attributes to pretty much every element that exists in the DOM. If we're creating elements just in our HTML file, we simply add the event attribute and specify the event listener (i.e. the function that will be called when the event is triggered) value like any other attribute for an element. However, if we need to specify an event listener dynamically in our JavaScript file, we need to use the `addEventListener()` method on an element object. 

Let's continue to add on to our code from above:

```javascript
// scripts.js
const insertNewElement = () => {
  let newDiv = document.createElement("div") // this creates a new div element
  newDiv.innerHTML = "This is a newly created div!"
  newDiv.className = "alpha added"
  
  // add a new event listener, which will trigger when the user moves the mouse cursor across this element
  newDiv.addEventListener("mousemove", (evt) => {
    // change the background color of the element based on the x-pos of the cursor!
    let xRatio = 1.0 - evt.x / document.documentElement.clientWidth
    let nonRedValue = Math.round(256 * xRatio)
    evt.target.style.backgroundColor = `rgb(256, ${nonRedValue}, ${nonRedValue})`
  })

  // add a new event listener, which will trigger when the user moves the mouse cursor off of this element
  newDiv.addEventListener("mouseout", (evt) => {
    evt.target.style.backgroundColor = "initial"
  })

  let parentDiv = document.getElementById("parent-div")
  if (parentDiv) {
    parentDiv.appendChild(newDiv) // this attaches our new div element into the DOM, by nesting it under an existing element
  }
}
```

The `addEventListener()` method takes in two parameters: 1) The event name, and 2) The event listener function. 

Notice that the event listener function takes in one parameter (that we have named `evt`). This is the event object that is *automatically* passed to our event listener from the browser. This event object contains a bunch of information about the event that occured, including the element that triggered the event (`evt.target`). In the example able, we are also choosing to use the x-position of the mouse (`evt.x`), which is another piece of information available to us from the event object. You can read up more about all of the information stored in the event object [here](https://www.w3schools.com/jsref/obj_event.asp)

There are also some methods that can be called on the event object, if needed:

#### event.preventDefault()
Many user events will trigger some default action in the browser (even if we don't specify an event listener ourselves). Some events that have default behavior:
- When you submit a form, your browser sends an HTTP request based on the form's `method` to the url in the form's `action`.
- When you click on a hyperlink, the browser automatically navigates to the location of the link's href attribute.
- When you press spacebar on a page, it scrolls the page down

All of these default functionality for events that are handled by the **browser** can be prevented by calling `event.preventDefault()`. We can choose to do some other action in response to the event, or just ignore it entirely. 

Let's try this example:

First... add a bunch of new div elements using our button, and then try to hit the <spacebar> key. Notice that your page scrolls, because the browser had a default behavior for handling this particular event.
  
Next... let's add in an event listener to intercept this event, and block it's processing by the browser.

``` javascript
scripts.js

window.addEventListener("keydown", (evt) => {
  if (evt.code == "Space") {
    evt.preventDefault()
  }
})
```

Now... add a bunch of new div elements using our button, and then try to hit the <spacebar> key and see if your page still scrolls down!


#### event.stopPropagation()
Sometimes, you might have an element with event listeners attached to it, nested inside of another element with event listeners attached to it. Consider this example:

```html
<!-- index.html -->
  <div id="parent-div" onclick="sayHello(event)">
    <div id="one" class="alpha apple" onclick="sayHi(event)">
      This is the first div.
    </div>
    <div id="two" class="alpha avacado">
      This is the second div.
    </div>
    <div id="three" class="beta banana">
      This is the third div.
    </div>
  </div>
```

An important thing to notice in the code above is that we are passing an `event` object to our event listener. This is a reserved keyword object, that we have access to in our HTML file. We have to manually pass this value in, since we are calling our event listener ourselves in this case (as opposed to before when our event listener was autmatically called, and automatically passed an event object by the browser).
  
```javascript
// scripts.js

const sayHi = (evt) => {
  console.log("hi (inner)")
}

const sayHello = (evt) => {
  console.log("hello (outer)")
}
```

If we click on the first nested div element, notice that both "hi (inner)" and "hello (outer)" are printed out to the console. The click event is being processed by the inner div, and the outer div because they both have onclick event listeners. What if we wanted to prevent the outer div from processing the event? We can simply add `evt.stopPropagation()` to our `sayHi()` function:

```javascript
// scripts.js

const sayHi = (evt) => {
  evt.stopPropagation() // this will prevent this event from continuing up the DOM tree
  
  console.log("hi (inner)")
}

const sayHello = (evt) => {
  console.log("hello (outer)")
}
```

Notice how the behavior changes now when you click on the first nested div element. You should only see "hi (inner)" printed out to the console, when clicking on the first nested div element. Clicking on any of the other divs, however, continues to print out "hello (outer)"

To summarize the difference: `preventDefault()` prevents the browser from doing it's default behavior based on an event, whereas `stopPropagation()` prevents other elements in the DOM from processing an event.
  
  
### Working with HTML Forms
  
Forms are HTML elements used to collect user data and pass it on to somewhere else. To create a form, we use the `<form></form>` tag, and insert form controls inside of the form. Let's change our HTML file to use a form instead of just an input control:
  
```html
<!-- index.html -->
  
    <!-- in body -->
    <form onsubmit="handleFormSubmit(event)">
      <input id="input-name" name="name" placeholder="name"/>
      <br />
      <input id="favorite-color" name="favColor" placeholder="color"/>
      <br />
      <button type=""submit>Submit</button>
    </form>

    <div>
      <p id="output"></p>
    </div>
```
  
```javascript
const handleFormSubmit = (evt) => {
  evt.preventDefault() // this will prevent the default browser behavior (page refresh)

  const formData = new FormData(evt.target);
  const formProps = Object.fromEntries(formData);
  
  // print out all the field names and values
  for (let fieldName in formProps) {
    console.log(fieldName, formData.get(fieldName))
  }

  showGreeting(formData.get("name"))
  
  // // alternate way (without using FormData)
  
  // let nameElem = evt.target.elements[0]
  // console.log(nameElem.name, nameElem.value)
  // let favColorElem = evt.target.elements[1]
  // console.log(favColorElem.name, favColorElem.value)
  
  // showGreeting(nameElem.value)
}
  
  
const showGreeting = (name) => { // update our function to take in a parameter
  let greetingOutput = document.getElementById("output")
  if (greetingOutput) {
    greetingOutput.innerHTML = "Hello " + name + "!"
  }
}
```
  
Some things to notice from the code above... 
  - We are using a `<form>` element, which has an onsubmit event attribute. 
  - We have added `name` attributes for our form fields, so that we can identify the fields within the form (the `id` attribute is not referenced for form data).     - Our button within our form has a `type=submit` attribute, which triggers the submit event for the form. All form should have at least one element with a `submit` type.
  - We are using the aforementioned `preventDefault()` method to avoid the default behavior of a form submit event. Normally, our browser would refresh the page automatically. We don't want this behavior because this will refresh our javascript application.
  
Forms are generally used to send data to another location on the web (usually an external server for processing). For example, when we enter in our username and password information on a site login page, the form data is collected and sent to a server to process when we hit the 'login' button. 
  
## Additional Resources
- [Eloquent Javascript: The DOM](https://eloquentjavascript.net/14_dom.html)
- [Excellent guide to DOM Event Bubbling & Capturing, Good Diagrams](https://javascript.info/bubbling-and-capturing)
- [MDN Event Reference](https://developer.mozilla.org/en-US/docs/Web/Events)

  
## Assignments
- Read the [MDN Introduction to Events](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events) and work through it's [Simple Example DOM Events tutorial](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events#a_simple_example).
- [DOM Manipulation](https://github.com/sierraplatoon/html-dom-manipulation)  
- [Guess A Number](https://github.com/sierraplatoon/html-number-guessing-game)
- Stretch Challenge: [Create a Static Webpage](https://github.com/sierraplatoon/html-static-webpage)


