# Server-Side Rendering

## Topics Covered / Goals
- Understand the pros and cons of server-side rendering vs client-side rendering
- learn the basics of templating
  - conditional rendering
  - variable interpolation
  - rendering in a loop
- learn intermediate templating concepts
  - use `includes` to avoid repeating common markup in a page, like modals or forms
  - use `extends` to avoid repeating common markup around a page, such as headers and footers


## Lesson

### Server-side rendering vs client-side rendering
> Most web applications need to render dynamic content. The web wouldn't be very interesting if every page was the same for every visitor, every time they visited. Generally, when a user (especially a logged in user) sends a request to a web application, the application needs to get necessary data from various sources such as APIs or a database, and then insert that data into a template, which gets rendered into HTML. However, there is an important decision to be made about when and where HTML gets rendered.
- One option, which we'll be exploring today, is server-side rendering.
    - This is the most traditional and simplest way to make a dynamic webpage, since you don't need a front-end framework like React.
    - Also, server-side rendering is easier to think about since your content doesn't get edited in-place; once the rendered HTML is delivered to the client, it's just static HTML. The client would have to send another request and load a new page in order to see new content. 
    - Server-side rendering is generally faster for the initial page load, since the client only has to download the initial HTML document before they can start seeing content. It might still take some time to load scripts and below-the-fold images, but at least the user can read your content as soon as the page loads. First impressions are important. Subsequent page loads may be a little faster, if the user has cached files that are used on all pages, but the page still needs to unload before new content can be rendered.
- Another option, which we'll be exploring later with React, is client-side rendering. 
    - This is a newer style of rendering webpages, made feasible by front-end frameworks like Angular, React, and Vue. 
    - In some apps, the only route that returns HTML is the root route `/`, and then the entire rest of the application is delivered via client-side rendering. Since the actual server route never changes, the client uses javascript to rewrite the URL to indicate which 'page' you're on. This is called a single-page-application, or SPA.  
    - Client-side rendering is a little harder to think about, because the data might be edited in-place based on user interactions. Of course, this is also the greatest advantage of client-side rendering. 
    - Client-side rendering is generally slower for the initial page load, because the client needs to download the entire application and execute javascript before the user sees any content at all. Subsequent page loads will be much faster, since the client needs to only transfer a bit of json and replace part of the page, instead of requesting a whole new page and its associated scripts, stylesheets, and images. 
        - In fact, page transitions in a SPA are sometimes too fast, causing users to not even notice that the page has changed. Some SPAs will add animations with artificial delays to the page transitions, so that it's easier to see when the page changes.
        - Some applications use a technique called 'lazy loading' which causes the client to request templates for new pages only when the client navigates there, instead of downloading everything up front. This makes the initial page load faster, while making subsequent loads a bit slower, mixing the advantages/disadvantages of server-side and client-side rendering.
- You don't have to choose one or the other! An application can use server-side and client-side rendering on different pages. For example, you might choose server-side rendering for parts of your application that don't require a login, like the login/signup pages, home page, about, contact, etc. Then, once a user logs in, all content that requires authentication is delivered in a SPA. 
- Some applications perform both server-side rendering and client-side rendering at the same time. This combines the benefits of a fast initial load with fast page transitions, as well as interactive front-end content. The downside is that this is the most complex way to render content. However, there are frameworks that can make this easier, if this is what you need to do (e.g. Next.js, Nuxt.js)

> Can you think of a type of application where you would prefer to use server-side rendering? How about an application where you would prefer client-side rendering?

### Basic Templating
> Whether you render content on the back-end or the front-end, you'll need to solve some similar problems. The first problem is variable interpolation, which is the simple case where you have a variable that needs to be inserted into the template. We've already seen this a couple times, but lets review. 

> Remember, our template doesn't automatically have access to every variable that is defined in the request function. It only has access to the context that is explicitly passed in. 

> The second problem that we'll need to address is conditional rendering. You may have an element that you only want to display sometimes, depending on some logic.

> The third basic problem that we'll need to address is rendering in a loop. Commonly, we'll have a list of data to present to the user, and it wouldn't make sense to hard-code HTML for each element. We need our templating system to generate similar HTML for each element in a list. 

### Intermediate Templating

#### Includes
> `include` lets us insert one template file into another. The included file would be only a part of an HTML file, called a partial. The file it is included into may or may not be a complete HTML file. This is useful if you have a component that is repeated in many different pages, or in different places in the same page. Including a file can also be useful if the included file is very large or complex, so that you don't have to look at the whole thing when you're reading the template that contains it. 

#### Extends
> Sometimes you'll have elements that are repeated on every page, in the same location. These repeated elements together can be considered your 'layout'. In the layout, we define places where dynamic content will be inserted, called 'blocks'. We can define parts of an HTML page with matching 'blocks' and insert them into the layout using `extends`. This is useful for navbars and footers.



## Resources
- [Django Templates](https://docs.djangoproject.com/en/4.0/topics/templates/)
- [Server-side rendering vs client-side rendering](https://www.freecodecamp.org/news/what-exactly-is-client-side-rendering-and-hows-it-different-from-server-side-rendering-bd5c786b340d/)

## Assignments
- [DRY Startup Website](https://github.com/romeoplatoon/dry-startup-website)
