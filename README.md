# React Summary

A summary based on online courses and official react documentation: (<https://reactjs.org/docs/getting-started.html>)

## JSX

- Not a string or html element
- Something like a template language used in django and comes with full power of JS.
- JSX produces React “elements”.
- Why JSX?:
    Rendering logic is coupled with UI logic. React has `components` which are loosely coupled units that contain both rendering & UI logic
- Any valid JS expression can be put in JSX's curly brackets
- JSX is an Expression Too
    After compilation, JSX expressions become regular JavaScript function calls and evaluate to JavaScript objects.
    This means that you can use JSX inside of if statements and for loops, assign it to variables, accept it as arguments, and return it from functions
- You should either use quotes (for string values) or curly braces (for expressions), but not both in the same attribute.
- JSX tags may contain children
- JSX Represents Objects
    Babel compiles JSX down to React.createElement() calls, which creates objects
    These objects are called “React elements”.
    React reads these objects and uses them to construct the DOM and keep it up to date.

## Rendering Elements

- Unlike browser DOM elements, React elements are plain objects, and are cheap to create. React DOM takes care of updating the DOM to match the React elements.
- Elements are what components are “made of”

```html
    <div id="root"></div>
```

- We call this a “root” DOM node because everything inside it will be managed by React DOM.
  - To render a React element, first pass the DOM element to ReactDOM.createRoot(), then pass the React element to root.render():

    ```javascript

    const root = ReactDOM.createRoot(document.getElementById('root'));
    const element = <h1>Hello, world</h1>;
    root.render(element);
    ```

- React elements are immutable. Once you create an element, you can’t change its children or attributes.
- React Only Updates What’s Necessary: React DOM compares the element and its children to the previous one, and only applies the DOM updates necessary to bring the DOM to the desired state.

## Components

### Function Components

```function Welcome(props) {return <h1>Hello, {props.name}</h1>;}```
