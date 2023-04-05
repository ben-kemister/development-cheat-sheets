---
title: React
tags:
- javascript
---



<!--more-->

## Create a React App

Use the comand `npx create-react-app <app_name>` to bootstrap a basic React app.

Example: `npx create-react-app my-app`

## Components

There are two basic way to create custom components in React:
1. Create a `class` which extends `React.Component` - this is an older style
2. Use a funcitonal style of declaring the component - a newer style since React version 14

## Funcitonal Components

### Accessing Props

Props are still avilable in functional components, you just need to declare a variable for them.

Example:
```js
const Child = (props) => {
    return (
        <div />
    )
}
```

You can then access the props throught the component as follows:

Example 1:
```js
const Child = (props) => {
    return (
        <div style={{backgroundColor: props.eyeColor}}/>
    )
}
```

Example 2:
```js
export const PrivateRoute = (props) => {

    const keycloak = userKeyCloak();

    return (
        <div>
            <h1>Use is authenticated? {keycloak && keycloak.authenticated}
        </div>
    )
}
```

## Hooks

### useEffect hook

The Effect Hook lets you perform side effects in React functional components. Examples of side effects include fetching data, setting up subsciptions and manually changing the DOM.

By using the useEffect hook you tell React that the component needs to do something after render.

The useEffect hook also takes a list of dependencies, which you can use to trigger the useEffect hook if their state changes.
