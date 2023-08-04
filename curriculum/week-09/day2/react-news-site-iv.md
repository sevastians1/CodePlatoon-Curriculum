# News Site IV

## Topics Covered / Goals
- Asynchronous JavaScript review
- Working with Promises review
- Using axios

## Lesson
Today, we are going to add an API integration to our news website. 

## Getting Data from an API
Up to this point, we've been building our website using static data that exists within our app. Let's first grab our completed `news-site-iii` and keep it nearby. We'll need it soon. We have been importing our News from `/data/news.json` and passing it into our ArticleList component. That's okay for right now, but we want to have dynamic data being fetched from an API. 

Today's challenge isn't going to be a ton of React. Instead it's mostly using JavaScript to connect with an API and use the returned data in our app, presenting in a React Component. Once we get our news data from the external data source (our API), we will put it in our HomePage and ArticleList components.

## Asynchronous Code

Let's take a step back and first talk about what asynchronous code is, why we use it, and how it operates in JavaScript. Let's take a look a the code below:
```javascript
function wait(time_in_ms) {
    let startTime = new Date().getTime();
    while (new Date().getTime() < startTime + time_in_ms);
}

console.log("sierra");

wait(5000); // wait 5 seconds ("doing some work")
console.log("hello");

wait(6000); // wait 6 seconds ("doing some work")
console.log("world");
```
How long will this code take to execute? Answer: 11 seconds.

But what if we could do some work in parallel (i.e. asynchronously)? Let's take a look at our updated asynchronous code below:

```javascript
function wait(time_in_ms) {
    let startTime = new Date().getTime();
    while (new Date().getTime() < startTime + time_in_ms);
}

console.log("sierra");

let myCallback = () => { console.log("world"); }
setTimeout(myCallback, 6000); // wait 6 seconds ("doing some work"), asynchronously

wait(5000); // wait 5 seconds ("doing some work")
console.log("hello");
```

How long will this code take to execute? Answer: 6 seconds.

This is because while we're doing some work on the side for 6 seconds, we are able to do some other work in our main execution thread for 5 seconds.

Some examples of asynchronous functions:
- setTimeout(()=>{}, 10000)  <=== call function after 10 seconds have passed
- setInterval(()=>{}, 10000) <=== call function every 10 seconds

## Promises
Now, let's talk about Promises. A [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) in JavaScript is a construct that specifies a future returned value. 

Let's take a look at the basic structure of **creating a Promise**:

```javascript
function doSomething() {
    return Math.random() > 0.25; // success rate: 75% 
}
// an example of a Promise that resolves to a string (NOTE: we can return any data type as needed)
let myPromise = new Promise((onSuccess, onFailure) =>{ 
        let success = doSomething();
        if (success)
            onSuccess("We did our thing!");
        else
            onFailure("There was an error!!!");
    }
)
```

And next, let's take a look at the basic structure of **consuming a Promise**:

```javascript
function doSomething() {
    return Math.random() > 0.25; // success rate: 75% 
}
// creating a Promise
let myPromise = new Promise((onSuccess, onFailure) => { 
        let success = doSomething();
        if (success)
            onSuccess("We did our thing!");
        else
            onFailure("There was an error!!!");
    }
)
// consuming a Promise example 1
myPromise.then(data => {
    console.log("SUCCESS:", data);
}).catch(error => {
    console.log("FAILURE:", error);
})

// consuming a Promise example 2
// let handleSuccess = (msg) => { console.log("SUCCESS:", msg); };
// let handleFailure = (msg) => { console.log("FAILURE:", msg); };
// myPromise.then(handleSuccess, handleFailure);
```

Asynchronous code can make your code much faster, when you have tasks that are independent of one another and can run in parallel. However, sometimes, you need to complete some tasks in order.

Let's consider a program that simulates some real world action. I think it's a great time to grab some cereal, so let's do it:

```javascript
let washDishes = () => { 
    return new Promise((onSuccess, onFailure) => {
        setTimeout(() => onSuccess("We washed a bowl and spoon!"),15000)
    }); 
}

let pourCereal = (result) => { 
    console.log(result);
    return new Promise((onSuccess, onFailure) => {
        setTimeout(() => onSuccess("We poured some cereal!"), 3000)
    }); 
}

let pourMilk = (result) => {
    console.log(result);
    return new Promise((onSuccess, onFailure) => {
        setTimeout(() => onSuccess("We poured some milk!"), 3000)
    });  
}

let eatBreakfast = (result) => { 
    console.log(result);
    console.log("Now we can eat!");
}

let goHungry = (error) => {
    console.log(error)
    console.log("I’m still hungry!!!");
}

washDishes()
    .then(pourCereal, goHungry) 
    .then(pourMilk, goHungry)
    .then(eatBreakfast, goHungry);
```

Notice how we are able to chain Promises together, via the then() method. The then() method gets called once a Promise is resolved (either fulfilled or rejected). The then() method can create another Promise and continue the chain.

You can also write the chain to handle any error with the same handler function, via a catch():

```javascript
// ...from above

washDishes()
    .then(pourCereal) 
    .then(pourMilk)
    .then(eatBreakfast)
    .catch(goHungry); // this catch will handle any onFailure's
```

### Using async-await instead of .then() construct

Instead of using chained then()s, there's an alternative design that you can employ, using the `async` and `await` keywords in JavaScript. The one caveat is that you need to create a parent async function. 
The keyword async before a function makes the function return a promise
Let's take a look:

```javascript

let washDishes = () => { 
    return new Promise((onSuccess, onFailure) => {
        setTimeout(() => onSuccess("We washed a bowl and spoon!"), 15000)
    }); 
}

let pourCereal = (result) => { 
    console.log(result);
    return new Promise((onSuccess, onFailure) => {
        setTimeout(() => onSuccess("We poured some cereal!"), 3000)
    }); 
}

let pourMilk = (result) => {
    console.log(result);
    return new Promise((onSuccess, onFailure) => {
        setTimeout(() => onSuccess("We poured some milk!"), 3000)
    });  
}

let eatBreakfast = (result) => { 
    console.log(result);
    console.log("Now we can eat!");
}

let goHungry = (error) => {
    console.log(error)
    console.log("I’m still hungry!!!");
}
async function prepBreakfast() {
    try { 
        let result = await washDishes();
        result = await pourCereal(result);
        result = await pourMilk(result);
        return eatBreakfast(result);
    }
    catch(error) {
        return goHungry(error);
    }
}

prepBreakfast();
```

The `await` keyword will make the excution wait for completion before proceeding to the next line. In the case of Promises, `await` will wait until the Promise is resolved and then will proceed. <ins>**Remember: You can only use the `await` keyword in functions that are marked with `async`**</ins> (otherwise you'll get an error). As we've discussed before, JavaScript is a single-threaded programming language so `await` doesn't actually halt (or "block") your entire application's execution. `async` functions behave differently than normal functions, because we are simulating asynchronous execution via `await`. Also, one key thing to remember is that `async` functions actually always return Promises (return values will automatically be converted to Promises!). And to be safe, we should always wrap our awaits in a try-catch in case something goes wrong.

## Axios
Now let's talk about axios, which can be used to retrieve things from web resources. A call to `axios.get` returns a Promise that will resolve to a Response object. We process this [Response](https://developer.mozilla.org/en-US/docs/Web/API/Response) object to get the data that we expect back from our fetch request.  


```
npm install axios
```

Next, let's create 3 functions:
- `fetchArticleByID(id)` - given an article ID, returns an Article object with the given ID.
- `fetchArticlesBySection(section)` - returns a list of articles whose `section` attribute matches the section argument.
- `fetchArticles(filters)` - returns a list of articles. The filters argument is optional - if no filters are provided, an array of all the articles are returned. If filters are provided, an array of Articles that meet the criteria are returned.



> create `src/api/ArticlesAPI.js`

`ArticlesAPI.js`
```js
const fetchArticleByID = (articleID) => {
};

const fetchArticlesBySection = (section) => {
};

const fetchArticles = (filters = null) => {
};

export default {
  fetchArticleByID,
  fetchArticles,
  fetchArticlesBySection
};
```

## Integrating ArticlesAPI.js into your App
At the moment, there is one component that is importing the json data and that is App.jsx
We might want to have each page consume its own data instead of keeping it in the `App.jsx` component but we can do that later. We can start in our main app file first.

For this, we want you to use async/await.
- To make API calls to outside resources within your React app, you have to use axios to make `get` requests
- axios is asynchronous (i.e., not synchronous / happening out of order)
- `axios.get` returns a Javascript `Promise` object. These `Promise` objects are basically Javascript's immediate response to you, saying "Hey I have received your request. I `Promise` to respond when I can."
- `Promise` objects must be resolved in order to get to the data using the`async/await` functionality built into Javascript
- Error-handling: whenever calling out to an API, there is always a possibility of an error occuring. To handle this error on the client-side and give our user proper feedback, we'll tack on a [.catch()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) at the end of our promise chain or you can put the api call in a `try{}` and catch errors with `catch(error){}`

[HackerNews API docs](https://hn.algolia.com/api)

> Example of calling the api

```js
const [someDataFromAnAPI, setSomeDataFromAnAPI] = useState(null)

  const CallAPI = () => {
    return axios.get('http://hn.algolia.com/api/v1/search_by_date?tags=story') //gets latest stories
  }
  async function getData() {
      try {
        const jsonResponse = await CallAPI()
        setSomeDataFromAnAPI(jsonResponse)
        console.log(jsonResponse)
      } catch (error) {
        console.error('Error occurred fetching data: ', error);
      }
    }
    
  useEffect(() => {
    getData()
  }, [])
  ```

  > The call could also be written like 
  ```js
  const CallAPI = () => {
    return axios.get('http://hn.algolia.com/api/v1/search_by_date', {
          params:{
            tags: ('story'),
            hitsPerPage:50
          }
    } )
  }
  
```
> get stories before a certain date
```js

let date = Math.floor(Date.now() / 1000) - 86400 //24hrs ago
axios.get('http://hn.algolia.com/api/v1/search_by_date?', {
          params:{
            tags: ('story'),
            hitsPerPage:50,
            numericFilters: `created_at_i<${date}` //news before 24hs ago
          }
    } )
```
#### API Calls

- fetchArticleByID: 
```js 
axios.get('http://hn.algolia.com/api/v1/search_by_date?', {
          params:{
            tags: (`story_$storyID`),
            hitsPerPage:50,
          }
    } )
```
    
- fetchArticles(filters):
```js
axios.get('http://hn.algolia.com/api/v1/search_by_date?', {
          params:{
            tags: ('story'),
            hitsPerPage:50,
            query: ('power', 'phone')
          }
    } )
```

- fetchArticlesBySection: 
```js
axios.get('http://hn.algolia.com/api/v1/search_by_date?', {
          params:{
            tags: (`${section}`),
            hitsPerPage:50,
            query: ('power', 'phone')
          }
    } )
```

The final goal is to recreate the [hackernews website](https://news.ycombinator.com/)

- Complete the three api functions: `fetchArticleByID`,`fetchArticles`, and `fetchArticlesBySection`
- Update the `data/sections.json` file to have sections that match the HN site
- Integrate the above api consumption pattern fully into your page files `src/pages/HomePage.js` , `src/pages/ArticlePage.js` and `src/pages/SectionPage.js` so that they are functional and each fetch the data related to them
   - this will probably mean refactoring the child components as well to work with our new pattern

- Styling


## Assignments
- [News Site IV](https://github.com/sierraplatoon/react-news-site-iv)
