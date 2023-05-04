# Documentation

## Overview

Outlined below are different aspects of the css framework within this repository. From custom properties to media queries and utility classes this will dip your feet in terms of how things _can_ function within the framework.

Of course, there are numerous ways to solve problems. Especially with programming! So I encourage you to explore and keep learing on unique ways to solve unique problems.

### Custom Properties

> Property names that are prefixed with `--`, like `--example-name`, represent custom properties that contain a value that can be used in other declarations using the `var()` function. Custom properties are scoped to the element(s) they are declared on, and participate in the cascade: the value of such a custom property is that from the declaration decided by the cascading algorithm." - [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)

We can leverage the functionality of custom properties to create an abstract system that allows us to make application-wide changes in a matter of minutes.

There are three total layers to this system: The global layer, the semantic layer, and the contextual layer. For the remainder of this section I will refer to custom properties as tokens.

We define these tokens all within the same selector, the :root {} selector. When discussing tokens we can consider the :root {} element to be a "global" selector of sorts, as any token defined within that element will cascade throughout our application.

```
:root {
    // Global Tokens
    --clr-black: black;
    --clr-white: white;

    // Semantic tokens
    --color-neutral-100: var(--clr-white);
    --color-neutral-900: var(--clr-black);

    // Contextual Tokens
    --button-background-color: var(--color-neutral-900);
    --button-text-color: var(--color-neutral-100);
}
```

You may be wondering: Why add the semantic layer? Why not just assign the global tokens to the contextual tokens. What's the point of this extra step?

This is so we don't end up in a situation like this should the design specs change:

```
    // Global Variables
    --clr-black: darkgreen;
    --clr-white: white;

    // Contextual Layer
    --button-background-color: var(--clr-black);
    --button-text-color: var(--clr-white);
```

Now our naming convention wouldn't make much sense anymore, would it?

Our semantic token layer allows us to define as many global variables as we want and swap them out as values within the semantic layer. The semantic tokens are then assigned to our contextual tokens, which can also be considered component tokens.

By having these three layers we can maximize our control while retaining a level of simplicity that managing the application is still quite easy.

```
// Defining Tokens
:root {
    // Global Tokens
    --clr-black: black;
    --clr-white: white;

    // Semantic tokens
    --color-neutral-100: var(--clr-white);
    --color-neutral-900: var(--clr-black);

    // Contextual Tokens
    --body-background-color: var(--color-neutral-100);
    --button-background-color: var(--color-neutral-900);
}

// Using Tokens
body {
    background-color: var(--body-background-color);
}

.button {
    background-color: var(--button-background-color);
}
```

By managing our tokens in such a manner we can effectively define our contextual tokens once, use them in our block-level styling and expect consistency across our application due to our reuse of semantic tokens within our contextual tokens.

As you navigate and interact with this framework more you will become very accustomed to the system in place.

### Media Queries

> Media queries allow you to apply CSS styles depending on a device's general type (such as print vs. screen) or other characteristics such as screen resolution or browser viewport width. - [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/CSS/Media_Queries/Using_media_queries)

In our use case, Media queries provide **two** main purposes. First, to allow for simple responsive development. Second, to provide additional accessibility considerations for impaired users, or those in unfavorable environments.

Here's our HTML:

```
<main>
    <section class="grid-example">
        <div class="card">
            <span>🌮</span>
            <p>Tacos are in fact, the bomb!</p>
        </div>
        <div class="card" data-type="dark">
            <span>🌮</span>
            <p>Tacos are in fact, the bomb!</p>
        </div>
        <div class="card">
            <span>🌮</span>
            <p>Tacos are in fact, the bomb!</p>
        </div>
        <div class="card" data-type="dark">
            <span>🌮</span>
            <p>Tacos are in fact, the bomb!</p>
        </div>
    </section>
</main>
```

Let's say we have this beautiful one column grid on mobile with some different colored cards:

```
main {
  padding-block: 2rem;
  padding-inline: 1rem;
}

.grid-example {
  display: grid;
  grid-template-columns: minmax(0, 1fr);
  gap: 1rem;
}

.card {
  min-height: 20rem;
  background-color: lightslategray;
  display: flex;
  flex-direction: column;
  gap: 1rem;
  align-items: center;
  justify-content: center;
}

.card[data-type="dark"] {
  background-color: darkslategray;
  color: white;
}
```

![Single Column Grid](/src/assets/images/README/single-column.png)

Our content will fit nicely within this grid, expanding to fill as much room all of the available room (1fr, or 1 fractional unit). That's all fine and nice on mobile, but what if we wanted to change our grid from a one column to a two or even four column grid on larger viewports? We can do that by using the `width` media query as follows:

```
@media screen and (min-width: 45em) {
  .grid-example {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}
```

Now we'll have two columns on viewports larger than 45em (translates to 720px, more on [units here](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Values_and_units)) allowing us to have two columns instead of one.

![Two Column Grid](/src/assets/images/README/two-column.png)

And if we were to bump this up to a four column grid on an even larger viewport:

```
@media screen and (min-width: 60em) {
  .grid-example {
    grid-template-columns: repeat(4, minmax(0, 1fr));
  }
}
```

![Four Column Grid](/src/assets/images/README/four-column.png)

You can see how handy these medai queries are! It's important to note that the media query `width` feature can go in both directions, or even be at a fixed width. (min-width, max-width, width). When taking a mobile-first development approach you will mostly use the min-width variation as it will allow you to adjust styles as a scale up, whereas max-width is vice-veras.

As mentioned above the second main use case I personally find with media queries is accessibility. An example of how that would look can be found below:

```
@media (prefers-reduced-motion: reduce) {
  html:focus-within {
    scroll-behavior: auto;
  }

  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

This media query in particular takes into account individuals who experience motion sickness on the web and disables nearly all motion. The individuals who take the time to modify these settings do so as a means to ease their experiences online.

For more on accessibility [see here.](https://www.w3.org/standards/webdesign/accessibility)

### Utility Classes

This collection of files contains collections of classes within them. Each class generally serves one purpose, and serves it very well.

You will likely find yourself using the layout, container, and accessibility utility classes the most.The color and typography classes have been included for niche use cases, where setting up custom variables and tokens is overkill.

An example of a container utility class in use:

```
<main class="container" data-type="wide">
    <!--
        Main Content
    -->
</main>
```

This will set up a container based on the defined utility class:

```
.container {
  --padding: calc(var(--container-min-margin-inline) * 2);
  --max-width: var(--container-max-width);

  width: min(100% - var(--padding), var(--max-width));
  margin-inline: auto;
}

.container[data-type="wide"] {
  --max-width: var(--container-max-width-wide);
}
```

Within this container we can create grids, flexboxes, and other further containing elements that will let us more easily arrange our content.

If we wanted to perform a page-wide grid we could place these containers within the desired columns and rows, whether they be explicitly or implicitly defined.

### Vendors

This directory is dedicated to importing styles from third-party libraries (i.e. bootstrap). Its main function is separation of your teams work from that of others.

## Other Resources

These tools will help you save time.

### Can I Use

[This site](https://caniuse.com/) is dedicated to browser support. You can look up any feature and discover which browsers implemented it, when they did, or if it is still in the pipeline.

### Google Webfonts Helper

[This utility](https://gwfh.mranftl.com/fonts) is designed to skip the step of converting fonts from ttf to woff format. It works with the roughly 1,500 font faces available on [Google Fonts](https://fonts.google.com/).

Whenever you work with fonts it's always best practice to review licensing to avoid any possible copyright infringement. Most fonts found on [Google Fonts](https://fonts.google.com/) are licensed under the [Open Font License](https://scripts.sil.org/cms/scripts/page.php?site_id=nrsi&id=OFL) or the [Apache v2.0 License.](https://www.apache.org/licenses/LICENSE-2.0)

### Squoosh

[This is a utility](https://squoosh.app/) focused around image conversion. You can easily modify image formats and file sizes to best serve different images under different circumstances.

## Final Notes

Please keep in mind this system is how I personally prefer to work. I highly encourage you all to explore many different philosophies and methodologies throughout your education and career.

Happy coding!
