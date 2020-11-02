---
title: "Modern-ish Javascript"
date: 2020-04-05T10:16:22+01:00
draft: false
tags: [javascript, destructuring, arrow functions]
---

This post includes a few notes on ECMA language features that I like as well as some info on memory leaking.  I have no doubt that it will read disjointed; I started this eons ago and only now have I decided to publish it.

## ES2015 (ES6)

### Class

I find Javascript messy at the best of times.  When things are messy, personally I find it difficult to see the forest through the trees, and by this I mean, do I cover all the *-cases (use/edge/corner)?  Or worse, can I see the existing bugs or bug breaders?!  Then there's the lack of readability.  I've spoken to many devs about classes and a major prefer arrow functions (more modern).  They might be right as I too did default to arrow functions up until I drank the cool-aid on TypeScript (once to TS they're no going back)!

Coming from an OOP background, I naturally gravitate towards constructs like classes.

```js
class Admin extends User 
{
    constructor (name) {
        super(name)
        this.initialize()
    }
    initialize = () => {}
}

```

### Destructuring

```js
const getProfile = () => {
  return {firstname: "garrard", lastname: "kitchen", married: true, children: 2}
}

const {firstname, lastname, ...family} = getProfile()

console.log(`firstname: ${firstname}`)
console.log(`lastname: ${lastname}`)
console.log(family)
```

This would result in:

![](../img/2020-04-06-11-40-48.png)



### Arrow function

Arrow functions are a great addition to the ES spec!  Their scope is purely inside of it's closure and is not affected by the `this` context which may hoisted functions fall victum of.

```js
initialize = () => {}
```

## ES2016 (ES7) Language Features

### The Decorator

Awesome addition to the EMCA family!

I've used to with create affect with Typescript and with at least one NestJS solution.

This is a contrived example on how you can use a basic decorator on a class function:

_You must have configured your solution to use babel_


{{< tabs "uniqueid" >}}
{{< tab "example" >}}


```js
class Content {  
    @link('nodejs', "<a href='https://nodejs.org/en/'>Node.js</a>")
    html() {
        return `This server language is called nodejs!`
    }
}

function link(_find, _replace) {
    return function(target, key, descriptor) {
      var old = descriptor.value()      
      descriptor.value = function hello() {
        var n = old.replace(_find, _replace)        
        return n;
      };
    }
}

const m = new Content()
console.log(m.html());
```

output:
```
[nodemon] restarting due to changes...
[nodemon] starting `babel-node index.js`
This server language is called <a href='https://nodejs.org/en/'>Node.js</a>!
[nodemon] clean exit - waiting for changes before restart
```

{{< /tab >}}
{{< tab "babel" >}}

package.json:
```json
...
  "scripts": {    
    "start": "nodemon --exec babel-node index.js"
  },
...
  "devDependencies": {
    "@babel/core": "^7.12.3",
    "@babel/node": "^7.12.1",
    "nodemon": "^2.0.6"
  },
  "dependencies": {
    "@babel/plugin-proposal-decorators": "^7.12.1"
  }
...
```

.babelrc:
```json
{
  "plugins": [
    ["@babel/plugin-proposal-decorators", {
      "legacy": true
    }]
  ]
}
```

{{< /tab >}}
{{< /tabs >}}



## ES2018 (ES9)

### Spread

I was reminded of something useful this morning from a youtube video I was watching.  JS passes objects (non-primitives) by reference, ergo, memory pointers, so it is possible to effect an object outside of it's closure.  So, imagine you return an array of objects (e.g. from a service to a controller).  It is possible, to effect this array of objects from within the controller.  One way I have found to avoid this is by using the spread syntax: 

```js
private readonly list: string[]
getList() {
   return list
}
```

you can do this: 

```js
getList() {
    return [...list]
}
```

Obvs, the 👆 is using an array but you can do this same with an object too {...list}

## Memory

### Functions arguments passed by value; always

_See also [spread](#spread) 👆 to for advance on how to avoid memory leakage._

Further to the above, JS always passes by value (not reference) ALL augments to a function.  This means that, if you pass in an argument (primitive or object) into a function, the closure is honoured and therefore any changes made to this value inside the closure is not reflected outside, example:

```js
let v: string = "A"
getValue(v){
  v = v + "B"
}
let result = getValue(v)
console.log(result) // output: AB 
console.log(v) // output: A
```

