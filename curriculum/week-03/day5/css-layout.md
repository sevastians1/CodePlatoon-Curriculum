# CSS Layout

## Topics Covered / Goals
- Display
- Position
- Z-index
- float
- overflow
- pseudo classes
    
> The purpose of this lecture is to learn how to use the most fundamental CSS properties for laying out a page, such as `display` and `position`. You should be able to place elements precisely on an HTML page, and be able to make a pixel-perfect recreation of a web page, given only a picture. 

### Display
> All elements have a default display type. Display types have very important nuances that affect the way elements appear on a page. The main display types are:
- `block` by default, take up the full width of a page, line break before and after
- `inline` tightly wraps content, no true dimensions
- `inline-block` best of both worlds, has dimensions but will also share horizontal line space
- `none` completely removes the element from the document flow
- `flex` creates a flex-container, a new feature that is useful for complex layouts where elements must be centered in a row or column (we'll talk about this later)

The following table includes the most commonly used tags and their default display types:

| display        | tags                                         |
|----------------|----------------------------------------------|
| `block`        | `body`, `div`, `section`, `p`, `header`, ... |
| `inline`       | `span`, `i`, `b`, `em`, `strong`, `sup`, ... |
| `inline-block` | `img`                                        |

Create several more divs that all have the class `.box`.

```html
<div id="boxes">
    <div class="box">Some Content</div>
    <div class="box" id="theBox"><strong>Some Content</strong></div>
    <div class="box">Some Content</div>
    <div class="box">Some Content</div>
    <div class="box">Some Content</div>
    <div class="box">Some Content</div>
    <div class="box">Some Content</div>
    <div class="box">Some Content</div>
    <div class="box">Some Content</div>
    <div class="box">Some Content</div>
    <div class="box">Some Content</div>
    <div class="box">Some Content</div>
</div>
```

> By default, divs have `display:block`. 
Use the inspector to see that this default style is being applied by the browser. Notice how each box takes up the full width of the page. 
> By default, `strong` elements have `display:inline`. 
Use the inspector to see that this default style is being applied by the browser. Notice how the strong tag's dimensions are the same as the text it contains.
It may be necessary to use the 'computed' tab in the inspector, instead of the 'styles' tab, to show the default styles that the browser applies.

In the linked stylesheet, add `display: inline-block` to the `.box` class.

```css
.box {
    height: 100px;
    width: 100px;
    padding: 2px;
    margin: 10px;
    border: 2px solid black;
    display: inline-block;
}
```

Refresh the page to see how the divs now form multiple rows. 

### How do we know how these divs will line up?
> By default, all elements are subject to the "document flow", meaning that they "fall" into the top left corner, then line up to the right.
> When there's no more space on the current line, elements form a new line below, to the left. 
> Some CSS styles can change the way elements flow (e.g. `display:block`, `display:inline-block`) and some styles can remove elements from the document flow entirely (e.g. `display:none`).
> During this lecture, we'll see other examples of styles that change how elements flow. 

### How do we know how many divs I can fit in one row? (box-sizing)
> Our divs have `width:100px;`. If our window is 1000px wide, you might think that we could fit 10 divs in one row.
> However, the element's width only includes the content, not the padding, margin, or border. 
> If we take the rest of the box model into account, each div actually takes up 100px + 2(2px) + 2(2px) + 2(10px) = 128px.
> We can make our lives easier by setting `* { box-sizing: border-box }`. This means that the border and padding should be included when calculating the element's width.
> For example, if we set `* { box-sizing: border-box }`, then our divs only take up 104px, because the padding and border are included in the 100px width. Only the element margin adds to the total space it takes up.
> The benefit of this is that we can now make small adjustments to our padding and border, without messing up the rest of our layout.
> The default value for `box-sizing` is `content-box`, meaning that only the content is included in the width. Another option is `box-sizing:margin-box`, which means that all layers of the box model are included when calculating the element's width.

Use the inspector to temporarily change the padding or border of one of the boxes. 
The size of the content changes dynamically so that the total width remains the same while the padding or border change.

### What's that extra space between the divs? It's not margin, or padding. What's going on?
> "**IMPORTANT**: One of the nuances of using `inline-block` is that **white-space** is still calculated in the layout of `inline-block` elements. This effectively creates a gap between `inline-block` elements, meaning the boxes will have this gap between them that is not a result of the `margin` or `padding` properties. In fact, the gap one sees is the actual `space` character!"
> One way to get rid of this space is to set the font-size of parent element wrapping the `inline-block` to **zero**
> However, doing this will also set the font-size of child elements to zero, so you may have to re-establish a sensible font-size for child elements. Unlike most CSS properties, font-related properties are inherited from parent to child.

```css
#boxes {
    font-size: 0; /* 0px is the same as 0inches, which is the same as 0%, etc etc. If you have 0 of something, the units don't matter. */
}

#boxes * {
    font-size: 12px;
}
```
### Why is there extra space around the edge of the screen?
> Remember that `body` is itself an html element, and it can have styles applied to it. By default, many browsers apply margin to the body. 

```css
body {
    margin: 0;
}
```
    
### Positioning
> Often, we want to position elements in ways that are totally different from how elements normally flow on the page.
> We can take more control over where an element displays on the page using the CSS `position` property. 

#### Static Position
> By default, all elements have `position:static`. Static elements are subject to the normal document flow, which we've been learning about today.
> Elements with `position:static` are considered to be **not positioned**. Elements with any other value for their `position` property are considered **positioned**.

#### Relative Position
> The next simplest value for `position` is `relative`. An element with `position:relative` mostly behaves the same way as an element with `position:static`, except:
- An element with `position:relative` is technically **positioned**. 
- An element with `position:relative` can be nudged in any direction using properties top, left, bottom, and right.
    - For example, setting `top:20px` pushes the element **down** 20px.
    - Don't forget, these properties (top, left, bottom, right) do **nothing** to static elements. 

Add position:relative to theBox to demonstrate relative positioning. 

```css
#theBox {
    position: relative;
    top : 20px;
    right: 30px;
}
```

> Even though `#theBox` appears to be floating off in space, it actually takes up space in its usual location, as if it were static. 
> You can tell because all the other divs are in their usual locations. They don't get pushed around when we add `top` or `left` to `#theBox`.

#### Fixed Position

> Fixed position removes an element from the document flow entirely, and positions it relative to the window. This is easy to understand from an example.

Add several dozen more `.box`es, enough that the page can be scrolled.

```css
#theBox {
    position: fixed;
    bottom : 20px;
    right: 30px;
}
```

> This will position `#theBox` in the lower right corner of the screen, 20px from the bottom edge, and 30px from the right edge.
> As we scroll down the page, you can see that `#theBox` stays in the same corner of the screen, and doesn't seem to interact with other elements on the page.
> `position:fixed` is commonly used for navbars, paywalls, and the worst kinds of advertisements. 

#### Absolute Position

> `position:absolute` is the most complicated value for `position`, so pay attention!
> An element with `position:absolute` is removed from the document flow entirely, and is positioned relative to its closest **positioned** parent.

To demonstrate `position:absolute`, we'll need to make some new html.

```html
<div class="outer">
    <div class="middle">
        <div class="inner"></div>
    </div>
</div>
```

```css
.outer {
    height: 400px;
    width: 400px;
    background-color:black;
    position: relative;
}
.middle {
    height: 300px;
    width: 300px;
    background-color: green;
}
.inner {
    height: 50px;
    width: 50px;
    background-color: red;
    position: absolute;
    right: 5px;
    bottom: 10px;
}
```

Using the inspector, notice how the `.inner` div is positioned relative to the bottom right edge of the `.outer` div, not the `.middle` div, because the `.outer` div is positioned, while the `.middle` div is not positioned.

### z-index
> z-index controls the position of an element on the z-axis (towards or away from the user).
> Because screens are flat, there are no units to the z-index, it's just a ranking of what's on top of what. 
> Be careful, a page can have multiple 'stacking contexts', so an element with a high z-index might not appear on top of another element with a low z-index, if they're in different stacking contexts.
> Best practice: set z-indexes in increments of 10, so that other developers can put elements above, below, or in between yours. 

### Floats
> Floating an element moves it to the left or right side of its container.
> The purpose of floating an element is to wrap text around images, like in a magazine or newspaper layout. 
> Floating an element also has many side effects which can be difficult to reason about. To keep your code simple, only use floats for wrapping text around images.
> If you just need to move an element to the side of its container, `position:absolute` or `text-align:right` are probably better choices. 

```css
    .pretendThisIsAnImage{
        height: 50px;
        width: 50px;
        background-color: red;
    }

    .floatMeRight {
        float: right;
    }

    .text-block {
        font-size: 24px;
    }
```

```html
        <div class="floatMeRight pretendThisIsAnImage"></div>
        <p class="text-block">
            This is a lot of text. This is a lot of text.
            This is a lot of text. This is a lot of text.
            This is a lot of text. This is a lot of text.
            This is a lot of text. This is a lot of text.
            This is a lot of text. This is a lot of text.
            This is a lot of text. This is a lot of text.
            This is a lot of text. This is a lot of text.
            This is a lot of text. This is a lot of text.
            This is a lot of text. This is a lot of text.
            This is a lot of text. This is a lot of text.
            This is a lot of text. This is a lot of text.
        </p>
```

> Now, you can see that the text is wrapped around the paragraph. 
> This might seem a bit odd, because the 'image' appears to be inside the `p`, even though the 'image' is a sibling of the `p`, not a child.

### Clear
> Elements with the `clear` property will refuse to share horizontal space with floated elements on the specified side.

```css
    .text-block {
        clear: left;
    }
```

Show students how the above styles do nothing, because no elements are floating to the left of the text block.

```css
    .text-block {
        clear: right;
    }
```

Show students how the above styles force the text block to render below the floated element. 

### Overflow
> Normally, html elements will automatically expand their size to accomodate all of their children. 
> However, if you explicitly set a height on an element, and it has more content than will fit in that height, the content will overflow from the container.

Set a fixed height `#boxes`.

```css
#boxes {
    height: 700px;
}
```

All the boxes on the page won't fit in 700px of vertical space. Use the inspector to see how the content is flowing outside of its container.

> If we want to control what happens when there is more content than fits in its container, we can set the `overflow` property.
> The most common value is `overflow:hidden` which makes the overflowing content invisible. 

```css
#boxes {
    height: 700px;
    overflow: hidden;
}
```

> If you want the extra content to be accessible without messing up your layout, then `overflow:scroll` will make `#boxes` scrollable.
> If you think that `#boxes` will have too much content only **some** of the time, you may want to use `overflow:auto`. This will make the scrollbars appear only when necessary. 

```css
#boxes {
    height: 700px;
    overflow: scroll;
}
```

Briefly show students that they can now scroll the `#boxes` div to view all the boxes that don't fit in 700px.

### Pseudo and Dynamic Classes
> Pseudo and dynamic classes are created as a result of user actions. For example:

```HTML
a:link    { color: green; }
a:visited { color: yellow; }
a:active { color: red; }
a:hover { color: orange; }
input:checked { height: 50px; width: 50px; }
input:valid { color: green; }
input:invalid { color: red; }
input:focus { color: gray; }
li:first-child {}
li:nth-child(2) {}

```
> Let's create an html file, put this CSS in the `style` tag and see the above in action:

> The first line of code would change the color of the links on the page to green, and if those links had been previously visited, they would be yellow.

> A commonly used dynamic class is `hover`.

```HTML
a:hover {
  text-decoration: none;
  color: blue;
  background-color: yellow;
}
```

## External Resources
- [Learn Layout](https://learnlayout.com/)

> The above code would cause links on the page to change colors only when hovered upon by the cursor.

## Assignments
- [LinkedIn](https://github.com/sierraplatoon/html-linkedin.git)


