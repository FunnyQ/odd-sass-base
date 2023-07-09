# odd-sass-base

A SASS library that provides a set of variables and mixins for building web applications.

## Install

To install this library, add it to the devDependencies section in your package.json file and run yarn install.

```json
{
  // ...
  "devDependencies": {
    // ...
    "odd-sass-base": "https://github.com/FunnyQ/odd-sass-base.git",
    // ...
  },
  // ...
}
```

In your main SASS file (e.g., assets/application.sass), which serves as the entry point for your SASS files, use the following template:

```sass
// application.sass
@use 'odd-sass-base' as *
@forward "odd-sass-base"
@forward 'odd-sass-base/sass/normalize'

// Customizations go here

+render-props-at-root
  --other-custom-property: 'can be defined here under this mixin'
```

### Nuxt 3 Projects

If you are using Nuxt, add vite sass config below to your `nuxt.config.js` file:

```ts
export default defineNuxtConfig({
  vite: {
    css: {
      preprocessorOptions: {
        sass: {
          sourceMap: true,
          // 👇 Add this line
          additionalData: `@use "@/assets/stylesheets/application" as *`
        }
      }
    }
  }
})
```

Then you can use this library in vue components (`<style lang="sass">` section, or separate sass files) without any additional configuration.

### Others

In files where you need to use variables or helpers, such as SASS files, `@use` this library using the following syntax:

```sass
@use 'odd-sass-base' as *

.product-grid
  +is-grid
// If there are any naming conflicts, you can also use
// a prefix of your choice to prevent them. For example:
@use 'odd-sass-base' as odd

.product-grid
  +odd.is-grid
```

Please note that **ordering in the main SASS file** is important, especially when you have customizations. See the [Customization](#customization) section for more details.

## Usage

### Customization

In the `abstracts` section, you will find various variables that can be customized, such as colors, font sizes, and more. You can **override** or **extend** these variables in your project.

```sass
// Use the mixin `add-color` to define a color
+add-color('primary', hsl(189, 79%, 45%))
+add-color('success', hsl(118, 79%, 45%))
+add-color('notice', hsl(214, 79%, 45%))
+add-color('warning', hsl(48, 79%, 45%))
+add-color('danger', hsl(353, 79%, 45%))

// Create an alias for a defined color using the `use-color` mixin
+add-color('ci-main', use-color('primary'))

// Similarly, there are mixins for font sizes (`add-font-size` and `use-font-size`),
// font weights (`add-font-weight` and `use-font-weight`),
// space sizes (`add-space-size` and `use-space-size`),
// and layer order (`add-layer` and `use-layer`)
```

Note that these customizations should be done before the `+render-props-at-root` mixin in your main SASS file. The defined variables will be used in `+render-props-at-root` to generate CSS Custom Properties as follows:

```css
:root {
  /* Prefix `c` for color */
  --c-primary: hsl(189, 79%, 45%);
  --c-success: hsl(118, 79%, 45%);
  --c-notice: hsl(214, 79%, 45%);
  /*...*/
  /* Prefix `fz` for font size */
  --fz-1: 1rem;
  --fz-2: 1.25rem;
  /*...*/
  /* Prefix `fw` for font weight */
  --fw-1: 100;
  --fw-2: 200;
  /*...*/
  /* Prefix `s` for space size */
  --s-iphone: 375px;
  --s-gap: 1rem;
  /*...*/
  /* Prefix `layer` for z-index */
  --layer-1: 10;
  --layer-notification: 70;
  /*...*/
}
```

The library also provides common helpers in the mixins section, such as +start-from, +is-flex, +has-lines-limit, +debug, and more. Check into the source code for more details.

---

#### Usage Example

```sass
// application.sass
@use 'odd-sass-base' as *
@forward "odd-sass-base"
@forward 'odd-sass-base/sass/normalize'
@forward 'custom/colors'
@forward 'custom/mixins'
@forward 'pages/index'

+render-props-at-root
```

```sass
// custom/_colors.sass
@use 'odd-sass-base' as *

+add-color('dark', hsl(0, 0%, 15%))
+add-color('light', hsl(0, 0%, 95%))
// ...
```

```sass
// custom/_mixins.sass
@use 'odd-sass-base' as *

=custom-mixin
  --background-color: var(--c-dark)
  width: min(100vw, var(--s-iphone))
  background-color: var(--background-color)
  +start-from(use-space-size('ipad'))
    --background-color: var(--c-light)
```

```sass
// pages/_index.sass
@use 'odd-sass-base' as *

.index-page
  +custom-mixin
  > .title
    font-size: var(--fz-large)
    font-weight: var(--fw-bolder)
  > .summary
    font-size: var(--fz-small)
    +has-lines-limit(3)
```
