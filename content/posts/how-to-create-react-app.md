---
title: "How to Create React App"
date: 2020-04-17T09:13:19+01:00
draft: true
---

## Requirements

- nodejs

To create your first react app, run:

```script
npm i npx create-react-app
npx create-react-app <name-of-app>
cd <name-of-app>
```

The above, installs npx. This is used to execute a `command` from node_modules/.bin folder.  By default, it will look in the `$PATH` or local project for the `command`.

npx create-react-app create a project that includes the default framework for your SPA.  Finally, you cd into the name of your project.

```
npm i @material-ui/core --save-dev
npm install @material-ui/icons --save-dev
```

Added this to before the </head> element:
```html
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700&display=swap" />
    <link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons" />
```

## Pushing state change down into child components

Let's say we have a parent (App) and 2 child functon components:

```js
export default function App(prop) {
    const [items, setItems] = useState(prop.rebuildList)
    const onClick = () => {
        //     
    }
    return (
        <App>
            <Button onClick={onClick}></Button>
            <Child1 list={items}/>
            <Child2 list={items}/>
        </App>
    }
}
```

If we want state (`list`) to propagate down into the 2 Child function components we need to do something like this:

```js
const onClick = () => {
    items.push({id:1})
    setRebuilds([...items])
}
```

This would see those nested components automatically updated (re-rendered) when referencing that prop (`list`).

However, if you did this:
```js
const onClick = () => {
    items.push({id:1})
    setRebuilds(items)
}
```

you would see the props in the child function components change but any components referring these props would not re-render.

Is this odd behaviour?  Good question.  I believe that as we're only passing an object literal reference, React doesn't see it as a changed value.  It does however, if we pass in a `value` (new state) and not a Object reference.  This `value` can also be a computed function (`param => param + 1`)

## What are Hooks?

Hooks is a new way of hooking into React functionality without having to write a class.  Hooks were introduced in React 16.8.  These Hooks can only be used from React Functions.  There's a new function called `useState`.  You use javascript destructuring to make available a new state variable and a `setter`. You can use the State Hook multiple times in a React Function For example:

```js
const [item, setItems] = useState([])
const [current, setCurrent] = useState(0)
```

## References

- [create-a-new-react-app](https://reactjs.org/docs/create-a-new-react-app.html)
- [npx](https://www.npmjs.com/package/npx)
- [create-react-app](https://www.npmjs.com/package/create-react-app)
- [hooks](https://reactjs.org/docs/hooks-overview.html)



