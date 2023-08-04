# Full-stack App

## Lesson
> Today, we'll be building a full-stack application, using React and Django. This is mostly the same as using React or Django separately, but there are a few main things we'll need to do differently.

> First, we'll need to `build` our react application, so that our django application can serve it from the static folder. We'll learn how to watch our jsx files for changes, so that they rebuild automatically when you make changes. We're not going to use the dev server any more!

> Next, we'll add Django REST Framework to our project to use the `@api_view` decorator. DRF has many features, and today, we'll only be using one of them. This decorator changes some default settings for CSRF protection, and replaces the `request` object with one that is more useful. 

> Lastly, we'll learn how to include CSRF tokens in AJAX requests, so that our application can be protected against CSRF vulnerabilities. 


### create django project

> Here's a rough outline of what we'll need to do today, to build a minimal full-stack application. 

- create a django project as usual:
    - `python -m django startproject <project_name>`
    - set up custom user model
    - set `AUTH_USER_MODEL` in `settings.py`
    - create/run migrations
    - declare `STATICFILES_DIRS` in `settings.py`
- create a react project: `npm create vite`
- edit `config.json` and build the react project
    - vite needs to place static assets in a folder that django will serve them from, and vite needs to write URLs in our `index.html` that our django server will recognize. 
    - your index.html will be copied from `./index.html` to `<outDir>/index.html`
    - your compiled static assets (js, css) will minified, concatenated, and copied from `./src/*` to `<outDir>/assets/*`
    - your uncompiled static assets (images, sounds, videos) will be copied, unmodified, from  `./public/*` to  `./dist/*`
- rebuild the project
	- we're no longer using the vite dev-server, we must rebuild the project to see changes to js files
	- watch the project for changes using a watch script in package.json: `"watch": "vite build --watch",`
- add Django Rest Framework and use `@api_view()` decorators
	- changes default CSRF protections to be more reasonable. Only logged-in users should need tokens
	- lets us declare which request methods a given URL can respond to
	- automatically parses the body as JSON
- store the authenticated user in react state
	- first, we'll need the usual signup, login, logout, whoami routes. 
	- request the logged in user from `/whoami` in a `useEffect` callback.
- use the CSRF token to make requests to the backend.
	- When using DRF, authenticated users must include a CSRF token to make requests other than GET requests
	- axios can be configured to automatically include this token in all requests


> Your `config.json` for vite might look a little like this:

```javascript
export default defineConfig({

  // vite uses this as a prefix for href and src URLs
  base: '/static/',
  build: {
    // this is the folder where vite will generate its output. Make sure django can serve files from here!
    outDir: '../shoe_proj/static',

    // delete the old build when creating the new build. 
    // this is the default behavior, unless outDir is outside of the current directory
    emptyOutDir: true,
	
	// vite will generate sourcemaps, which let you see logs and error messages with line numbers from our jsx files, not from the minified js
	sourcemap: true,

  },
  plugins: [react()]
})
```


> You can configure Axios to automatically send CSRF tokens by setting default headers:

```javascript
const getCSRFToken = ()=>{
    let csrfToken

    // the browser's cookies for this page are all in one string, separated by semi-colons
    const cookies = document.cookie.split(';')
    for ( let cookie of cookies ) {
        // individual cookies have their key and value separated by an equal sign
        const crumbs = cookie.split('=')
        if ( crumbs[0].trim() === 'csrftoken') {
            csrfToken = crumbs[1]
        }
    }
    return csrfToken
}
axios.defaults.headers.common['X-CSRFToken'] = getCSRFToken()

```


> We can make our front end aware of the logged in user by calling `whoAmI()` in a `useEffect`, and on `logOut`.

```javascript
  const logOut = function(event){
    event.preventDefault()
    axios.post('/logout').then((response)=>{
        console.log('response from server: ', response)
        whoAmI()
    })
    
  }
  
  const whoAmI = async ()=>{
    const response = await axios.get('/whoami')
    const user = response.data && response.data[0] && response.data[0].fields
    console.log('user? ', user)
    setUser(user)
  }

  useEffect(()=>{
		whoAmI()
  }, [])
  ```

# Assignment 

### Setup
- Create a full stack application. 
 	- Connect a react project and a django project make sure you are able to see your `App.jsx`code in the browser at `localhost:8000`

### React Frontend
***\*Utilize state, and effects**
- Create a `login page`, a `signup page`, and a `home page` inside your `react router`(you may choose between `HashRouter` and `BrowserRouter`).
	- `signup`: 
		- form that takes in firstname, lastname, username(can be email), and password
		- a submit button that takes the data from the form and saves it
	- `login`: 
		- form that takes in username and password
		- a submit button 
		 	- takes the data from the form and keeps track of the logged in user `useState()?`
		 	- reroutes to the `homePage`
	- `homePage`: 
		- display username of person logged in  
		- button to log out


### STRETCH: connecting to Django API Server 
*\* walkthrough [demo](https://www.youtube.com/watch?v=6vBGHBmXKAw&list=PLu0CiQ7bzwESxBdsmsbfRk8Nm4tv5lVgq&index=5) from a previous cohort (includes the project set up as a whole)

- create api routes for `login`, `signup`, and `logout` that work with the django user auth model
- make the three buttons in our react pages from above call the apis that you wrote in your django application 
  

***\*If you are using `browser router` be careful of what you name your react client side routes vs your backend server side API routes**

***\*the demo should help with any `csrf` issues you may run into**