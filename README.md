# Alex's Code Conventions
*Last Updated: July 14, 2015 2:33 PM*

## Forward

This document contains a very detailed outline of my most important JavaScript and CSS code conventions in a [Style Guide](http://styleguides.io/) format.

When each member of our team is collaborating on a project, we are all likely developing separate pieces of code. Ideally, each of those separate pieces of code should uniformly look and behave in such a way that they were produced by a single developer. There are many reasons behind this belief system:

- Easier to read and quickly inspect
- Uniform patterns allow others to quickly understand basic flow, meaning, and functionality
- The long-term value of software to an organization is in direct proportion to the quality of the codebase.
- Neatness counts

You can see some of our customized, high-level guidelines at the following site: [jsCode.org](http://jscode.org/03c498)


## Table of Contents

[Ember JS](#ember-js)

  1. [Ember Conventions](#ember-conventions)
  1. [Local Variables and Prototype Extensions](#local-variables-and-prototype-extensions)
  1. [Organizing Modules](#organizing-modules)
  1. [Controllers](#controllers)
  1. [Templates](#templates)

[Git](#git)

  1. [Creating Feature Branches](#creating-feature-branches)
  1. [Commit Contents](#commit-contents)
  1. [Commit Messages](#commit-messages)
  1. [Example Bug Workflow](#example-bug-workflow)

[Text Editor](#text-editor)

  1. [VIM](#vim)
  1. [Sublime Text](#sublime-text)

[Javascript](#javascript)

  1. [JS Style](#js-style)
  1. [JS Naming](#js-naming)
  1. [Variable Declarations](#variable-declarations)
  1. [Function Declarations](#function-declarations)
  1. [Primitive Literals](#primitive-literals)
  1. [Operator Spacing](#operator-spacing)
  1. [Object Literals](#object-literals)
  1. [Comment Annotations](#comment-annotations)
  1. [JS Rules of Thumb](#js-rules-of-thumb)

[CSS](#css)

  1. [CSS Style](#css-style)
  1. [File Structure](#css-file-structure)
  1. [CSS Naming](#css-naming)
  1. [CSS Rules of Thumb](#css-rules-of-thumb)

[HTML](#html)

  1. [Indentation and Line Breaks](#indentation-and-line-breaks)
  1. [Escaping Characters](#escaping-characters)
  1. [Smart Quotes](#smart-quotes)

[Resources](#resources)

  1. [JS Resources](#js-resources)
  1. [CSS Resources](#css-resources)

## Ember JS

### Ember Conventions
Follow all project naming and structure onvenstions as outlined in [Ember CLI](http://www.ember-cli.com/).

- All route and template names will be dasherized. (Names which are more than 1 word will be separated by the dash "-" character.)
- All *resources* will be in the top-level `templates/` directory.
- All *routes* will be pulled from `templates/` sub-directories matching their parent resource.
- All components will be placed in `templates/components/`
- All names corresponding to the same route will be the identical and will be placed into the appropriate directories. (controllers/, routes/, views/, templates/, etc.)

### Local Variables and Prototype Extensions
Do not use the prototype extension syntax as Ember is moving away from this convention. Create local variables from the Ember namespace.

```javascript
  // Good - Distilling each "module" from Ember down to its lowest level
  const {
    Component,
    computed,
    on
  } = Ember; // Shorthand ES6 Destructuring Syntax
  const { alias } = computed;

  export default Component.extend({
    first: alias('firstName'),
    last: alias('lastName'),

    fullName: computed('first', 'last', function() {
      /* Code */
    }),

    sayHello: on('didInsertElement', function() {
      /* Code */
    })
  });

  // Bad - Using prototype extensions. No local variables.
  export default Ember.Component.extend({
    first: Ember.computed.alias('firstName'),
    last: Ember.computed.alias('lastName'),

    fullName: function() {
      /* Code */
    }).property('first', 'last'),

    sayHello: function() {
      /* Code */
    }).on('didInsertElement')
  });
```

### Organizing Modules
 * Define your object's default values first.
 * Define single line computed properties second.
 * Define multi-line computed properties third.
 * Define actions last. This provides a common place where actions can be found in each module.
 * [Don't override init](http://reefpoints.dockyard.com/2014/04/28/dont-override-init.html). Unless you want to change an object's `init` function, perform actions by hooking into the object's `init` hook via `on`. This prevents you from forgetting to call `_super`.

```javascript
  //Good
  export default Ember.Component.extend({
    // Defaults
    tagName: 'span',

    // Single line Computed Properties
    post: alias('myPost'),

    // Multi-line Computed Properties
    authorName: computed('author.firstName', 'author.lastName', function() {
      /* Code */
    }),

    actions: {
      someAction: function() {
        /* Code */
      }
    }
  });
```

### Controllers
 * Define query params first. These should be listed above default values.
 * Do not use `ObjectController` or `ArrayController`, simply use `Controller`.
 * Alias your model. It provides a cleaner code, it is more maintainable, and will align well with future routable components within Ember.

```javascript
  // Good
  export default Controller.extend({
    user: alias('model')
  });
```

### Templates
 * Do not use partials. Always use components instead. Parials share scope with the parent view and components will provide a consistent scope.
 * Use block syntax 

```handlebars
  {{! Good }}
  {{#each posts as |post|}}

  {{! Bad - Using old non-block syntax}}
  {{#each post in posts}}
```

 * Use components in `{{#each}}` blocks. Contents of your each blocks should ideally be a single line. This will allow you to test the contents in isolation via unit tests, as your loop will likely contain more complex logic in this case.

```handlebars
  {{! Good }}
  {{#each posts as |post|}}
    {{post-summary post=post}}
  {{/each}}

  {{! Bad }}
  {{#each posts as |post|}}
    <article>
      <img src={{post.image}} />
      <h1>{{post.title}}</h2>
      <p>{{post.summar}}</p>
    </article>
  {{/each}}
```


## Git

### Creating feature branches
```bash
# Start the feature
git checkout -b feature/my-new-feature

# Sync a copy of the branch on the remote server
git push -u origin feature/my-new-feature

# Make individual atomic commits then push
git commit
git commit
git commit
...
git push

# For completing a feature get the most recent version of all the code.
git pull

# Merge the develop branch into the feature branch to make sure that nothing is outdated
git merge develop

# Finish the feature and Create PR
git push

# Delete the remote branch once merged
git push origin :feature/my-new-feature
```

### Commit Contents
- Every commit should be constrained to a single piece of functionality and its dependencies. (Atomicity)
- Find good points in the development process to pause and commit pieces of your effort – a good rule of thumb is that any time you feel pleased that you're making progress you should commit your changes.
- Don't make changes to unrelated functionality inside of a single commit.
- If you encounter a bug when developing a new feature or another issue that needs to be addressed you can follow the example below.

### Commit Messages
Write [good commit messages](http://robots.thoughtbot.com/post/48933156625/5-useful-tips-for-a-better-commit-message). Every commit message should explain in detail what it is the commit is trying to accomplish. The people reviewing the commit should be able to identify whether or not they should be the person reviewing it based solely upon the commit message.

This means that you should include:

- What types of functionality you worked on (JS, CSS, HBS). This doesn't have to be explicit, "update the login route event handler" would work to identify that you worked on JS.
- Explanations of why you used that particular approach to solve the problem, if necessary.
- A brief description of next steps if there is additional work that needs to be done.
- Do not remove default populated contents from commit messages. For example, merge commits will by default say what branch it was and what conflicts occurred during the merge.

### Example Bug Workflow
```bash
# Store your current working state in your feature branch
git stash save "currently working on foo"

# Move to the develop branch or a feature branch to address the issue
git checkout develop

# Fix Code and Commit the change
git add my-fix.js
git commit

# Write a good commit message.

# Push the new version to the origin server.
git push

# Get back to what it is you were working on.
git checkout feature/feature-name

# If you need the fix in your current branch in order to move forward.
git merge develop

# Add back your work in progress.
git stash pop
```


## Text Editor

Choose your favorite text editor. Some good options include:

 - [Sublime Text](https://www.sublimetext.com/)
 - [Atom](https://atom.io/)
 - [VIM](http://www.vim.org/)

### VIM
For VIM users, the following is a great resource:

[Vim Adventures](http://vim-adventures.com/)

### Sublime Text
For Sublime Text 2 users, I'm including a nice starting point for your user-specific `Preferences.sublime-settings` file:

```json
/*
  Preferences.sublime-settings
  Path: ~/Library/Application Support/Sublime Text 2/Packages/User/Preferences.sublime-settings
*/
{
  "auto_complete_commit_on_tab": true,
  "bold_folder_labels": true,
  "color_scheme": "Packages/Color Scheme - Default/Monokai.tmTheme",
  "default_encoding": "UTF-8",
  "default_line_ending": "unix",
  "detect_indentation": false,
  "draw_minimap_border": true,
  "draw_white_space": "all",
  "ensure_newline_at_eof_on_save": true,
  "file_exclude_patterns":
  [
    ".DS_Store",
    "Desktop.ini",
    "*.pyc",
    "._*",
    "Thumbs.db",
    ".Spotlight-V100",
    ".Trashes"
  ],
  "folder_exclude_patterns":
  [
    ".git"
  ],
  "font_size": 13,
  "highlight_line": true,
  "highlight_modified_tabs": true,
  "hot_exit": false,
  "ignored_packages":
  [
    "Vintage"
  ],
  "match_brackets": true,
  "match_brackets_angle": true,
  "open_files_in_new_window": false,
  "remember_open_files": true,
  "rulers":
  [
    120
  ],
  "save_on_focus_lost": true,
  "shift_tab_unindent": true,
  "show_encoding": true,
  "show_line_endings": true,
  "tab_size": 2,
  "translate_tabs_to_spaces": true,
  "trim_trailing_white_space_on_save": true,
  "word_separators": "./\\()\"':,.;<>~!@#$%^&*|+=[]{}`~?",
  "word_wrap": true,
  "wrap_width": 120
}

```


## JavaScript

### JS Style
- Use soft-tabs with a **2** space indent.
- Be generous with well-written and clear comments.
- Each line should be no longer than **120** characters.
  - Using *Sublime Text 2*, you can enforce this rule visually with a vertical ruler at 120 characters by customing your **User Settings** file. *(Shown above greater detail below in the `Sublime Text 2` section)*


### JS Naming
- For variables and functions, names should be limited to alphanumeric characters and, in some cases, the underscore character.
  - Do **NOT** use: the dollar sign ("$") in any names.
- Variable names should be formatted in camel case.
- The first word of a boolean variable name will be "is".
- The first word of a variable name should be a noun (not a verb).

```javascript
// Good
const accountNumber = "8401-1";

// Bad: Not camel case
const AccountNumber = "8401-1";
const account_number = "8401-1";

// Bad: Begins with a verb
const getAccountNumber = "8401-1";
```

- Function names shuld also be formatted using camel case.
- The first word of a function should be a verb (not a noun).

```javascript
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
- Always use `const` to declare variables.

```javascript
// Good
const car = new Mustang();

// Bad: Glabal variable
car = new Mustang();
```

- All variables should be declared before used.
- Always use one `const` declaration per variable.

```javascript
  // Good
  const store;
  const count = 10;
  const name  = "Alex";
  const found = false;
  const empty;

  // Bad: One `const` for multiple variables (much harder to spot global variable errors)
  const store,
      count = 10,
      name  = "Alex",
      found = false;
      empty;

  // Bad: Improper initialization alignment, Incorrect indentation
  const count       =10;
  const name = "Alex";
  const found  =    false;
  const empty;

  // Bad: Multiple declarations on one line
  const count = 10;
  const name  = "Alex";
  const found = false, empty;
```

- Keep variable declarations as the first statements of a function's body.
- Group `const` declarations, then group `let` declarations
- Never initialize a variable to `null` if it doesn't have an initial value. In this case you will **ONLY** declare the variable.

```javascript
  // Good
  const hogwarts = "Hogwarts School of Witchcraft and Wizardry";
  const Gryffindor = "Gryffindor";
  const Slytherin = "Slytherin";
  let quidditchMatchTime = new Date();
  let i;
  let length;
  
  // Bad: Improper Alignment. Multiple variables declared per line.
  const hogwarts = "Hogwarts School of Witchcraft and Wizardry", Gryffindor = "Gryffindor", Slytherin = "Slytherin";
  let quidditchMatchTime = new Date(),
    i;
  let length;
  
  // Bad: Not using `const` when appropriate. Initialized variables to `null`.
  let hogwarts = "Hogwarts School of Witchcraft and Wizardry";
  let Gryffindor = "Gryffindor";
  let Slytherin = "Slytherin";
  let quidditchMatchTime = new Date();
  let i = null;
  let length = null;
```

- Do not use global variables. Implied global variables should also never be used.

```javascript
  // Bad: Improper initialization alignment, Incorrect indentation
  const count       =10;
  const name = "Alex";
  const found  =    false;
  const empty;

  // Bad: Multiple var statements, Multiple declarations on one line
  const count = 10;
  const name  = "Alex";
  const found = false, empty;
```

### Function Declarations
- All functions should be declared before they are used to avoid hoisting confusion.
- There should be no space between the name of a function and the left parenthesis of its parameter list.
  - For anonymous functions, there should be no space between the `function` keyword and the parentheses.
- There should be one space between the right parenthesis and the left curly brace that begins the statement body.
- The body itself is indented two spaces and the closing right curly brace is aligned with the line containing the beginning of the declaration of the function.
- When a function is to be invoked immediately, the entire invocation expression should be wrapped in parens so that it is clear that the value being produced is the result of the function and not the function itself.

```javascript
  // Good
  function outer(c, d) {
    const e = c * d;

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

- Immediately invoked function expression format

```javascript
  //Good
  (() => {
    console.log('Herp Derp');
  })();
```

- Never user `arguments`, instead use the rest syntax `...`

```javascript
  //Good
  function concatAll(...args) {
    return args.join('');
  }
  
  //Bad: `arguments` is not explicit and not an actual JS `Array`
  function concatAll() {
    const args = [].slice.call(arguments);
    return args.join('');
  }
```

- Use the default parameter syntax rather than manipulating function arguments within the function body.

```javascript
  // Good
  function foo(opts = {}) {
    //...
  }
  
  // Bad: Mutating function arguments in the body.
  function foo(opts) {
    opts = opts || {};
    //...
  }
```

- Use arrow functions notation in place of a normally used anonymous function. Arrow functions will execute with the correct context of `this`.
- If the function body is small and will cleanly fit on one-line, the you may omit the brances and use the implicit return value.
- Always use parentheses around the arguments (including single arguments) for readability

```javascript
  // Good
  [1,2,3].map((x) => {
    return x * x;
  });
  
  // Even Better
  [1,2,3].map((x) => x * x);
  
  // Bad
  [1,2,3].map(function(number) {
    return number * number;
  });
```

### Primitive Literals
- Strings should appear on a single line. Don't use a slash to create a new line in a string.
- If you need multi-line strings use the template literal format.

```javascript
  // Good
  const name = "Alex DiLiberto";

  // Good
  const greeting = `Hello, my name
  is ${name}`;

  // Bad
  const greeting = "Hello, my name \
  is Alex DiLiberto.";
```

Numbers should be written as decimal integers, e-notation integers, hexadecimal integers, or floating-point decimals with at least one digit before and one digit after the decimal point. Do **NOT** use octal literals.

```javascript
// Good
let count = 10;

// Good
let price = 10.0;
let price = 10.00;

// Good
let num = 0xA2;

// Good
let num = 1e23;

// Bad: Hanging decimal point
let price = 10.;

// Bad: Leading decimal point
let price = .1;

// Bad: Octal (base 8) is deprecated
let num = 010;
```

- `null` should be used only in the following situations:

  1. To compare against an initialized variable that may or may not have an object value
  1. To pass into a function where an object is expected
  1. To return from a function where an object is expected

```javascript
// Good
function getPerson() {
  if (condition) {
    return new Person("Alex");
  } else {
    return null;
  }
}

// Bad: Testing against an uninitialized variable
let person;
if (person != null) {
  doSomething();
}

// Bad: Testing to see if an argument was passed
function doSomething(arg1, arg2, arg3, arg4) {
  if (arg4 != null) {
    doSomethingElse();
  }
}
```

- Never use `undefined` as a literal. To test if a variable has been defined, use the `typeof` operator.

```javascript
// Good
if (typeof variable == "undefined") {
  doSomething();
}

// Bad: Using undefined literal
if (variable == undefined) {
  doSomething();
}
```

### Operator Spacing
- Operators with two operands must be preceded and followed by a single space to make the expression clear.
- When parentheses are used, there should be no white space immediately after the opening paren or immediately before the closing paren.

```javascript
// Good
const found = (values[i] === item);

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

```javascript
// Good
let object = {
  key1: value1,
  key2: value2,

  func: function() {
    doSomething();
  },

  key3: value3
};

// Bad: Improper indenation, Missing blank lines around function
let object = {
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

```javascript
if (condition) {
  statements
} else if (condition) {
  statements
} else {
  statements
}
```


## CSS

### CSS Style
- Use soft-tabs with a **two** space indent.
- Put spaces after `:` in property declarations.
- Put spaces before `{` in rule declarations.
- Use hex color codes with each letter capitalized `#123ABC` unless using `rgba()`.
- Any `$variable` or `@mixin` that is used in more than one file should be put in `globals/`. Others should be put at the top of the file where they're used.

```scss
/* Good coding style example! */
.styleguide-format {
  border: 1px solid #0F0;
  color: #000;
  background: rgba(0,0,0,0.5);
}
```

### File Structure
In general, the CSS file organization should follow something like this:

```bash
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

### CSS Naming
Use ID and class names that are as short as possible but as long as necessary.

```scss
.nav {
  /* Instead of .navigation */
}
.author {
  /* Instead of .atr */
}
```

Do not concatenate words and abbreviations in selectors by any characters other than hyphens.

```scss
.demo-image {
  /* Instead of .demoimage or .demo_image */
}
```

ID names should be in lowerCamelCase (although, as mentioned above, this should be **AVOIDED** for CSS styling hooks)

```scss
#pageContainer {
}
```

Class names should be in lowercase, with words separated by hyphens. (as mentioned above).

```scss
.my-class-name {
}
```

HTML elements should be in all lowercase.

```scss
body,
div {
  /* Instead of BODY, DIV */
}
```

### CSS Rules of Thumb
- As a rule of thumb, do **NOT** nest further than 3 levels deep.
  - If you find yourself going further, consider reorganizing your rules (specificity needed or the layout of the nesting).
- Unit-less line-height is preferred because it does not inherit a percentage value of its parent element, but instead is based on a multiplier of the font-size.
- Long, comma-separated property values (such as collections of gradients or shadows) should be arranged across multiple lines.
- Avoid using ID selectors.
- Include a snippet of HTML in a CSS comment for situations where it would be useful for a developer to know exactly how a chunk of CSS applies to some HTML.
- Comments that refer to selector blocks should be on a separate line immediately before the block to which they refer.

```scss
/* Comment about this selector block. */
selector {
  property: value; /* Comment about this property-value pair. */
}
```

- Multiple selectors should each be on a single line, with no space after each comma.

```scss
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

```scss
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


## HTML

###Indentation and Line Breaks
Break to a new line if the tag contains another element.

```html
  <!-- Good -->
  <p>
    This is a
    <a href="#">link</a>.
  </p>

  <!-- Bad: All tags are grouped on a single line -->
  <p>This is a <a href="#">link</a>.</p>
```

In the above Good example note the period needs to be right after the anchor tag here because we do not want an extra space when the browser renders the HTML.

This also makes sense:

<h2>June 16<sup>th</sup></h2>
because there shouldn’t be a space or possibility of line break between the date and its ordinal indicator.

###Escaping Characters
Escape the following characters with HTML entity encoding to prevent context switching into any execution context, such as script, style, or event handlers.

 & --> &amp;
 < --> &lt;
 > --> &gt;
 " --> &quot;
 ' --> &#x27;
 / --> &#x2F;

###Smart Quotes
Use [Smart Quotes](http://smartquotesforsmartpeople.com/) when appropriate.


## Resources

### JS Resources
1. *Maintainable Javascript* by Nicholas C. Zakas
1. [Code Conventions for the JavaScript Programming Language](http://javascript.crockford.com/code.html) by Douglas Crockford
1. [AirBnB Javascript Styleguide](https://github.com/airbnb/javascript)

### CSS Resources
1. [GitHub CSS Styleguide](https://github.com/styleguide/css)
1. [Google HTML/CSS Style Guide](http://google-styleguide.googlecode.com/svn/trunk/htmlcssguide.xml)
1. [Principles of writing consistent, idiomatic CSS](https://github.com/necolas/idiomatic-css) by Nicolas Gallagher
1. [My HTML/CSS coding style](http://csswizardry.com/2012/04/my-html-css-coding-style/) by Harry Roberts
1. [Improving Code Readability With CSS Styleguides](http://coding.smashingmagazine.com/2008/05/02/improving-code-readability-with-css-styleguides/) by Vitaly Friedman
1. [ThinkUp Code Style Guide: CSS](https://github.com/ginatrapani/ThinkUp/wiki/Code-Style-Guide:-CSS) by Gina Trapani
1. [Wordpress CSS Coding Standards Handbook](http://make.wordpress.org/core/handbook/coding-standards/css/)
1. [CSS Style Guides](http://css-tricks.com/css-style-guides/) by Chris Coyier
