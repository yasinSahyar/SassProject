Scss Style Guide Starter
Special thanks to Carwin Young (Github @carwin) whose docs this is based on

This is a guide for formatting Sass stylesheets, it's goals are:

To encourage consistency between developers in a repository.
Styles applying to an element should be in one place
Code should be easily find-able and legible by humans
Classes are mostly explicitly named (pointing to specific elements, not functionality)
CSS should be written so it is easier to maintain so it can have more longevity
As with most things, there will be exceptions to the rules set forth here. When you come across those exceptions, please note them via code comments.

Helper docs
Helper doc name
For usefule docs that are a bit long to go in README.md
Also good for external dogs, e.g. Sass Doc Annotation
Folder Structure
More info in each folder's readme...

Filename	Purpose
10_core	Sass resources: variables, mixins, functions
20_base	A collection of architecturally high-level styles for basic elements.
30_widget	Small elements, usually have less than 10 tags in their markup
40_component	Medium elements, they are larger than widgets, but not sections
50_section	Elements that take up an entire row in the page layout
60_template	Page templates, designs that are reused by many pages
70_page	Styles for unique pages, that only occur once on the site
98_admin	Styles for Drupal/CMS elements
99_utility	Single use classes that apply very specific effects like font-size, background, etc.
When creating new .scss files
Not all files have to be partials, if a piece of CSS is uncommon, or part of the admin experience it's better to make it a separate file and include it as a library.

The build will aggregate partials into styles.css and compile non-partial files separately, in an identical folder structure under CSS.

Basics
Use only soft tabs equalling two spaces. No hard TAB characters.
Remove trailing whitespace.
Do not define a unit for 0 values. (0px should be 0)
// comments will not be rendered on compile.
Indention & Spacing
Starting level selectors should have no indention.
Properties should be indented once (two spaces) under their selectors.
One space should follow a selector name before the opening declaration bracket.
Brackets should be on their own lines
One selector per line (create new line after a comma in the selector list)
Add one space between a property (after the colon) and value.
For example:

.selector,
.another .selector {
  text-align: center;
  vertical-align: middle;
}
Sass Formatting
Selector Nesting
Please try to limit the nesting of selectors as much as possible. Nesting tends to turn into a spaghetti mess of code and specificity issues.

A newline should precede and follow a selector being nested.
Place nested selectors after the parent's properties/includes/extends/etc...
For example:

.selector {
  color: #ffffff;
  background: #000000;

  .nested-selector {
    text-align: center;
    border-radius: 5px;
    color: #cecece;
  }
}
Mixins, Nesting, and You
When using mixins that act as containers for style declarations (such as @include breakpoint()), never nest a selector within the mixins brackets.
@includes etc... should be placed (when sensible) at the beginning of a selector's properties.
For example:

.selector {
  @extend %selector-to-extend;
  @include button--primary;
  color: #ffffff;
  background: #000000;
  @include breakpoint(lima) {
    color: #eeeeee;
  }
  @include breakpoint(papa) {
    color: #cccccc;
  }

  .nested-selector & {
    // Nested selector content here.
    // Notice the newlines.
  }
}
The goal is that all of the styles for an element should be in one place. This tends to be easier to debug than having all styles for a breakpoint in one place.

Extending
While Sass' @extend functionality is pretty, it causes a lot of maintenance nightmares, so use it sparingly. The one absolute rule is:

Do not @extend a selector onto a selector in a different file.

Generally it's best to use a @mixin; due to @extends incompatability with @media queries and it's unintentional @extends if the extended selector is used somewhere else.

Class Naming Conventions
This project leverages a Block-Element-Modifier syntax for naming classes. This produces rather lengthy class names but also provides quick, easily discernible information to developers.

The primary goal of this syntax is to reduce the specificity of CSS selectors.

The syntax looks like this:

.component
.component__element
.component__element--modifier
The class names in this syntax do not imply any structural hierarchy in regard to markup. More often than not, the base component class is the containing element of the sub-components, but this is not always the case. You may end up with a markup structure in which an element is placed above the base component like so:

<div class="component__wrapper">
  <div class="component">
    <!-- other markup here -->
  </div>
</div>
Elements should have the base class and the modifier classes, so it can be assumed that .component__element--variation is already getting the styles of .component__element.

<div class="component">
  <div class="component__element component__element--modifier"></div>
</div>
.component__element {
  // styles
}

.component__element--modifier {
  // separate or overriding styles
}
More documentation can be found on Get Bem

Class name prefixes
js- Prefix
The js- prefix on class names denotes that the class is provided by javascript. This is helpful in avoiding styles that get prefixed with .no-js, and can be added after an element is processed by JS, not when JS has started to run on the page.

For example:

// Component state
.js-component-name--initialized { }
.js-component-name--open { }
.js-component-name--active { }

// Component was created by JS
.js-component-name { }
u- Prefix
The u- prefix denotes a 'utilty class'. These are single function classes that have a specific purpose.

For example:

.u-clearfix { }
.u-visually-hidden { }
.u-float-right { }
.u-grid-container { }
.u-grid-item--wide { }
Utility classes should not be extended, e.g.

// BOOOOOOO!!!
.sidebar.u-grid-item { }
Styling Components
Keep specificity low
Ideally all selectors can be accomplished with one class, but this isn't always the case. Exceptions can be made when:

A parent component/element context changes something in the current component
There's a class or style that can't be changed or modified (in particular with 3rd party code)
JS is adding classes due to custom behaviors; it's much more performant to toggle one class on a wrapper and have a little more CSS specificity.
Keep element styles together
One of the big goals of this CSS architecture is to have one place that describes each piece. Individual classes should not be styled in different files or different blocks.

If an element has styles for different breakpoints, and is modified by different parent contexts, it should be written something like this:

.widget {
  font-size: 12px;
  color: #ffffff;
  background: #000000;
  transition: background 0.25s ease-in;
  @include breakpoint(lima) {
    font-size: 14px;
  }
  @include breakpoint(tango) {
    font-size: 16px;
  }

  &:hover {
    background: #333333;
  }

  // These styles could have gone in the .section component's file,
  // but we want to keep styles that effect .widget in one place.
  .section--featured & {
    font-size: 14px;
    background: #001133;
    @include breakpoint(lima) {
      font-size: 16px;
    }
    @include breakpoint(lima) {
      font-size: 18px;
    }

    &:hover {
      color: #002255;
    }
  }
}
There is one exception to this rule, and that is when something needs to be added to _shame.scss.

Other Conventions
Do not add units to 0 values.

// No.
margin: 0px auto;
// Yes.
margin: 0 auto;
Feel free to add styles with multiple values across lines, unfortunately sass-lint doesn't have a rule for that, so you'll have to disable and re-enable the indentation rule.

.component__button {
  // sass-lint:disable indentation
  transition:
    color 0.1s,
    background 0.25s,
    box-shadow 0.25s;
  // sass-lint:enable indentation
}
CSS Property Declaration Order
Styles should be declared from the ones that most effect the element's layout, to the least. Inside of the layout, the styles that effect the parent context (e.g. grid-item or flex-item styles) should be listed first.

Animation and transition values should be listed last.

This order is to help the coder understand how the item lays out by reading from top to bottom, while purely aesthetic and 'future tense' styles are collected at the bottom.

.selector {
  // Parent context styles
  grid-area: 1 / -1;
  justify-self: center;
  align-self: center;
  // Positioning declarations
  position: relative;
  top: 10px;
  z-index: 10;
  // Box model declarations
  display: inline-block;
  width: 100px;
  margin: auto;
  padding: 10px;
  border: 1px solid #333333;
  // Transformations
  transform: translate(10px);
  // Font settings
  font-size: 10px;
  line-height: 1.6;
  font-family: sans-serif;
  color: #ffffff;
  // Other declarations
  background: #000000;
  box-shadow: 2px 2px 3px #000;
  // Animations/transitions
  transition: background 0.25s ease-in;
  transition-delay: 0.25s;
}
Declaring Color
When possible, use one of the predefined Sass variables in place for colors. Otherwise, when declaring hexadecimal color values, always use the full hex value. Being consistent makes it much easier to find and replace should we need to.

// No.
color: #333;
// Yes.
color: #333333;
Comments
Sectioning comments should look like so, with bottom borders extending to 80 characters:

// This is a section
// -----------------------------------------------------------------------------
.selector {
  color: $blue;
}
Avoiding specificity wars and !important
Never use #id selectors
Ideally a class can be added to any element that only has an [id], but if that HTML cannot be updated please use an attribute selector instead. This will keep specificity lower so the project is easier to work with in the future.

// No.
#my-element { }

// Yes.
[id='my-element'] { }
Competing specificities
There is only one scenario when !important should be used; when an inline style that is unchangeable, or necessary needs to be overridden in a specific context.

If that is the case, consider putting the style in _shame.css with a good comment.

If the competing style is using an id no amount of classes in the selector will overrule an id, so the same amount of id's must be used in your selector.

If that is not the case (and there are the same amount of id's in your selector), first write the ideal selector. Let's say this selector only selects what it needs to, but isn't specific enough:

.widget__bit {
  .section--featured & { // My ideal selector
    // Styles
  }
}
Adding an ID will start specificity wars in the project, adding extra classes may change what it selects or create confusion down the road.

Though it is silly, the recommended approach is to add unnecessary attribute selectors:

.widget__bit {
  .section--featured &[class] {
    // Outputs: .section--featured .widget__bit[class]
    // Styles
  }
}
We've just said that the .widget__bit will have a class attribute, which of course it does. This adds 1 class worth of specificity. If that is not enough, attribute selectors can be chained until they have the specificity desired:

.widget__bit {
  .section--featured &[class][class][class][class][class] {
    // Outputs: .section--featured .widget__bit[class][class][class][class][class]
    // Styles
  }
}
Only use the bare minimum needed to make your style win.

What is _shame.scss?
This file is not to put people down, but to keep track of nasty CSS we either have to write, or couldn't figure out how to avoid at the time.

Each entry in the file should be documented where it shows up, and why it needed to be written the way it is.

The goal is if the non-optimal code gets in the way of another dev they'll have docs to know why it was necessary at the time, and if someone wants to kill some technical debt in the repo, this file could be a good place to start.

_shame.scss will also be loaded at the bottom of the cascade, so selectors in it will need as little specificity as possible.

SassDocs
We're not currently using SassDocs, but we're using the commenting standards, see: Sass Doc Annotations

Documentation is only necessary on mixins, functions, and some variables (if they're not self evident).
