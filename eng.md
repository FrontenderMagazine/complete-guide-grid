<article id="post-177225">
This article is **in-progress**. We're trying to stay on-top of grid layout and
make sure we have some information about it. But know that grid is extremely new,
not well supported, and in-flux in browserland still.

The `grid` property in CSS is the foundation of Grid Layout. It aims at fixing
issues with older layout techniques like[float][1] and [inline-block][2], which
both have issues and weren't really designed for page layout.

You start by establishing a grid context on an element:

    .area {
      display: -ms-grid; /* prefix for IE 10 */
      display: grid; /* or inline-grid */
    }

Then you define what that grid layout will be like. Think columns and rows like
a spreadsheet (or table). Then, you can define for each child a column and a row
(resulting in a cell
). **No markup changing is involved; everything is done through CSS.**

Along with the fact this method fixes the issues we encounter with older layout
techniques, its main benefit is**you can display a page in a way which can
differ from the flow order**.

### Properties

Since CSS Grid Layout is a whole module and not a single property, it gathers a
bunch of properties meant to be used together. Some are meant to be set on the 
parent (grid) element, others on the children.

### The \`fr\` Unit

The `fr` unit can be used for `grid-rows` and `grid-columns` values. It stands
for*"fraction of available space"*. Think of it as *percentages for available
space* when you've taken off fixed-sized and content-based columns/rows. As the
spec says:

> The distribution of fractional space occurs after all 'length' or content-
> based row and column sizes have reached their maximum.
>

### Coming Soon

The properties listed so far are the very basics to use the grid layout; they
are also the first (and currently only) properties to be supported in this 
module.

According to the official specifications, there are a lot more involved
including:

*   `grid-template`: allows grid definition through a template of identifiers
*   `grid-column-position`: current `grid-column` since `grid-column `is
    supposed to be a shorthand for
   `grid-column-position` and `grid-column-span` 
*   `grid-row-position`: same as above
*   `grid-position`: shorthand for `grid-column-position` and 
    `grid-row-position` 
*   `grid-span`: shorthand for `grid-column-span` and `grid-row-span` 
*   `grid-area`: shorthand for `grid-column-position`, `grid-row-position`, 
    `grid-column-span` and `grid-row-span` 
*   `grid-auto-columns`: change default size for columns
*   `grid-auto-rows`: change default size for rows
*   `grid-auto-flow`: allows grid-items to automatically flow in available
    cells
   

Unfortunately, these properties are currently unsupported. We'll update this
article over time to document and include examples.

### Example

Consider a structure like the following:

    <div class="main">My awesome content here</div>
    <footer class="footer">Some informations here</footer>
    <header class="header">My site title goes here</header>
    <aside class="sidebar">Here is my side content</aside>

Note how the markup order has absolutely no impact on the final rendering.

    body {
     /* First, we define body as a grid element */
     display: grid;
       
     /* Then, we define the number of columns we want by setting their dimensions */
     /* Beware, gaps between columns will be actual columns too */
     /* 1. This means there are 3 columns: 
      * the first one is 200px wide
      * the second one will be a margin (1%) 
      * the third one will occupy the remaining space 
      */
     grid-columns: 200px 1% 1fr; /* 1 */
       
     /* Now we define the number of rows and their dimensions */
     /* 2. this means there are 5 rows: 
      * the first one will be sized according to its content
      * the second one will be a margin
      * the third one will be sized according to its content
      * the fourth one will be a margin as well
      * the last one will be sized according to its content 
      */
     grid-rows: auto 15px auto 15px auto; /* 2 */
       
     /* The body element is now a 3*5 grid. */
    }
     
    /* Both the header and the footer will be full width, so we have to make them occupy the 3 columns */
    .header, 
    .footer {
      grid-column-span: 3;
    }
    
    /* Let's define in which row the header will be: the first one */
    .header {
      grid-row: 1;
    }
     
    /* Same for the footer: the last one so the fifth (remember margins count as cols/rows */
    .footer {
      grid-row: 5;
    }
     
    .sidebar {
      /* The sidebar will occupy the first column which is 200px wide */
      grid-column: 1;
      /* And the 3rd row (there are the header and a margin before) */
      grid-row: 3;
    }
     
    .main {
      /* The main content will be on the 3rd column and the 3rd row */
     grid-column: 3;
     grid-row: 3;
    }

With a few other lines of CSS to handle styling issues, we have a decent layout
with absolutely no floats, no inline-blocks, no defined widths, no defined 
heights and no margins and completely independant of the flow order. Pretty neat,
right?

    [Check out this Pen!][3]

If you see the 4 parts one under the other, your browser doesn't support CSS
Grids. Please switch to a supported browser to enjoy the demo.

Now let's add a 2nd sidebar to, for instance, display ads. It will be on the
right side of the main content. There are a few tweaks to do in order to make 
everything works.

    /* We first change the grid-columns definition on the grid element to add 2 new columns: one margin and one sidebar */
    body {
      display: grid;
      grid-columns: 200px 1% 1fr 1% 100px;
      grid-rows: auto 15px auto 15px auto;
      /* We could have writen grid-rows: auto (15px auto)[2] */
    }
    
    /* Both the header and the footer will expand to 5 columns instead of 3 */
    .header, .footer {
      grid-column-span: 5;
    }
    
    .ads {
      grid-column: 5;
      grid-row: 3;
    }

Done! We now have a layout with full-width header and footer, 3-columns on the
main part including 2 fixed-width columns with about 13 lines of CSS.

    [Check out this Pen!][4]

If you see the 5 parts one under the other, your browser doesn't support CSS
Grids. Please switch to a supported browser to enjoy the demo.

What about responsive design? Easy as a pie. Let's slightly tweak our design
when under 600px device width.

    @media all and (max-width: 600px) {
      body {
        /* First, we make the body 1-column and 5 rows spaced by 4 margin rows */
        grid-columns: 1fr;
        grid-rows: auto (1% auto)[4];
        /* This is the same as:
         * grid-rows: auto 1% auto 1% auto 1% auto 1% auto;
         */
      }
      
      /* Then we say to all elements they belong in the only existing column */
      .header, .ads, .sidebar, .main, .footer {
        grid-column: 1;
      }
      
      /* Now we define their row; don't forget spacing rows! */
      .header  { grid-row: 1; }
      .ads     { grid-row: 3; }
      .main    { grid-row: 5; }
      .sidebar { grid-row: 7; }
      .footer  { grid-row: 9; }
      
      /* We even can create grids inside grids */
      .ads {
        display: grid;
        grid-columns: 1% (32% 1%)[3]; /* 1% 32% 1% 32% 1% 32% 1% */
        grid-rows: 2; /* One for the title, one for the images */
      }
      
      .ads h2  { grid-row: 1; grid-column-span: 3; }
      .ads img { grid-row: 2; }
      .ads img:nth-of-type(1) { grid-column: 2; }
      .ads img:nth-of-type(2) { grid-column: 4; }
      .ads img:nth-of-type(3) { grid-column: 6; }
    }

Have a look at [this Pen][5] and resize your browser to see the magic!

Here is a grid system made with CSS Grid Layout and Sass:

    [Check out this Pen!][6]

If you see all items one under the other, your browser doesn't support CSS
Grids. Please switch to a supported browser to enjoy the demo.

If you feel uncomfortable with the idea of having columns and rows as margins,
I guess you can still use actual margins on element but that defeats the purpose
a bit. Or you could use a CSS preprocessor to ease you the calculations; here is
a[demo][7].

### Related

### Other Resources

### Browser Support {#browser-support}

| Chrome | Safari | Firefox | Opera | IE  | Android | iOS  |

| none   | none
| none    | none  | 10+ | none    | none
|

Support coming in WebKit/Blink and Gecko (currently in some nightly builds).</
article
>

 [1]: http://css-tricks.com/almanac/properties/f/float/
 [2]: http://css-tricks.com/almanac/properties/d/display/#inline-block
 [3]: http://codepen.io/HugoGiraudel/pen/snrcj
 [4]: http://codepen.io/HugoGiraudel/pen/plhDk
 [5]: http://codepen.io/HugoGiraudel/pen/2befd6d225b69912af8561f7cb020124
 [6]: http://codepen.io/HugoGiraudel/pen/pHLlr
 [7]: http://codepen.io/HugoGiraudel/pen/bf9765f98147be052535f4d767a040eb