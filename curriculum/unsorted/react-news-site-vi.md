# News Site VI

## Topics Covered / Goals
- Full stack react/django application setup 
  - managing static files with CRA + django
  - running `npm run build` automatically with `npm watch`

## Lesson
> Today, we're going to build a full stack news site. First,  we're going to make a few small changes to our back end so it can serve as an API to our front end. Next, we'll combine our frontend and our backend into a single, full-stack project. After we learn a reliable development workflow for our full stack application, we'll add a feature that lets a user set a preference in the database which affects how the frontend displays for them.


### Backend Setup
> There are a couple of things I'll change in my django app that may be a bit different from how we've done things before. 
- Install django-rest framework: `pip install djangorestframework`
- Add DRF to your Apps in `settings.py`: 
  ```python
  INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'news_app',
    'rest_framework',
]
  ```
- Use DRF's `@api_view([])` decorator for our API routes. This indicates that a route will be used for AJAX requests, and should return JSON. 
- Update our root route `/` so that it manually sends an HTML file from our static folder, instead of rendering a template. We're not going to use Django templates anymore. 

### Serving the Frontend from the backend
> In a full stack application, your frontend is not a separate application from your backend. It's all one full stack single-page application, but your backend must send the frontend to the client when the homepage is requested. First, let's copy our front end into our django project folder, we have all of our files together.
```bash
# news-site=react app, news_app=django project
mv news-site/* news_app
```

> Next, to get django to serve our front end, we can't use the react's dev-server anymore. It can be useful when we're learning react, or for pure front-end developers, but as full-stack developers, we'll need django to serve our front-end. This way, our local development environment more closely resembles the production environment, and we're less likely to get surprise bugs when we deploy. 

> We have to build the react application, by typing `npm run build`. After we build the app, notice that a new `build` folder was created. Inside of this folder, you'll find the contents of the react `public` folder were copied over, and there's also a folder called `static` which contains your compiled/minified JS and CSS.

> If you look at the generated `index.html` file, you might notice that react has assumptions about how our server serves static files. It has scripts and links that fetch static files from `/static`. As long as we're using create-react-app, react is not very flexible about these assumptions, so we'll have to change our django settings to match react's folder structure. 

```python
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, "build/static"), # your static/ files folder
]
```

> Now we can see that our styles and scripts are loading. However, if we try to make any changes to the front-end, they won't take effect immediately. We have to rebuild the app, but that's not very convenient. We need to set up our react app to automatically rebuild, whenever we change our code. Fortunately, there's an npm package that will help us do this.

```bash
npm install watch
```
> Next, let's add a script to our `package.json` that invokes watch. 

```json
  "watch": "watch 'npm run build; touch manage.py' ./src"
```

> We can start the watcher with `npm run watch`. While it's running, any changes to anything in the `src` folder will cause the front-end to rebuild, and the back-end to restart.

### Authentication
> Now that we can continue developing our front-end, let's add authentication in. In our react router, we need to add pages for login and signup, then we'll build those pages with login/signup forms. On the server side, we'll need to handle these requests in our views. We'll also need a `whoAmI` route, so that we can identify the user after they've logged in. We'll use DRF to indicate that these are API views, which changes some default settings that makes this easier to integrate with axios. 

```python
@api_view(['POST'])
```

> Now we can sign up and log in, but it's not obvious that we're logged in, or how to log out. Let's conditionally render a logout button in our `AppNav`, only when the user is logged in. 
```javascript
<Nav.Link  href="#/signup">Sign Up</Nav.Link>
<Nav.Link  href="#/login">Log in</Nav.Link>
{ props.appState.user && <button onClick={()=>{logOut(props.setAppState)}}>Log Out</button> }
```

> Now that the user is logged in, django will start enforcing CSRF protections. We'll have to manually grab the CSRF token from a cookie that django sets, and then include it in a header for every request we send to our server. Fortunately, the django docs include a function for parsing cookies, and axios can be configured to include default headers on all requests. Put the following code in `utils.js` (or really anywhere) to automatically satisfy django's CSRF requirements. 

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

> If we log in and then refresh the page, the logout button disappears! We'll need to use `useEffect` to run `whoAmI` when the page loads initially. 
```javascript
useEffect(()=>{
  whoAmI(setAppState)
}, [])
```

### Adding preferences
> Now that we've got our basic full stack app set up, let's add a new feature for saving preferences. First, we'll create the front-end UI for filling a form and sending the request to the backend. Then we'll handle creating the preference on the backend, and lastly we'll update our `whoAmI` route to handle sending the preference to the front-end, and apply it to the UI. 


