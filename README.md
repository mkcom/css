# CSS / Sass Styleguide

*A mostly reasonable approach to CSS and Sass*

## Table of Contents

  1. [CSS](#css)
    - [Formatting](#formatting)
    - [Comments](#comments)
    - [OOCSS and BEM](#oocss-and-bem)
    - [ID Selectors](#id-selectors)
    - [JavaScript hooks](#javascript-hooks)
  1. [Sass](#sass)
    - [Syntax](#syntax)
    - [Ordering](#ordering-of-property-declarations)
    - [Mixins](#mixins)
    - [Placeholders](#placeholders)
    - [Nested selectors](#nested-selectors)


## CSS

### Formatting

* Use soft tabs (4 spaces) for indentation
* Prefer camelCasing over dashes in class names.
* Do not use ID selectors!
* When using multiple selectors in a rule declaration, give each selector its own line.
* Put a space before the opening brace `{` in rule declarations
* In properties, put a space after, but not before, the `:` character.
* Put closing braces `}` of rule declarations on a new line
* Put blank lines between rule declarations

**Bad**

```css
.avatar{
    border-radius:50%;
    border:2px solid white; }
.no, .nope, .not_good {
    // ...
}
#lol-no {
  // ...
}
```

**Good**

```css
.avatar {
    border-radius: 50%;
    border: 2px solid white;
}

.one,
.selector,
.perLine {
    // ...
}
```

### Comments

* Prefer line comments (`//` in Sass-land) to block comments.
* Prefer comments on their own line. Avoid end-of-line comments.
* Write detailed comments for code that isn't self-documenting:
  - Uses of z-index (try to avoid this anyway)
  - Compatibility or browser-specific hacks (prefer not to use any hacks!)

### OOCSS and BEM

We encourage some combination of OOCSS and BEM for these reasons:

  * It helps create clear, strict relationships between CSS and HTML
  * It helps us create reusable, composable components
  * It allows for less nesting and lower specificity
  * It helps in building scalable stylesheets

**OOCSS**, or “Object Oriented CSS”, is an approach for writing CSS that encourages you to think about your stylesheets as a collection of “objects”: reusuable, repeatable snippets that can be used independently throughout a website.

  * Nicole Sullivan's [OOCSS wiki](https://github.com/stubbornella/oocss/wiki)
  * Smashing Magazine's [Introduction to OOCSS](http://www.smashingmagazine.com/2011/12/12/an-introduction-to-object-oriented-css-oocss/)

**BEM**, or “Block-Element-Modifier”, is a _naming convention_ for classes in HTML and CSS. It was originally developed by Yandex with large codebases and scalability in mind, and can serve as a solid set of guidelines for implementing OOCSS. _We only changed the double dashes/underscores to single ones for better readability._

  * CSS Trick's [BEM 101](https://css-tricks.com/bem-101/)
  * Harry Roberts' [introduction to BEM](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)

**Example**

```html
<article class="listingCard listingCard-featured">

  <h1 class="listingCard_title">Adorable 2BR in the sunny Mission</h1>

  <div class="listingCard_content">
    <p>Vestibulum id ligula porta felis euismod semper.</p>
  </div>

</article>
```

```css
.listingCard { }
.listingCard-featured { }
.listingCard_title { }
.listingCard_content { }
```

  * Compound words in class names must be written in camelCase.
  * `.listingCard` is the “block” and represents the higher-level component
  * `.listingCard_title` is an “element” and represents a descendant of `.listingCard` that helps compose the block as a whole.
  * `.listingCard-featured` is a “modifier” and represents a different variation on the `.listingCard` block.

### ID selectors

While it is possible to select elements by ID in CSS, it should generally be considered an _anti-pattern_. ID selectors introduce an unnecessarily high level of [specificity](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity) to your rule declarations, and they are not reusable.

For more on this subject, read [CSS Wizardry's article](http://csswizardry.com/2014/07/hacks-for-dealing-with-specificity/) on dealing with specificity.

### JavaScript hooks

Avoid binding to the same class in both your CSS and JavaScript. Conflating the two often leads to, at a minimum, time wasted during refactoring when a developer must cross-reference each class they are changing, and at its worst, developers being afraid to make changes for fear of breaking functionality.

We recommend creating JavaScript-specific camelCased classes to bind to, prefixed with `.js`:

```html
<button class="btn btn-primary jsRequestToBook">Request to Book</button>
```

There are also state classes prefixed with `.js` or `.has` (for grammatical reasons) which may only be added or removed by Scripts:

```html
<div class="tabs isActive">First tab</div>
```

## Sass

### Syntax

* Use the `.scss` syntax, never the original `.sass` syntax
* Order your `@extend`, regular CSS and `@include` declarations logically (see below)

### Ordering of property declarations

1. `@extend` declarations

    Just as in other OOP languages, it's helpful to know right away that this “class” inherits from another.

    ```scss
    .btn-green {
        @extend %btn;
        // ...
    }
    ```

2. Property declarations

    Now list all standard property declarations, anything that isn't an `@extend`, `@include`, or a nested selector.

    ```scss
    .btn-green {
        @extend %btn;
        background: green;
        font-weight: bold;
        // ...
    }
    ```

3. `@include` declarations

    Grouping `@include`s at the end makes it easier to read the entire selector, and it also visually separates them from `@extend`s.

    ```scss
    .btn-green {
        @extend %btn;
        background: green;
        font-weight: bold;
        @include transition(background 0.5s ease);
        // ...
    }
    ```

4. Nested selectors

    Nested selectors, _if necessary_, go last, and nothing goes after them. Add whitespace between your rule declarations and nested selectors, as well as between adjacent nested selectors. Apply the same guidelines as above to your nested selectors.

    ```scss
    .btn {
        @extend %btn;
        background: green;
        font-weight: bold;
        @include transition(background 0.5s ease);

        .icon {
            margin-right: 10px;
        }
    }
    ```

### Mixins

Mixins, defined via `@mixin` and called with `@include`, should be used sparingly and only when function arguments are necessary. A mixin without function arguments (i.e. `@mixin hide { display: none; }`) is better accomplished using a placeholder selector (see below) in order to prevent code duplication.

### Placeholders

Placeholders in Sass, defined via `%selector` and used with `@extend`, are a way of defining rule declarations that aren't automatically output in your compiled stylesheet. Instead, other selectors “inherit” from the placeholder, and the relevant selectors are copied to the point in the stylesheet where the placeholder is defined. This is best illustrated with the example below.

Placeholders are powerful but easy to abuse, especially when combined with nested selectors. **As a rule of thumb, avoid creating placeholders with nested rule declarations, or calling `@extend` inside nested selectors.** Placeholders are great for simple inheritance, but can easily result in the accidental creation of additional selectors without paying close attention to how and where they are used.

**Sass**

```sass
// Unless we call `@extend %icon` these properties won't be compiled!
%icon {
    font-family: "Airglyphs";
}

.icon-error {
    @extend %icon;
    color: red;
}

.icon-success {
    @extend %icon;
    color: green;
}
```

**CSS**

```css
.icon-error,
.icon-success {
    font-family: "Airglyphs";
}

.icon-error {
    color: red;
}

.icon-success {
    color: green;
}
```

### Nested selectors

**Do not nest selectors more than three levels deep!**

```scss
.pageContainer {
    .content {
        .profile {
            // STOP!
        }
    }
}
```

When selectors become this long, you're likely writing CSS that is:

* Strongly coupled to the HTML (fragile) *—OR—*
* Overly specific (powerful) *—OR—*
* Not reusable


Again: **never nest ID selectors!**

If you must use an ID selector in the first place (and you should really try not to), they should never be nested. If you find yourself doing this, you need to revisit your markup, or figure out why such strong specificity is needed. If you are writing well formed HTML and CSS, you should **never** need to do this.
