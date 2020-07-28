# Using CSS Custom Properties

## Key takeaways

- Variable stacks
- The logic fold
- fallbacks
- proximity and inheritance
- `initial` keyword
- Houdini and custom properties

## Definiton

CSS Custom Properties are property names prefixed with `--` containing a value that can be used in other declarations using the `var()` function.

Custom properties are **scoped** to the elements they are declared on, and participate in the **cascade**.

## Basic usage

Like preprocessor variables (Sass, PostCSS):

```css
$primary: #fdc605;


.primary-thing {
   background-color: $primary;
}
```

We can define a variable for reuse:

```css
:root {
  --primary: #fdc605;
}
.primary-thing {
   background-color: var(--primary);
}
```

### Fallback

The `var()` function accepts a fallback value:

```css
.thing {
  color: var(--thing-text, black);
}
```

We can take advantage of this with the concept of [variable stacks](https://css-tricks.com/using-custom-property-stacks-to-tame-the-cascade/).

CSS Variables work like normal CSS properties.

### Dynamic scope

Where custom properties are concerned, dynamically scoped means they are subject to inheritance and the cascade



Are inherited properties

Invalid at computed-value time = `initial`

In other words, use invalid to your advantage!

Use for pure data, not CSS values

About Houdini

Houdini is a set of low-level APIs that exposes parts of the CSS engine, giving developers the power to extend CSS by hooking into the styling and layout process of a browser’s rendering engine.

In short, Houdini gives a developer building blocks in JavaScript to allow writing new kinds of CSS

such as: `CSS.registerProperty()`

CSS Variables enable theming independent of CSS structure

Default default values are possible

`--colorD: var(--color, black);`

Create single property mixins (like function currying)

Example?

Custom Longhands

Example?

### Proximity and inheritance

This is my favourite capability.

CSS Custom Properties can be used other than where they are defined. They will inherit from the closest proximity definition, e.g

```html
// need context here
<section id="outer">
  <p>This text is red</p>
  <div id="inner">
    <p>This text is green!</p>
  </div>
</section>
```

Gotchas

Inheritance and recalculation

we can’t rely on inheritance for functions that need to be re-calculated, e.g. linear-gradient() because each property value is computed once per element, and the computed value is inherited

Using a universal selector `*` will make the property (function) available everywhere



- initial
- 