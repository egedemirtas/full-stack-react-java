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

```javascript
function Welcome(props) {
    return <h1>Hello, {props.name}</h1>;
}
```

- This function is a valid React component because it accepts a single “props”(properties) object argument with data and returns a React element.
- We call such components “function components” because they are literally JavaScript functions.

### Class Components

- You can also use an ES6 class to define a component:

```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

#### Rendering Componenets

- elements can also represent user-defined components:

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const root = ReactDOM.createRoot(document.getElementById('root'));
const element = <Welcome name="Sara" />;
root.render(element);
```

1. We call `root.render()` with the `<Welcome name="Sara" />` element.
2. React calls the Welcome component with {name: 'Sara'} as the props.
3. Our Welcome component returns a `<h1>Hello, Sara</h1>` element as the result.
4. React DOM efficiently updates the DOM to match `<h1>Hello, Sara</h1>`

> Note: Always start component names with a capital letter.

## Props

- Props are Read-Only
- Whether you declare a component as a function or a class, it must never modify its own props.
- All React components must act like pure functions with respect to their props.

> Pure functions: they do not attempt to change their inputs, and always return the same result for the same inputs.

## Converting a Function to a Class

1. Create an ES6 class, with the same name, that extends `React.Component`
2. Add a single empty method to it called `render()`
3. Move the body of the function into the `render()` method.
4. Replace props with this.props in the `render()` body.
5. Delete the remaining empty function declaration.

```javascript
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}
```

```javascript
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

- Clock is now defined as a class rather than a function.
- The render method will be called each time an update happens, but as long as we render `<Clock />` into the same DOM node, only a single instance of the Clock class will be used.
- This lets us use additional features such as local state and lifecycle methods.

## Adding Local State to a Class

```javascript
class Clock extends React.Component {
  constructor(props) { // Add a class constructor that assigns the initial this.state:
    super(props); // we pass props to the base constructor: Class components should always call the base constructor with props
    this.state = {date: new Date()};
  }

  render() {// Replace this.props.date with this.state.date in the render() method
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2> 
      </div>
    );
  }
}
```

## Adding Lifecycle Methods to a Class

- it’s very important to free up resources taken by the components when they are destroyed.
- We want to set up a timer whenever the Clock is rendered to the DOM for the first time. This is called “mounting” in React.
- We also want to clear that timer whenever the DOM produced by the Clock is removed. This is called “unmounting” in React.

```javascript
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() { // runs after the component output has been rendered to the DOM. This is a good place to set up a timer
    // While this.props is set up by React itself and this.state has a special meaning, 
    // you are free to add additional fields to the class manually if you need to store something that doesn’t participate in the data flow
    this.timerID = setInterval( 
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() { // tear down the timer in the componentWillUnmount() lifecycle method
    clearInterval(this.timerID);
  }

  tick() { // Clock component will run every second.
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

1. When `<Clock />` is passed to `root.render()`, React calls the constructor of the Clock component. Since Clock needs to display the current time, it initializes this.state with an object including the current time. We will later update this state.
2. React then calls the Clock component’s `render()` method. This is how React learns what should be displayed on the screen. React then updates the DOM to match the Clock’s render output.
3. When the Clock output is inserted in the DOM, React calls the `componentDidMount()` lifecycle method. Inside it, the Clock component asks the browser to set up a timer to call the component’s `tick()` method once a second.
4. Every second the browser calls the `tick()` method. Inside it, the Clock component schedules a UI update by calling `setState()` with an object containing the current time.
5. Thanks to the `setState()` call, React knows the state has changed, and calls the `render()` method again to learn what should be on the screen.
6. This time, `this.state.date` in the `render()` method will be different, and so the render output will include the updated time. React updates the DOM accordingly.
7. If the Clock component is ever removed from the DOM, React calls the `componentWillUnmount()` lifecycle method so the timer is stopped.

## Using State Correctly

1. Do Not Modify State Directly
    - this will not re-render a component:

    ```javascript
    // Wrong
    this.state.comment = 'Hello';
    ```

    - Instead, use setState():

    ```javascript
    // Correct
    this.setState({comment: 'Hello'});
    ```

    - The only place where you can assign `this.state` is the constructor.

2. State Updates May Be Asynchronous
    - React may batch multiple `setState()` calls into a single update for performance.
    - Because `this.props` and `this.state` may be updated asynchronously, you should not rely on their values for calculating the next state.

    ```javascript
    // Wrong
    this.setState({
        counter: this.state.counter + this.props.increment,
    });
    ```

    - To fix it, use a second form of `setState()` that accepts a function rather than an object. That function will receive the previous state as the first argument, and the props at the time the update is applied as the second argument:

    ```javascript
    // Correct
    this.setState((state, props) => ({
        counter: state.counter + props.increment
    }));

    // or

    // Correct
    this.setState(function(state, props) {
        return {
            counter: state.counter + props.increment
        };
    });
    ```

3. State Updates are Merged
    - When you call `setState()`, React merges the object you provide into the current state.
    - For example, your state may contain several independent variables and you can update them independently with separate `setState()` calls
