---
layout: post
title: Optional Chaining and Nullish Coalescing in Javascript
tags: programming webdev 
---

# Optional Chaining and Nullish Coalescing in Javascript

There are two new operators on the Javascript block: [Optional Chaining](https://github.com/tc39/proposal-optional-chaining)
and [Nullish Coalescing](https://github.com/tc39/proposal-nullish-coalescing).
The addition of these to Javascript allow for safe deep object access (optional
chaining) and default value resolution (nullish coalescing). These are incredibly
exciting<sup>1</sup> and can be effectively used together to write safe and
ergonomic code for accessing objects, especially those coming from API calls or
other suspect sources.

## Optional Chaining

Optional chaining allows for easy access of deeply nested values within an object
tree without having to check for the existence of each key along each access.

Previous to optional chaining, your deep accesses had to look something like this:

```js
const obj = {
    deeply: {
        nested: {
            key: 42
        }
    }
}

// UNSAFE: if any key does not exist, then you get a TypeError exception
obj.deeply.nested.key // 42
obj.deeply.broken.key // TypeError: undefined has no properties

// SAFER, BUT UNWIELDY
if ('deeply' in obj) {
    if ('nested' in obj.deeply) {
        if ('key' in obj.deeply.nested) {
            obj.deeply.nested.key // 42
        }
    }
}

// SAFER, LESS UNWIELDY BUT STILL BAD
obj && obj.deeply && obj.deeply.nested && obj.deeply.nested.key // 42
```

Compare to the new optional chaining syntax (`?.`), which short-circuits if a key is
undefined without throwing an error.

```js
obj?.deeply?.nested?.key // 42 or undefined
```

You can also use this for optional methods:

```js
// Setup: make two classes, one with and one without a method
class BaseAnimal {
    // ... empty ...
}

class Dog extends BaseAnimal {
    bark() {
        console.log('woof!')
    }
}

const a1 = new Animal()
const a2 = new Dog()

// Run the method if available

a1?.bark() // no output
a2?.bark() // woof!
```

## Nullish Coalescing

Nullish coalescing is a natural complement to optional chaining. Nullish
coalescing makes it easy to set default values for variables via short-circuiting
while avoiding a common footgun of using the `||` operator. The `||` operator
operates on truthy or falsey values, which can be `0`, `null`, `false`, the empty
string, or `undefined` (to name a few). However, the value being short-circuited
can sometimes intentionally be a falsey value, which can cause unintuitive behavior.

As an example, watch what happens when we define a simple function that takes an
optional parameter and either prints the parameter or the string `default` if none
is provided:

```js
// implementation with the "||" operator
function default(option) {
    // get a default value
    option = option || 'default'
    console.log(option)
}

// some normal examples:
default()      // 'default'
default(42)    // 42
default('hi!') // 'hi!'

// things start to break here:
default(0)     // 'default'
default(false) // 'default'
default('')    // 'default'
```

Nullish coalescing (`??`) allows for slightly smarter overriding of a default
value by only considering `null` and `undefined`:

```js
// implementation with the "??" operator
function default(option) {
    // get a default value
    option = option ?? 'default'
    console.log(option)
}

// some normal examples:
default()      // 'default'
default(42)    // 42
default('hi!') // 'hi!'

// these work too!
default(0)     // 0
default(false) // false
default('')    // ''
```

This is much more intuitive, as intentionally falsey values cannot trip the
default value.

## Putting it all together

These two operators complement each other to create an easy way to access unknown
objects from sources such as APIs. Here's a simple (contrived) example:

```js
const print_from_api = async () => {
    const response = await fetch('https://api.example.com')
    let body = await response.json()

    // at this point, we don't know what body contains,
    // so let's use the new syntax
    console.log( body?.message ?? 'No message on body.' )
}
```

## Browser Support

Currently, both are ECMA Stage 4 Drafts, meaning that all changes are finished
and will be included in the next ECMA standard revision. However, these features
are already available for use in most browsers. Optional chaining can be used in
`78.59%` of global browsers, and nullish coalescing can be used in `78.64%`.
Notably, the Firefox Browser and Samsung Browser for Android do not support these
operators, so if you need to target these platforms you may need to use a
transpiler like Babel to get access to these.


## Further Reading

  - [Optional Chaining on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining)
  - [Optional Chaining on Caniuse](https://caniuse.com/#search=optional%20chaining)
  - [Optional Chaining TC39 Proposal](https://github.com/tc39/proposal-optional-chaining)
  - [Nullish Coalescing on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing_operator)
  - [Nullish Coalescing on Caniuse](https://caniuse.com/#search=nullish%20coalescing)
  - [Nullish Coalescing TC39 Proposal](https://github.com/tc39/proposal-nullish-coalescing)

