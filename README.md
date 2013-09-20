Code Conventions
======================
*Last Updated: September 20, 2013 11:46 AM*

This document contains a very detailed outline of the most important JavaScript and CSS code conventions.

When working with a team of developers, you are all likely developing separate pieces of code. Ideally, I believe that those separate pieces of code should all uniformly look like they were produced by one developer. There are many reasons behind this:

- Easier to read and quickly inspect
- Uniform patterns allow others to quickly understand basic flow, meaning, and functionality
- The long-term value of software to an organization is in direct proportion to the quality of the codebase.
- Neatness counts


JavaScript
------------------------
### Style
- Use soft-tabs with a **two** space indent.
- Be generous with well-written and clear comments.
- Each line should be no longer than **120** characters.
	- Using *Sublime Text 2*, you can enforce this rule visually with a vertical ruler at 120 characters by customing your **User Settings** file.

```
/* Preferences.sublime-settings */
{
  ...other settings...
  
  "rulers": [120]
}
```

### Naming
- For variables and functions, names should be limited to alphanumeric characters and, in some cases, the underscore character.
	- Do **NOT** use: the dollar sign or backslack characters in any names.
- Variable names should be formatted in camel case.
- The first word of a variable name should be a noun (not a verb).

```
// Good
var accountNumber = "8401-1";

// Bad: Not camel case
var AccountNumber = "8401-1";
var account_number = "8401-1";

// Bad: Begins with a verb
var getAccountNumber = "8401-1";
```

- Function names shuld also be formatted using camel case.
- The first word of a function should be a verb (not a noun)

```
// Good
function doSomething() {
  // code
}

// Bad: Not camel case
function Do_Something() {
  // code
}

// Bad: Begins with a noun
function car() {
  // code
}
```

### Variable Declarations
- All variables should be declared before used.
- The `var` statements should be the first statements in the function body.
- When listing multiples variables in the same logical block, it is preferred that each variable be given its own line and properly indented (variable comment is to be used as needed).
- Define all variables at the top of the function. JavaScript currently does not have block scope, so defining variables in blocks can confuse programmers who are experienced with other C family languages.
- Use of global variables should be minimized. Implied global variables should never be used.

```
  // Good
  var count   = 10,
      name    = "Alex",
      found   = false,
      empty;

  // Bad: Improper initialization alignment, Incorrect indentation
  var count   =10,
  name = "Alex",
  found   = false,
  empty;

  // Bad: Multiple var statements, Multiple declarations on one line
  var count = 10,
      name  = "Alex";
  var found = false, empty;
```

### Function Declarations
- All functions should be declared before they are used. Inner functions should immediately follow the `var` declarations. This helps make it clear what variables are included in its scope.
- There should be no space between the name of a function and the left parenthesis of its parameter list.
	- For anonymous functions, there should be no space between the `function` keyword and the parentheses.
- There should be one space between the right parenthesis and the left curly brace that begins the statement body.
- The body itself is indented two spaces and the closing right curly brace is aligned with the line containing the beginning of the declaration of the function.
- Use of global functions should be minimized.
- When a function is to be invoked immediately, the entire invocation expression should be wrapped in parens so that it is clear that the value being produced is the result of the function and not the function itself.

```
  // Good
  function outer(c, d) {
    var e = c * d;

    function inner(a, b) {
      return (e * a) + b;
    }

    return inner(0, 1);
  }

  // Good
  div.onclick = function() {
    return false;
  };

  // Bad: Improper spacing of first line, left brace on wrong line
  function doSomething (arg1, arg2)
  {
    return arg1 + arg2;
  }
```

### Primitive Literals
Strings should appear on a single line. Don't use a slash to create a new line in a string.

```
// Good
var name = "Alex DiLiberto";

// Bad
var greeting = "Hello, my name \
is Alex DiLiberto.";
```

Numbers should be written as decimal integers, e-notation integers, hexadecimal integers, or floating-point decimals with at least one digit before and one digit after the decimal point. Do **NOT** use octal literals.

```
// Good
var count = 10;

// Good
var price = 10.0;
var price = 10.00;

// Good
var num = 0xA2;

// Good
var num = 1e23;

// Bad: Hanging decimal point
var price = 10.;

// Bad: Leading decimal point
var price = .1;

// Bad: Octal (base 8) is deprecated
var num = 010;
```

`null` should be used only in the following situations:

1. To initialize a variable that may later be assigned
2. To compare against an initialized variable that may or may not have an object value
3. To pass into a function where an object is expected
4. To return from a function where an object is expected

```
// Good
var person = null;

// Good
function getPerson() {
  if (condition) {
    return new Person("Alex");
  } else {
    return null;
  }
}

// Bad: Testing against an uninitialized variable
var person;
if (person != null) {
  doSomething();
}

// Bad: Testing to see if an arugment was passed
function doSomething(arg1, arg2, arg3, arg4) {
  if (arg4 != null) {
    doSomethingElse();
  }
}
```

Never use `undefined` as a literal. To test if a variable has been defined, use the `typeof` operator.

```
// Good
if (typeof variable == "undefined") {
  doSomething();
}

// Bad: Using undefined literal
if (variable == undefined) {
  doSomething();
}
```

### Operator/Parentheses Spacing
- Operators with two operands must be preceded and followed by a single space to make the expression clear.
- When parentheses are used, there should be no white space immediately after the opening paren or immediately before the closing paren.

```
// Good
var found = (values[i] === item);

// Good
if (found && (count > 10)) {
  doSomething();
}

// Bad: Missing spaces, Extra space after opening paren, Extra space around argument
for ( i=0; i<count; i++) {
  process( i );
}
```

### Object Literals
- The opening brace should be on the same line as the containing statement.
- Each property-value pair should be indented one level with the first property appearing on the next line after the opening brace.
- If the value is a function, it should wrap under the property name and should have a blank line both before and after the function.

```
// Good
var object = {
  key1: value1,
  key2: value2,

  func: function() {
    doSomething();
  },

  key3: value3
};

// Bad: Improper indenation, Missing blank lines around function
var object = {
               key1: value1,
               key2: value2,
               func: function() {
                 doSomething();
               }
          };
```

### Comment Annotations
Comments may be used to annotate pieces of code with additional information. The acceptable annotations are:

1. `TODO` - Indicates that the code is not yet complete. Information about the next steps should be included.

2. `HACK` - Indicates that the code is using a shortcut to achieve its results.

3. `XXX` - Indicates that the code is problematic and should be fixed as soon as possible.

4. `FIXME` - Indicates that the code is problematic and should be fixed soon. Less important than `XXX`.

5. `REVIEW` - Indicates that the code needs to be reviewed for potential changes.

### Rules of Thumb
- Always use `===` and `!==` to avoid type coercion errors.
- Blank lines improve readability by setting off sections of code that are logically related. Use as needed.
- A `return` statement with a value should not use parentheses unless they make the return value more obvious in some way.
- The `if` class of statements should have the following form:

```
if (condition) {
  statements
} else if (condition) {
  statements
} else {
  statements
}
```

- Variables should not be declared in the initialization section of a `for` statement.
- The `for` class of statements should have the following form:

```
var i,
    len;
for (init; condition; update) {
  statements
}

for (variable in object) {
  statements
}
```


CSS
------------------------
### Style
- Use soft-tabs with a **two** space indent.
- Put spaces after `:` in property declarations.
- Put spaces before `{` in rule declarations.
- Use hex color codes with each letter capitalized `#123ABC` unless using `rgba()`.
- Any `$variable` or `@mixin` that is used in more than one file should be put in `globals/`. Others should be put at the top of the file where they're used.

```
/* Good coding style example! */
.styleguide-format {
  border: 1px solid #0F0;
  color: #000;
  background: rgba(0,0,0,0.5);
}
```

### Structure
In general, the CSS file organization should follow something like this:

```
  css/
  ├── global/
  │   ├── components/
  │   │   ├── button.scss
  │   │   └── checkbox.scss
  │   ├── header.scss
  │   └── footer.scss
  ├── layout/
  │   ├── _base.scss
  │   └── _login.scss
  ├── mixins/
  │   ├── _font.scss
  │   └── _navigator.scss
  └── vendor/
      └── _media-queries.scss
```

### Naming
Use ID and class names that are as short as possible but as long as necessary.

```
.nav {
  /* Instead of .navigation */
}
.author {
  /* Instead of .atr */
}
```

Do not concatenate words and abbreviations in selectors by any characters other than hyphens.

```
.demo-image {
  /* Instead of .demoimage or .demo_image */
}
```

ID names should be in lowerCamelCase (although, as mentioned above, this should be **AVOIDED** for CSS styling hooks)

```
#pageContainer {
}
```

Class names should be in lowercase, with words separated by hyphens. (as mentioned above).

```
.my-class-name {
}
```

HTML elements should be in all lowercase.

```
body,
div {
  /* Instead of BODY, DIV */
}
```

### Rules of Thumb
- As a rule of thumb, do **NOT** nest further than 3 levels deep.
	- If you find yourself going further, consider reorganizing your rules (specificity needed or the layout of the nesting).
- Unit-less line-height is preferred because it does not inherit a percentage value of its parent element, but instead is based on a multiplier of the font-size.
- Long, comma-separated property values (such as collections of gradients or shadows) should be arranged across multiple lines.
- Avoid using ID selectors.
- Include a snippet of HTML in a CSS comment for situations where it would be useful for a developer to know exactly how a chunk of CSS applies to some HTML.
- Comments that refer to selector blocks should be on a separate line immediately before the block to which they refer.

```
/* Comment about this selector block. */
selector {
  property: value; /* Comment about this property-value pair. */
}
```

- Multiple selectors should each be on a single line, with no space after each comma.

```
selector1,
selector2,
selector3,
selector4 {
}
```

- Broad selectors allow us to be efficient, yet can have adverse consequences if not tested. Location-specific selectors can save us time, but will quickly lead to a cluttered stylesheet. Exercise your best judgement.
- Always avoid "Magic Numbers".
	- These are numbers that are used as quick fixes on a one-off basis.
	- Just because if works for your one scenario, doesn't mean it will work for all examples and permutations.

```
/* 
 Magic Number example. 
 AVOID this whenever possible!
*/
.box {
  margin-top: 37px;
}
```

- Strive to only include selectors that include semantics.
	- A `span` or `div` holds none.
	- A `heading` has some.
	- A class defined on an element has plenty.


Resources
------------------------
### Javascript
1. *Maintainable Javascript* by Nicholas C. Zakas
2. [Code Conventions for the JavaScript Programming Language](http://javascript.crockford.com/code.html) by Douglas Crockford

### CSS
1. [GitHub CSS Styleguide](https://github.com/styleguide/css)
2. [Google HTML/CSS Style Guide](http://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml)
3. [Principles of writing consistent, idiomatic CSS](https://github.com/necolas/idiomatic-css) by Nicolas Gallagher
4. [My HTML/CSS coding style](http://csswizardry.com/2012/04/my-html-css-coding-style/) by Harry Roberts
5. [Improving Code Readability With CSS Styleguides](http://coding.smashingmagazine.com/2008/05/02/improving-code-readability-with-css-styleguides/) by Vitaly Friedman
6. [ThinkUp Code Style Guide: CSS](https://github.com/ginatrapani/ThinkUp/wiki/Code-Style-Guide:-CSS) by Gina Trapani
7. [Wordpress CSS Coding Standards Handbook](http://make.wordpress.org/core/handbook/coding-standards/css/)
8. [CSS Style Guides](http://css-tricks.com/css-style-guides/) by Chris Coyier
