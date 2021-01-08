[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)
![ECMAScript >= 2016](https://img.shields.io/badge/-JavaScript-blue?logo=javascript&style=flat)

# Some JS to remember...

This is my personal JavaScript learning diary. Feel free to learn with me, commit your suggestions and :thanks: correct me. Looking forward to it!

### Content in the making:

  - [`const` > `let` > `var`](#const--let--var)
  - [`Arrows` are for fun](#arrows-are-for-fun)
  - [Some `functions` think they are better](#some-functions-think-they-are-better)
  - [Business as usual: `Arrays`](#business-as-usual-arrays)
  - [Watch out, there is `Scope`](#watch-out-there-is-scope)
  - [Closures` are closed source](#closures-are-closed-source)
  - [Construction site: `Classes`](#construction-site-classes)
  - [`Polymorphism` or `inherting` the right `prototype`](#polymorphism-or-inherting-the-right-prototype)
  - [About context and `this`](#about-context-and-this)
      - [Scope vs context](#scope-vs-context)
      - [Global context](#global-context)
      - [The more context, the better...](#the-more-context-the-better)
      - [Arrow functions to the rescue!](#arrow-functions-to-the-rescue)
  - [Separate with `Encapsulation`](#separate-with-encapsulation)
  - [Do not accidently `destructure` yourself](#do-not-accidently-destructure-yourself)
  - [Try to hold your async/await promises](#try-to-hold-your-asyncawait-promises)
  - [Do not try, catch it!](#do-not-try-catch-it)
  - [`Refactoring` or die trying](#refactoring-or-die-trying)
  - [Airbnb` is more than couchsurfing](#airbnb-is-more-than-couchsurfing)
  - [`Lint` the babelfish](#lint-the-babelfish)
  - [Build` without rocket science](#build-without-rocket-science)
  - [Use git!](#use-git)
  - [Release your horse: `Deploy` it!](#release-your-horse-deploy-it)

Happy coding :rocket:

> **Strict mode** is enabled in ES6 classes by default. For consistency use strict mode everywhere.
> 
> Read more about here:\
> https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode

***
## `const` > `let` > `var`
```
//...
```
***
## `Arrows` are for fun
```
//...
```
***
## Some `functions` think they are better
```
//...
```
***
## </a>Business as usual: `Arrays`
```
//...
```
***
## </a>Watch out, there is `Scope`
```
//...
```
***
## </a>Closures` are closed source
```
//...
```
***
## </a>Construction site: `Classes`
```
//...
```
***
## </a>`Polymorphism` or `inherting` the right `prototype`
```
//...
```
***
## </a>About context and `this`
### Scope vs context
Imagine a tree diagram. We have one **global scope** at the top and lots of nested **local scopes** underneath. Visibility of elements is given vertically up the ladder back to the global scope. 

This means that for a variable in a local scope of a function:
- It could **not be accessed from outside**
- It could be **accessed from the local scopes underneath in the same nest**
- The **global namespace is not polluted**

In contrast to the scope the **context** is where an element belongs to. A function / class / object, which are objects under the hood, is declared or initialized at some point somewhere. This is the associated context. With the help of `this` you could refer to this context (Be aware of [`arrow functions`](#arrow-functions-to-the-rescue)).

#### Global context

```javascript
function foo() {
    console.log(this)
}
foo() // undefined
```

The context of a function `foo` is *window(browser) / global(node) / module(node) or undefined(strict mode)* by default. Lets call it the **global context** from now on.

#### The more context, the better...

Be aware of the sneaky keyword `new`. This sets a new context so that `this` refers to the newly created object `foo`:

```javascript
function foo() {
    console.log(this)
}
const bar = new foo(); // foo {}
```

This is useful if you want to setup objects with some arguments like through factory functions:

``` javascript
function foo(val) {
    this.prop = val;
    console.log(this.a);
}
const bar = new foo('1234'); // '1234'
```

Now that we know about the sneaky `new` keyword you may want to use `class` too. In the following example `bar` is set to a new instance of `Foo`. This is done by using the keyword `new` so that the context of the newly created instance of `Foo` is set to theirself. When the method is invoked `this` is indeed an instance of `Foo`.

```javascript
class Foo {
    method = function() {
        console.log(this instanceof Foo)
    }
}
const bar = new Foo()
bar.method(); // true
```
Lets call another method on `Foo` with a return function. Of course the anonymous function invoked by calling the `method` on the object has the context bind to the object (instance of the class) itself. Instead the anonymous return function lost context and has no reference to the object. This is why the interpreter falls back to the global context, which is `undefined` in `'strict-mode'`.

```javascript
class Foo {
    method = function() { 
        console.log(this instanceof Foo) // true
        return function() { console.log(this instanceof Foo) }
    }
}
const bar = new Foo()
bar.method()() // false
```
### Arrow functions to the rescue!
***
Besides the improved optics and the reduced writing effort the so called arrow function *- introduced with ECMAScript 6 (ES6) -* solves this loosing context problem that can otherwise only be solved with some effort (see: `.bind(), .apply(), .call()`).

```javascript
class Foo {
    method = function() {
        return () => { console.log(this instanceof Foo) }
    }
}
const bar = new Foo()
bar.method()() // true
```

The `=>` creates no own context. Instead it **inherits the context from its nearest surrounding function** which is in this case the anonymous function which context is set to the object itself by the fact, that it is declared within the local scope of the method on an object. This results in an `this` as a reference to the object. Assume an object property `cat`, this is reachable by `this.cat` inside the arrow function.

Assume a `MVC` (Model-View-Controller) where the Model doesn't know what the View does and the other way around. The controller plays man in the middle, hopefully catches all events on both side and transfers them to the opposite side. 

To establish a simple connection, lets use the arrow function as its best and avoid nasty `bind/apply/call`.

*[controller]*
```javascript
class Controller {
    constructor(model, view) {
        this.model = model
        this.view = view
        
        // Explicit binding 
        // Glue the model & controller together
        this.model.bindOnAddData(this.onAddData)
    }

    onAddData = (data) => {
        this.view.doSomething(data) // connection to the view
    }
}

const mvc = new Controller(new Model, new View)
```
*[model]*
```javascript
class Model {
    constructor() {
        this.data = ['data'] // the storage
    }

    bindOnAddData(callback){
        this.onAddData = callback
    }

    addData(str) { // ES6 Shorthand for addData = function(){}
        this.data.push(str) // push str to this.data;
        this.onAddData(this.data);
    }
}
```
The controller constructor binds his `onAddData` method to the `onAddData` method of the model, so that the controller will be informed when the model invokes `onAddData`. The controllers `onAddData` method needs to be an arrow function to preserve the "controller" context when it is invoked in the context of `onAddData` on the model.
***
## Separate with `Encapsulation`
***
```
//...
```
## Do not accidently `destructure` yourself
***
```
//...
```
## Try to hold your async/await promises
***
```
//...
```
## Do not try, catch it!
***
```
//...
```
## `Refactoring` or die trying
***
```
//...
```
## Airbnb` is more than couchsurfing
***
```
//...
```
## `Lint` the babelfish
***
```
//...
```
## Build` without rocket science
***
```
//...
```
## Use git!
***
```
//...
```
## Release your horse: `Deploy` it!