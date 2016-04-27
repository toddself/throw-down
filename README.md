# throw-down.js

A tiny library for building pure modular DOM components with a reliable life cycle.

```
npm install --save throw-down
```

## Features

 - Unopinionated, can be used with any DOM framework
 - Build your own helper methods as each are isolated in separate modules
 - Works well with yoyo/bel/vanilla DOM elements
 - Uses features available in browsers today instead of inventing new syntax/APIs
 - Allows DOM elements/components to have `load`/`unload`/`update` hooks
 - Compatible with vanilla DOM elements and vanilla JS data structures
 - Zero dependencies, doesn't require tons of dev dependencies
 - `0.5kb` minified + gzipped, small enough for UI components to include as a dependency

## About

Keeping track of a specific DOM node/element/component during DOM morphing is hard. `throw-down` makes it really easy. It provides life cycle hooks for your DOM components, so you can track of the life of the component as the DOM is morphed. Just `connect` your DOM component, and keep an eye on it with simple hooks that fire when the component is `constructed`, `loaded`, `mutated`, `unloaded`. That's it!

## Example

Used with the <a href="https://github.com/maxogden/yo-yo/">yoyo</a> UI framework

```js
const yo = require("yo-yo")
const connect = require("throw-down/connect")
const update = require("throw-down/update")(yo.update)

const Component = function(_yield) {
    var id, open = true

    function construct (_id) {
      id = _id
    }

    function loaded (node) {}
    function mutated (node) {}
    function unloaded (node) {}

    function toggle () {
      open = !open
      update(id, render())
    }

    function render () {
      return yo`<div><button onclick=${toggle}>Toggle</button> ${open && "Open!" || "Closed!"} ${_yield}</div>`
    }

    return connect(render, construct, loaded, mutated, unloaded)
}

document.body.appendChild(Component(Component()));
```

Without using `throw-down` ID's

```js
const yo = require("yo-yo")
const connect = require("throw-down/connect")
const update = require("throw-down/update")(yo.update)

const Component = function(_yield) {
    var el, open = true

    function track (node) {
      el = node
    }

    function toggle () {
      open = !open
      update(el, render())
    }

    function render () {
      return yo`<div><button onclick=${toggle}>Toggle</button> ${open && "Open!" || "Closed!"} ${_yield}</div>`
    }

    return connect(render, null, track, track)
}

document.body.appendChild(Component());
```

Here we are just keeping track of the components target element while the DOM is morphing.

## Installing

You can get it <a href="https://www.npmjs.com/package/throw-down">from npm</a>: `npm install --save throw-down`

## API

The API methods shipped with `throw-down` -- 3 methods in total

```js
const connect = require("throw-down/connect")
const retrieve = require("throw-down/retrieve")
const update = require("throw-down/update")(yo.update) // yoyo/morphdom helper
```

### (1) connect

Connects the DOM element target to `throw-down`

**Parameters**

-   `element` **Function** the render method that returns a pure DOM element you want to track
-   `constructed` **Function** fired once before the element is rendered
-   `added` **Function** fired when the target DOM element is loaded
-   `mutated` **Function** fired when the target DOM element is mutated
-   `removed` **Function** fired when the target DOM element is removed

Returns **Object** - the DOM element with the `dataset-tdid` tracker

### (2) retrieve

Retrieve the DOM element from the components cache

**Parameters**

-   `id` **String** the `throw-down` element ID

Returns **Object** - the cached `throw-down` component

### (3) update

A morphdom/yoyo update helper that transports the `throw-down` ID from the input element to the other

**Parameters**

-   `morphdom` **Function** either the `morphdom` or `yo.update` methods

Returns **Function** - the update method `update(el, newEl, opts)`

## Component mutation

When you mutate your DOM element/component make sure you transport the `node.dataset.tdid` to the newly morphed component. Checkout the `./update.js` for more information.

## Experimental

Note, this package is highly experimental, it uses the MutationObserver, we don't know how fast this module will be in production. More tests are needed. Please report all issues to the github repository.

## Building Your Own Life Cycle

If you would like more React like life cycle hooks, you can do so by modifying the `update` and `connect` modules. Hooks you will need to provide are the added, mutated and removed hooks for the DOM observer.

## Garbage Collection

Once the component is unloaded, the `removed` hook is fired, and the component is removed from the local `components` cache.

## ID Exchange

Presently, if a mutation occurs where the `dataset-tdid` is morphed (i.e identical component replacement), this mutation is flagged as a component `unload`. This may be changed in the future.

## Compatibility/MutationObserver

`throw-down` uses the window.MutationObserver to track DOM mutation

Support for MutationObserver can be tracked here:
http://caniuse.com/#search=MutationObserver

Global `86.4%` browser support

-- throw-down has support for `IE9+`, IE9- can be accomplished by removing the `forEach` statements in `./index.js`

## Modules that work well with throw-down

`throw-down` helps compliment other module systems such as:

 - yoyo - a tiny library for building modular UI components (uses bel/morphdom)
 - bel - creates DOM elements from template strings
 - morphdom - efficiently morphs DOM elements (without a virtual DOM)
 - hyperx - tagged template string virtual DOM builder

## "throw-down"?

The name `throw-down` was inspired by the great work done by @maxogden on yoyo, `throw-down` is the name of a yoyo trick.

## Similar Packages

<a href="https://github.com/shama/on-load">on-load</a> | by Kyle Robinson Young @shama
<a href="https://github.com/chromakode/diablo">diablo</a> | by Max Goodman @chromakode

## Story

https://github.com/maxogden/yo-yo/issues/15

## License

```
The MIT License (MIT)

Copyright (c) 2016 Nick Dodson

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
