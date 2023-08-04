# HTML and CSS


## Topics Covered / Goals
- **HTML**
  - What is HTML?
    - HTML = Hyper Text Markup Language
    - Instructs the web browser how to display a page.
    - Separates content from presentation.
    - Elements define content types by containing tags that contain or express content.
  - HTML is not...
    - Programming in its purest sense.
    - Easy, because it requires quite a bit of memorization.
    - Easy, because different browsers display markup in different ways.
- **CSS**
  - What is CSS?
    - CSS stands for Cascading Style Sheets
    - CSS describes how HTML elements are to be displayed on screen, paper, or in other media
    - CSS saves a lot of work. It can control the layout of multiple web pages all at once
  - Why use CSS?
    - CSS is used to define styles for your web pages, including the design, layout and variations in display for different devices and screen sizes.
  - How to debug CSS
    - Use the `elements` tab in devtools
    - Use the `network` tab in devtools


## Lesson
**HTML Tag Architecture**

```html
<!-- index.html  -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Code Platoon!</title>

  <link href="styles.css" rel="stylesheet" /> <!-- A link to our style sheet  -->
  <script src="app.js" defer></script> <!-- A link to our javascript file  that will interact with our webpage -->

</head>
<body>
  <h1 id="title" >My Website</h1>
  <p class='sub-title'>Subtitle for my website</p>
  <!-- The rest of your HTML goes here  -->
</body>
</html>

```

**HTML Elements & Tags**
HTML consists of __elements__ which are the building blocks to HTML pages and which are represented by HTML __tags__.
```html
<!-- index.html  -->

<!-- An HTML element is the start of an HTML tag and the end of an HTML tag. -->
<h1>This is a heading tag</h1>

<!-- This is an HTML element with a button tag -->
<button>PRESS HERE</button>
```

[Here is a list of HTML Tags from HTML](https://www.w3schools.com/tags/ref_byfunc.asp) and [htmlreference.io](https://htmlreference.io/)

**HTML Attributes (__id__ & __class__)**
When styling webpages with colors, fonts, sizes, and photos you will rarely apply CSS properties directly to the __element__ or __tag__.
```html
<!-- index.html -->
<h1>My Website</h1>
<p>Subtitle for my website</p>
```

You will apply an __id__ or __class__ to select the desired HTML element. The difference between an __id__ and a __class__ is that an __id__ can be used to identify only one element, whereas a __class__ can be used to identify more than one.

```html
<!-- index.html -->
<h1 id="title" >My Website</h1>
<p class='sub-title'>Subtitle for my website</p>
```

```css
/* styles.css */
#title {
  font-size: 34px;
  text-align: center;
  color: #66B6FF
}

.sub-title {
  font-size: 18px;
  text-align: center
}
```

**Example**
- Let's `touch` a new html file called `index.html` and put this content inside: `<h1>Hello World!</h1>`
- Break this down:
  - The `<h1>` is called a `tag`, _most_ tags will have openings `<h1>` and closings `</h1>`.
  - The `content` is what is between the tags. Tags can be used to express content in different ways.
    - Let's take a look at what exactly the `<h1>` does to the content that resides within it:
      - Try this: `<h1>content</h1>`
      - Then this: `<em>content</em>`
      - What do you notice that the HTML tags do to the content inside?
  - The combination of everything: `<h1>This is a heading</h1>` is an element. The web is full of elements!
- Sometimes, `elements` have `children`.
- Here is an example of an element with children:
```HTML
<div>
  <h1>Heading</h1>
</div>
```
In this example, the `<div>` has a child, `<h1>`.

**Display types**
It is _crucial_ to know how an element is displayed.

A `block` element, such as `<h1>` takes up the **entire** width of the browser window.

An `inline` element, such as `<a>` only takes up the **space** of its content

**Individual Discovery (Not to be submitted)**
1. [Check out some of the tags and what they do](https://developer.mozilla.org/en-US/docs/Web/HTML/Element)
2. Right-click on some text in your browser and select 'Inspect' to see some elements on your own.
    ![inspect-element](https://cloud.githubusercontent.com/assets/2447940/21957062/4c6b6238-da54-11e6-8219-537af59e20ee.gif)
3. There are some really weird tags like `bdo`, what does it do?
    - Try: `<bdo dir="rtl">uoy htiw eb ecrof eht yam</bdo>`
    - The `rtl` from above is called an `attribute`.

**What is CSS?**
- _Cascading_ Style Sheets
    - The emphasis on cascading means that the last style line of code to run is what is rendered by the browser.
    - A way to think of the relationship between HTML and CSS is building a building.
      - The architecture of a building is the HTML
      - CSS is the color of the paint, the size of the windows, etc.
    - On the web, HTML contains and expresses content, CSS _styles_ the content.

- How to apply CSS (code together)
  - Put it directly `inline`
    - `<p style="color: red;">Paragraph</p>`
  - Create an internal stylesheet
    ```HTML
    <html>
      <head>
        <style>
          p { color: red; }
        </style>
      </head>
      <body>
        <p>Paragraph</p>
      </body>
    </html>
    ```
  - Link an external stylesheet:
    ```HTML
    <html>
      <head>
        <link rel="stylesheet" type="text/css" href="styles.css">
      </head>
      <body>
        <p>Paragraph</p>
      </body>
    </html>
    ```
    `styles.css` file:
    ```CSS
    p { color: red; }
    ```

**The structure of CSS**

`selector { property: value }`

Let's say I wanted to center some text on a line (Let's do this together).

1. `<h5>Text</h5>`
2.  Apply CSS `<h5 style="text-align: center;">Text</h5>`
3.  In this case, the `h5` is the `selector`, `text-align` is the property, and `center` is the value.

**Student Exploration**
1. [Look at all the properties](https://meiert.com/en/indices/css-properties/)
2. Use the browser's element inspector to see what properties have been applied to content across the web.
3. Try altering the CSS through the console!

    ![alter-css](https://cloud.githubusercontent.com/assets/2447940/21957697/17d0c8de-da62-11e6-8e45-00575bbae501.gif)

## CSS Specificity
- What happens if you you have `div`, `class`, or `id` elements with style conflicts? (i.e., a `div` has X style, a `class` has Y style, and an `id` has Z style)? The browser won't know what to do!
- Luckily, there is specificity built into CSS. The higher the weight,
the greater the superiority when styling conflicts occur.

Selector | Weight
--- | ---
`<>` | .001
`.class` | .010
`#id` | .100

Selectors can be combined to result in a greater weight.

`.red-text .capital { background-color: yellow; }`

The above carries a weight of `.020`

**The Box Model**

**~~Everything is a box.~~ Everything on the internet is a box.**

![box model](https://cloud.githubusercontent.com/assets/2447940/21958828/a870607e-da7c-11e6-9447-6e7d8e4d67fe.PNG)

Check it out for yourself

![box-model-gif](https://cloud.githubusercontent.com/assets/2447940/21958874/76a5aff8-da7d-11e6-91d3-e9e4fd97cd2b.gif)

Let's make a box together.

```HTML
<style>
  div {
    border: 6px solid #949599;
    height: 200px;
    margin: 20px;
    padding: 20px;
    width: 200px;
  }
</style>
<div>Hello</div>
```

Hints:

- `border` is shorthand for setting multiple border properties.
- `#949599` is a hexadecimal color code.
- `div` is a tag that has no meaning, except that it is a block element. `span` is the same, except inline.

**Class Practice**
1. Make a square.
2. Make a rectangle.
3. Try different border values, such as `dashed` or `inset` (replace `solid` from above).
4. Adjust the `margin` and padding, and see its effect.
5. [Try out some new colors](http://www.color-hex.com/)
6. Can you make a circle?
7. Can you make an oval?
  - Change the `background-color` to something more desirable to your eyes.

**Debugging CSS**

## Resources
- [Front End Intro](https://github.com/sierraplatoon/html-front-end-intro)

## Assignments
- [Top Ten](https://github.com/sierraplatoon/html-top-ten)

