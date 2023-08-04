# News Site II

## Topics Covered / Goals
- Use client side routing / React router
- Use Bootsrap components


## Lesson
- News Site II (see assignment list)

### Article list component
> Let's create a component for the home page that will list our articles

```javascript
import "./articleList.css"
import ArticleTeaser from '../ArticleTeaser/ArticleTeaser.js';

function ArticleList(props) {
  return (
    <ul id="articles">
      { 
        props.articles.map((article, index) => (
          <li key={index} className={index % 2 ? "odd-item" : "even-item"}>
            <ArticleTeaser { ...article } id={ index + 1 } />
          </li>
        ))
      }
    </ul>
  )
}

export default ArticleList;
```

### React-router
> When we click a link in our list, we want the page to refresh and show the article. We need to use react-router so that we can 'change the page' without losing application state. From the server's perspective, we're still on the same page, but to the user, they are moving to different pages on your site. 

> For now, we'll need at least two pages. The home page will have a list of articles, and then we'll have another page for viewing an individual article.

[React Router](https://reactrouter.com/en/main) is a popular open source library that's used to control routing in a single page app. Using this library, you can load `component`s based on URL paths. For example, you can configure React Router to load ComponentX when the URL `http://localhost:3000/#/componentx` is requested.

To utilize React Router, let's install:
```sh
$ npm install react-router-dom --save
```

### hash-router vs browser-router
> Normally, whenever the URL changes, your browser sends a GET request to that new URL. However, by default, if the URL contains a `#`, then anything that changes after the `#` is not considered a new server-side route, and so no data is sent to the server. This is used for client-side routing to elements with a specific id. You can access this information from javascript with `window.location.hash`. hash-router extends this concept, and uses the value of `window.location.hash` to load different components, which will serve as the pages of our website.

> Another option for client-side routing is the browser-router. This approach doesn't leave a `#` in the URL, and instead uses extra javascript to avoid making requests to the server when the URL changes. Using the browser router can cause a variety of bugs to occur in your application, because there will be some situations where client-side URLs will accidentally get sent to the server. When a user refreshes the page or just types a URL directly into the URL bar, that request will go to the server, and your server needs to be prepared to handle that request gracefully. Also, many modern browsers don't even show the full URL by default, so the sole benefit of the browser router is often irrelevant. 


In `App.js`, we need to bring in the necessary libraries from the package we just installed:

```javascript
import { HashRouter as Router, Routes, Route } from 'react-router-dom';
```

Let's rewrite our `render`:
```javascript
return (
  <div>
    <h1>AppNav Component</h1>
    <hr />
    <AppNav navItems={navItems} handleNavClick={(clickedItem) => console.log(clickedItem)} />
    <Router>
      <div>
        <Routes>
            <Route path="/" element={<HomePage />} />
            <Route path="/articles/:articleID" element={<ArticlePage />} />
        </Routes>      
      </div>
    </Router>
  </div>
);
```


## ArticlePage Component
The `ArticlePage` component should render the `Article` component, and provide the necessary props to the child component. If you remember, `Article` accepts a variety of props from a single article object in `src/data/news.json` array. In order to determine the array object to use, we need to obtain the params from the router logic. To do this, we can employ the `useParams()` hook. The index you'll want to target within the articles array will be contained within `params.articleID`, which corresponds to `[articleID]` portion in this URL: `http://localhost:3000/#/article/[articleID]`

__useParams()__

You can use the `useParams` hook to retrieve dynamic params from the current URL (ex: `articleID`) that were matched by the Route Path.

Example for url: http://localhost:3000/#/articles/:articleID
```js
import { useParams } from 'react-router-dom';

const ArticlePage = () => {
  let { articleID } = useParams();

  return (
    <div>
      <div>Article Page</div>
      <div>Article ID: { articleID }</div>
    </div>
  )
}
```

## HomePage Component

The one piece of functionality left to complete is the `handleTitleClick` function being passed into the `ArticleList` `component`. Ultimately, this function should trigger a page change. React Router automatically passes a series of routing-related props to the `HomePage` `component`. 
We can use the `Link` component from `react-router-dom` to manually navigate to a route path. Ultimately, this should look something like 

```js
import {Link} from "react-router-dom";

  <Link to=`/articles/${articleID}` > title </Link>

```

`articleID` corresponds to the index of an item in the articles array, and is a parameter already being passed into this function. You should be able to click links in your homepage and be able to hit different urls that correspond with the article that you clicked.



## Assignments
- [News Site II](https://github.com/sierraplatoon/react-news-site-ii)
- [Hangman](https://github.com/sierraplatoon/react-hangman)


