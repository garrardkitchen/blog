---
title: "Modern Javascript"
date: 2020-04-05T10:16:22+01:00
draft: true
tags: [javascript, destructuring, arrow functions]
---

## Destructuring

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

## Arrow function

Arrow functions are a great addition to the ES spec!  Their scope is purely inside of it's closure and is not affected by the `this` context which may hoisted functions fall victum of.

```js
initialize = () => {}
```
![](../img/2020-04-06-09-56-21.png)

## Class

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
