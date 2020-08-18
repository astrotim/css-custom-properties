# Using CSS Custom Properties

Notes for a talk on the topic. 

## Key takeaways

- Fallbacks
- Variable stacks
- `initial` keyword
- Proximity and inheritance
- Component variants
- Theming
- Dark mode
- DRY and the logic fold
- With JavaScript
- Taming source order
- Houdini

## Definiton

CSS Custom Properties are property names prefixed with `--` containing a value that can be used in other declarations using the `var()` function.

Custom properties are **scoped** to the elements they are declared on, and participate in the **cascade**.

## Basics

Like preprocessor variables (Sass, PostCSS):

```css
$colour-primary: #fdc605;

.primary-thing {
   background-color: $colour-primary;
}
```

We can define a variable for reuse:

```css
:root {
  --colour-primary: #fdc605;
}

.primary-thing {
   background-color: var(--colour-primary);
}
```

### Fallback

The `var()` function accepts a fallback value:

```css
.thing {
  color: var(--thing-text, black);
}
```

But it only accepts a single fallback - a limitation is required to support fallbacks with a comma inside them, such as `background-image` or font stacks. 

### Variable stacks

We can workaround this by nesting another `var` as the fallback, allowing us to create 'variable stacks`.

```css
/* link.css */
border-color: var(--link-hover, var(--link-text, var(--colour-assistant)));
/* list-item.css */
color: var(--list-number, var(--colour-assistant, var(--colour-accent)));
```

We've done this in the branding API - more details below.

## Proximity and inheritance

In the absense of a 'local' custom property, the `var()` function will inherit the value from the property in closest proximity.

[Explain it with a rainbow](https://codepen.io/astrotim/pen/rNeOjrv?editors=1100) 🌈.

This allows for using custom properties other than where they are defined.

### Component variants

Using the inheritance behaviour, we can expose sets of CSS Custom Properties to create simple APIs for component variants.

[The "detail header" example](https://codepen.io/astrotim/pen/poJjrPa?editors=0110).

### `initial` and invalid

Setting a custom property value to `initial` will make it fallback to the next available value.

If a value is invalid, such as `20px` for a colour, this sets the entire property value to `initial`.

Explain it some more 🌈.


## Theming

CSS Variables enable theming independent of CSS structure. We can make use of the fallback values to set defaults and expose named custom properties for specific overrides.

This is the basis of the Nucleus branding API. Each branded component implements one of the three base palette colours as a default: primary, accent or assistant. These colours can then be overridden where necessary. 

```scss
/* button.css */
border-color: var(--button-fill, var(--colour-accent));

/* link.css */
color: var(--link-text, var(--colour-assistant));
```

### Theme config

To set up a new theme, an application simply needs to define the palette of three colours,

```css
:root {
  --colour-primary: hsl(47, 98%, 51%);
  --colour-accent: hsl(47, 98%, 51%);
  --colour-assistant: hsl(214, 100%, 40%);
}
```

### Dark mode

For backgrounds, borders and utility text, we similarly defined a paletee of reversible greys which can be redefined for a dark context

```css
/* light mode */
:root {
  --tint-1: hsl(0, 0%, 20%);
  --tint-2: hsl(0, 0%, 40%);
  --tint-3: hsl(0, 0%, 60%);
  --tint-4: hsl(0, 0%, 80%);
  --tint-5: hsl(0, 0%, 90%);
  --tint-6: hsl(0, 0%, 95%);
}

/* dark mode */
@media (prefers-color-scheme: dark) {
  :root {
    --colour-assistant: #4d9aff;
    --tint-2: hsl(0, 0%, 60%);
    --tint-6: hsl(0, 0%, 10%);
  }
}
```

## The logic fold

Mike Riethmuller has coined a term for an interesting concept based on using CSS Custom Properties: _the logic fold_.

The idea here is that the relevant variable values are positioned at the top of the file where it can be made easier to trace how a custom property changes. Then "below the fold" is the declarative CSS which implements these variables.

### Button example

[CodePen link](https://codepen.io/astrotim/pen/BaKNqgB?editors=0100)


## DRY: reducing repetition

Similar to using preprocessed variables, custom properties allows for removing repetition resulting in more readable code.

[CodePen example - grid columns](https://codepen.io/astrotim/pen/poybyrO?editors=0100).

## Taming source order

When using an authoring system such as CSS Modules in conjunction with Webpack, one does not have control over the final source order of the compiled CSS file. This is determined by the order in which imports are resolved during the build process.

While the encapsulation afforded by CSS Modules is greatly beneficial, one annoying side effect is that two selectors of equal specificity may be compiled in a different order, with application styles coming before library styles. The hacky workaround to ensure the correct styles are applied is to chain the selector to itself to 'win' by specificity.

CSS Custom Properties allow us to avoid needing to reach for this hack.

[CodePen example](https://codepen.io/astrotim/pen/OJMeZrM?editors=1100)



## Simplification

Something like a `linear-gradient` can have some pretty hostile syntax. This can be greatly simplified with custom properties.

[CodePen example - gradients](https://codepen.io/astrotim/pen/gOPgEwy?editors=0100).

## With JavaScript

```js
element.style.setProperty("--variable-name")
```

**Example**

Credit to Lea Verou for this demo.

```js
var root = document.documentElement;

document.addEventListener("mousemove", evt => {
	let x = evt.clientX / innerWidth;
	let y = evt.clientY / innerHeight;

	root.style.setProperty("--mouse-x", x);
	root.style.setProperty("--mouse-y", y);
});
```

[CodePen demo link](https://codepen.io/astrotim/pen/BaKopza?editors=0110)

### Houdini

Houdini is a set of low-level APIs that exposes parts of the CSS engine, giving developers the power to extend CSS by hooking into the styling and layout process of a browser’s rendering engine.

In short, Houdini gives a developer building blocks in JavaScript to allow writing new kinds of CSS

```css
.bg {
  --square-odd: skyblue;
  --square-even: white;
  background-image: paint(checkerboard);
}
```

[CodePen example - CSS Paint API](https://codepen.io/astrotim/pen/YzqybrE?editors=0110)

## References

- [A Strategy Guide To CSS Custom Properties](https://www.madebymike.com.au/writing/a-strategy-guide-to-css-custom-properties/) - Mike Riethmuller, May 2018 
- [A user’s guide to CSS variables](https://increment.com/frontend/a-users-guide-to-css-variables/) - Lea Verou, May 2020 
- [Using Custom Property “Stacks” to Tame the Cascade](https://css-tricks.com/using-custom-property-stacks-to-tame-the-cascade/) - Miriam Suzanne, June 2020 
- [Patterns for Practical CSS Custom Properties Use](https://css-tricks.com/patterns-for-practical-css-custom-properties-use/) - Tyler Childs, Oct 2019
- [CSS Custom Properties In The Cascade](https://www.smashingmagazine.com/2019/07/css-custom-properties-cascade/) - Miriam Suzanne, July 2019
- [CSS Variables Secrets (talk)](https://leaverou.github.io/talks/css-variables/#intro) - Lea Verou, 2017  
- [Houdini: Maybe The Most Exciting Development In CSS You’ve Never Heard Of](https://www.smashingmagazine.com/2016/03/houdini-maybe-the-most-exciting-development-in-css-youve-never-heard-of/) - Philip Walton, March 2016
- [Global and Component Style Settings with CSS Variables](https://www.sarasoueidan.com/blog/style-settings-with-css-variables/) - Sara Soueidan, June 2020
