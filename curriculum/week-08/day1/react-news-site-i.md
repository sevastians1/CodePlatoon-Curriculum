# News Site I

## Topics Covered / Goals
- Planning and creating a React application
- Passing down data and functions as props
- Using state properly
- React Bootstrap


## Lesson
Today begins a string of lessons involing the 'News Site' project. This is something that we'll be going over together during the lectures for the next few days, as well leave it for you to complete on your own as your daily assignment. The goal of this project is just to understand and get comfortable with using React to develop the front end of a website. 


### Project Setup

- start a basic react project with `npx create vite`
    -[npm vs npx](https://sentry.io/answers/difference-between-npm-and-npx-in-javascript/#:~:text=The%20command%20npm%20is%20used,JavaScript%20packages%20downloaded%20this%20way.&text=To%20understand%20why%20both%20of,s%20approach%20to%20dependency%20management.)
- delete unnecessary boilerplate
- copy data over
    - eventually, we'll use the hackernews API to provide news articles for our app. For now, we'll use some hard-coded sample data in `news.json`. 
- create folder/files for components

### Components
- App: top level component that contains all other components
    - The App tracks all the state for the other components (in this demo).
- ArticleTeaser: a preview of an article, with a headline and a date
    - accepts `objectID`, `title`, `created_at`, and `handleTitleClick`
    - accepts a function as a prop. remember the difference between a function call and function reference
- Article: the actual article, with content 
    - has many props. use spread syntax.
    - conditional rendering. There are a few different ways to do this in react
- AppNav: the navigation bar with links
    - list rendering. create a nav link for each item in the navItems array. 


## Component I: ArticleTeaser
The `ArticleTeaser` component should accept the following `props` from `App.jsx`:
1. `objectID` - a number
2. `title` - a string
3. `created_at` - a string
4. `handleTitleClick` - a event handling function

All of these `props` will always be passed in.

The `ArticleTeaser` component should:
1. Display the `title` inside of an `<h2>` tag.
2. When the `title` `<h2>` tag is clicked, it should call `props.handleTitleClick(props.id);`. 
3. Display the `created_at` in a `<p>` tag.


## Component II: Article
In `App.jsx`, you'll notice that when the `Article` component is rendered, we pass `{...article}` to the component. This is known as the spread syntax. You can read more about it [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax). Rather than passing the entire `article` object, we are spreading its properties to be passed down via `props`.
Therefore, the `Article` component should accept the following `props`:
1. `title` - a string
2. `created_at` - a string
3. `url` - a string
4. `author` - a string (optional)

The `title`, `abstract`, and `created_date` `props` will always contain values. `image` and `byline` may be set, but they may also be null. Be sure to account for this.

The `Article` component should:
1. Display the `title` inside of an `<h1>` tag.
2. Display the `created_at` in a `<p>` tag.
3. Display the `url` in an `<a>` tag.
4. Display the `author` inside of a `<p>` tag.

#### Sidenote: Conditional rendering in React
How do I only render something if the data exists? There are several ways we can handle this in React. Here we will explore three common options:

**Option 1: `&&`**

```javascript
// in the render
<ParentComponent>
  {dataExists && <ChildComponent>}
</ParentComponent>
```
Explanation: If there is just one piece of data we're checking for, we can do a quick existence check which will coerce the data to a boolean value `true` if the data does exist and then render the component/element that follows `&&`. If the data doesn't exist, it will be coerced to a boolean value `false` and will not render the child component/element.

**Option 2: Create a helper render function**

```javascript
const renderIfDataExists = () => {
  if (dataExists) {
    return <ChildComponent />
  }
};

// in the render
<ParentComponent>
  { renderIfDataExists() }
</ParentComponent>
```
Explanation: This is a common pattern for rendering that involves more complex logic. For example, if our `dataExists` check was looking for multiple pieces of data, we might want to extract it out into this helper function.

**Option 3: Use a ternary operator**
```javascript
<ParentComponent>
  { dataExists ? <ChildComponent /> : '' }
</ParentComponent>
```
Explanation: This is a good option to use if you are choosing between two different components/elements to render, but you can technically just render an empty string or `null` as seen above.

## Component III: AppNav
The `AppNav` component should accept the following `props`:
1. `sections` - an array of navItem objects.
2. `handleNavClick` - a function.

The `AppNav` component should return a `<nav>` component that contains `<a>`'s as children - one for every item in the `props.sections` array.

The AppNav component should:
1) Map through `props.sections` to create an array of `<a>` elements. The objects within `props.sections` look something like this:
```
{
  label: "stories"
}
```
When transforming/mapping the `nav` item objects in `props.sections` into an array of `<a>` tags, you'll want to use the `label` property (displayed in the example above) as the text that appears on screen. At the same time, you will want to attach an event handler to each `<a>`'s `onClick` event. `onClick` should call `props.handleNavClick`, and pass the `label` property from the `nav` item object.

**You are done when all of your data is displayed and your `onClick` events are firing for your AppNav links and your ArticleTeaser links (i.e. you should see the `console.log`s)**

### React-bootstrap
> It's easy to use bootstrap in our react app the same way we have been using it already. We could load bootstrap's CSS and JS in our index.html, and define a react component that uses those bootstrap classes. 

```javascript
function MyButton(props) {
  return (
    <button type="button" class="btn btn-primary">{props.buttonText}</button>
  );
}
```

> There's nothing wrong with this approach, but it turns out that other people have already done this work for us. We can use react-bootstrap to import bootstrap components as pre-built react components, instead of constructing them with HTML. Let's install react-bootstrap (and regular bootstrap) with `npm install react-bootstrap bootstrap`, then let's [read the docs](https://react-bootstrap.github.io/getting-started/introduction) to figure out how to use it. 

## External Resources
- [React Bootstrap](https://react-bootstrap.github.io/getting-started/introduction)


## Assignments
- [News Site I](https://github.com/sierraplatoon/react-news-site-i)
- [MadLibs](https://github.com/sierraplatoon/react-mad-libs)


