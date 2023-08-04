# Responsive Web Design

## Topics Covered / Goals
- What is Responsive Web Design (RWD)?
    - accomodates different device sizes, input methods, data network limits
    - progressive enhancement vs graceful degradation
- How do we make a responsive website?
    - meta viewport tag
    - responsive units (%, vh/vw/vmin, ems/rems,)
	- flexbox
    - media queries
	- use a CSS framework (e.g. bootstrap)
- Bootstrap basics
	- Grid system
	- Useful components


## Lesson


### What is responsive web design?
> Responsive web design is an approach to building websites in which the website responds to various aspects of the user's device to show them a more appropriate version of the website. Most commonly, a responsive website needs to adapt to the width of the user's device, to show a version of the website that fits in the screen. Aside from screen size, a responsive website should be usable across different devices with different capabilities, such as touchscreens, mouse and keyboard, or even browsers that don't have javascript.

> Most approaches to responsive web design fall into one of two categories, depending on whether you are primarily targeting larger, fully featured devices, or smaller, more limited devices. 
- Progressive Enhancement is a style of rwd in which you design primarily for the smallest, most limited devices (touch screen devices without mouse/keyboard), and then gradually add non-essential features for larger screens. For example, notice what happens on your linkedin profile page as you expand the window from phone-sized to full-size.
- Graceful degradation is a style of rwd in which you design primarily for the largest, most capable devices (laptops with a mouse and keyboard), and then gracefully remove features for devices that can't handle them. Ideally, removed features are replaced with an alternative, or at least the user is informed that their browser does not support the required feature. One simple example of graceful degradation would be the `<noscript>` tag, which only renders on the screen for users that don't run javascript. Ideally, this element should be used to display a plain-text version of your website for users that don't have javascript enabled. More commonly, this tag is just used to tell users that the website won't work without javascript. 

```html
	<noscript>you need javascript for this webpage!</noscript>

```

### How do we create a website that looks good at different screen sizes?
> There are many aspects of responsive web design, but we will be focused mostly on building layouts that adapt to different screen widths. 

#### Responsive Meta Tag
> The first problem you'll need to solve if you want your website to be usable on phones is that all of the content will appear tiny by default, much smaller than you'd expect. Let's build a basic webpage, and then look at it in the device simulator (left of the 'elements' tab in dev tools) to see how much smaller it gets. 

```html
<html>
    <head>
        <!-- <meta name="viewport" content="width=device-width, initial-scale=1"> -->
    </head>
    <body>
        <h1>Look at this header!</h1>
        <p>this is a paragraph</p>
    </body>
</html>
```
> The problem is that since mobile browsers were invented before mobile websites, many mobile browsers will automatically scale down the size of content so that a website that was designed for a large screen will fit. Since we're actually designing our website for a small screen, we need to control this behavior. This is done by adding a responsive meta tag to the head of the html document. There are [several settings](https://css-tricks.com/snippets/html/responsive-meta-tag/) you can set to control the scaling and zoom. Here's a common responsive meta tag that causes content to render at a consistent size across mobile and desktop browsers.

```html
<html>
    <head>
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
        <h1>Look at this header!</h1>
        <p>this is a paragraph</p>
    </body>
</html>
```

#### Responsive Units
##### %
> One way to make a design that works at different screen sizes is to avoid using fixed units like `px`, and instead use responsive units. The most common or basic responsive unit might be `%`, although it's not the simplest one, actually. `1%` in CSS means 1% of the parent element, for most CSS properties, like height and width. This can make % tricky to work with, because restructuring your HTML might resize an element that is sized with %, even if the screen size is the same. For some CSS properties, such as `transform`, % refers to a percentage of the element itself, not its parent. 

##### vh, vw, vmin, vmax
> Some other responsive units that might be easier to work are vh and vw. One vh is always 1% of the height of the browser window, and one vw is always 1% of the width of the window. These can be useful for a paywall or login modal that you want to cover 'most' of the user's screen, regardless of their window's dimensions. 

> Similarly, 1 vmax is equal to 1 vh or 1 vw, whichever is bigger. A vmin is just the opposite. These can be useful for designing a layout that adapts to the device's orientation, either landscape or portrait. For example, you could use vmin to make an element that expands proportionally to the screen size, but always fits entirely in the screen, regardless of orientation. 

```css
.annoying-popup {
    position: fixed;
    width: 80vmin;
    height: 80vmin;
    background-color: purple;
    border-radius: 999px;
}
```

##### ems and rems
> There are two responsive units that are used for sizing fonts in particular: `em`, and `rem`. `em` lets you set a size for an element relative to that element's parent's font-size. This can be tricky to work with, since the size of an element sized in `em` will change if you move it around the page. Also, nested elements sized with `em` will multiply each other. 


> The other option for responsive font sizing is `rem`, which is a root em. A `rem` lets you size elements as a multiple of the font-size of the `html` element. This makes it easy to have user-adjustable font, or have all of your fonts change size with a media query, as we'll learn about later. If all the text on your site is sized with `rem`, then you only need to change the font-size for the `html` element in order to resize all the text on the site. 

```html
<div class="rem-size">
	<p>wow!</p>
	<div class="rem-size">
		<p>wow!</p>
		<div class="rem-size">
			<p>wow!</p>
			<div class="rem-size">
				<p>wow!</p>
			</div>
		</div>
	</div>
</div>
```

#### Flexbox
> Flexbox is useful for solving one-dimensional layout problems, i.e. when you're trying to line items up in a row or column. This is a different use case from CSS grid, which is used for solving two-dimensional layout problems. `flex` is a relatively new display type that changes the document flow in a container, giving you more options for how you want to center or align content. A `flex` container can have other flex properties, such as `justify-content`. Individual items in a flex container can have flex properties set on them, if you want them to flow differently from their siblings. [This article](https://css-tricks.com/snippets/css/a-guide-to-flexbox/) has a lot of good visual examples of different flex properties. 
```css
.container {
  display: flex; /* or inline-flex */
  justify-content: space-evenly; /* spaces the child elements evenly in relation to the parent container
}
```

#### Media Queries
> If all of the above techniques are not sufficient to make your website responsive, then you might need to use media queries, which let you apply completely different CSS based on various device properties, primarily the width. Uncommonly, you might also need to use media queries to add or remove relevant elements when displaying your page for other devices like screen readers or printers. You probably wouldn't need to print the website's navbar, since you can't click on a piece of paper. 

```css
	@media (max-width: 1245px) {
		html {
			font-size: 18px;
		}
	}
	@media (max-width: 845px) {
		html {
			font-size: 14px;
		}
	}
```

### Styling with Bootstrap

#### What is it?
> Bootstrap is a very popular CSS framework, essentially just a bunch of class selectors with pre-defined CSS rules
- Provides Normalization (standardizes the way some elements look by default - Normalize.css)
- Helps you build your layout (Grid system)
- Helps you build a Responsive design (uses media queries and relative units to accomodate devices of different sizes)

#### Grid System
> Bootstrap's grid system uses a 12 column layout. You will be specifying how many columns (how wide) an element takes up
- 12/12 columns => 100% width
- 6/12 columns => 50% width
- Grid system has a specific pattern you must follow
	- You NEED to have these classes in order for the grid to work

		1. .container / .container-fluid
		2. .row
		3. .col-*-* (.col-SCREENSIZE-COLUMNS)

	- Column Classes
		`.col-xs-8` => on `xs` screens and up, 8/12 columns
		Screen sizes : `xs`, `sm`, `md`, `lg`
		columns : 1 - 12

```html
<div class="container">
	<div class="row">
		<div class="col-md-4"></div>
		<div class="col-md-8"></div>
	</div>
</div>
```

> You can use multiple column classes on an element. Also, if there are more than 12 columns in a row, they will wrap around and make another row.
> In the example below, we'll see 3 rows of 4 columns on medium screens, and 4 rows of 3 columns on small screens.

```html
<div class="container">
	<div class="row">
		<div class="col-md-3 col-sm-4"></div>
		<div class="col-md-3 col-sm-4"></div>
		<div class="col-md-3 col-sm-4"></div>
		<div class="col-md-3 col-sm-4"></div>
		<div class="col-md-3 col-sm-4"></div>
		<div class="col-md-3 col-sm-4"></div>
		<div class="col-md-3 col-sm-4"></div>
		<div class="col-md-3 col-sm-4"></div>
		<div class="col-md-3 col-sm-4"></div>
		<div class="col-md-3 col-sm-4"></div>
		<div class="col-md-3 col-sm-4"></div>
		<div class="col-md-3 col-sm-4"></div>
	</div>
</div>
```

> COLLUMNS CAN CONTAIN OTHER ROWS. THE POSSIBILITIES ARE ENDLESS!

```html
<div class="container">
	<div class="row">
		<div class="col-sm-4">
			left column!
		</div>
		<div class="col-sm-4">

			<div class="row">
				<div class="col-sm-5">
					left subcolumn
				</div>
				<div class="col-sm-7">
					right subcolumn
				</div>
			</div>
		</div>
	</div>
</div>
```

#### JS Components
> Bootstrap provides many interactive components, powered by javascript.
- Collapse
- Accordion (many collapses together, used commonly in FAQs)
- Carousel (for showing a series of images or slides)
- Dropdown (for simple menus)
- Modal (for prettier alert and confirm boxes)
- Navbar (responsive navigation with a hamburger menu)

## External Resources
- [Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
- [Learn Flexbox with flexbox froggy](https://flexboxfroggy.com)

	
## Assignments
- [Styling with Bootstrap](https://github.com/sierraplatoon/html-bootstrap)


