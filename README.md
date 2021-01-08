[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](http://www.gnu.org/licenses/gpl-3.0)
![ECMAScript >= 2016](https://img.shields.io/badge/-JavaScript-blue?logo=javascript&style=flat)

# Some JS to remember...

This is my personal JavaScript learning diary. Feel free to learn with me, commit your suggestions and :thanks: correct me. Looking forward to it!

### Content in the making:

1. [`const` > `let` > `var`](#001)
2. [`Arrows` are for fun](#002)
3. [Some `functions` think they are better](#003)
4. [Business as usual: `Arrays`](#004)
5. [Watch out, there is `Scope`](#005)
6. [`Closures` are closed source](#006)
7. [Construction site: `Classes`](#007)
8. [`Polymorphism` or `inherting` the right `prototype`](#008)
9. [About context and `this`](#009)
10. [Separate with `Encapsulation`](#010)
11. [Do not accidently `destructure` yourself](#011)
12. [Try to hold your `async` / `await` `promises`](#012)
13. [Do not `try` to `catch` it!](#013)
14. [`Refactoring` or die trying](#014)
15. [`Airbnb` is more than couchsurfing](#015)
16. [`Lint` the babelfish](#016)
17. [`Build` without rocket science](#017)
18. [Release your horse: `Deploy` it!](#018)

Happy coding :rocket:

> **Strict mode** is enabled in ES6 classes by default. For consistency use strict mode everywhere.
> 
> Read more about here:\
> https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode

***
## <a id="001"></a>`const` > `let` > `var`
```
//...
```
***
## <a id="002"></a>`Arrows` are for fun
```
//...
```
***
## <a id="003"></a>Some `functions` think they are better
```
//...
```
***
## <a id="004"></a>Business as usual: `Arrays`
```
//...
```
***
## <a id="005"></a>Watch out, there is `Scope`
```
//...
```
***
## <a id="006"></a>Closures` are closed source
```
//...
```
***
## <a id="007"></a>Construction site: `Classes`
```
//...
```
***
## <a id="008"></a>`Polymorphism` or `inherting` the right `prototype`
```
//...
```
***
## <a id="009"></a>About context and `this`
#### Scope vs context
Imagine a tree diagram. We have one **global scope** at the top and lots of **local scopes** underneath. As usual within a tree diagram local scope nodes could be nested. Visibility of elements is given vertically up the ladder back to the global scope. 

This means that:
- a variable existing in a local scope of a function **could not be accessed from outside** and nothing could get messed up
- the **namespace is not polluted**, cause an variable declared inside is not visible from the outside

In contrast: The **context** is where an element belongs to. A function / class / object (everything is an object!) gets declared or initialized somewhere. With the help of `this` you could refer to this context.

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
## <a id="010"></a>Separate with `Encapsulation`
***
```
//...
```
## <a id="011"></a>Do not accidently `destructure` yourself
***
```
//...
```
## <a id="012"></a>Try to hold your async/await promises
***
```
//...
```
## <a id="013"></a>Do not try, catch it!
***
```
//...
```
## <a id="014"></a>`Refactoring` or die trying
***
```
//...
```
## <a id="014"></a>Airbnb` is more than couchsurfing
***
```
//...
```
## <a id="014"></a>`Lint` the babelfish
***
```
//...
```
## <a id="014"></a>Build` without rocket science
***
```
//...
```
## <a id="014"></a>Release your horse: `Deploy` it!