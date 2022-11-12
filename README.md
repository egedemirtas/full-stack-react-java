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

### Adding Lifecycle Methods to a Class

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

### Using State Correctly

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

### The Data Flows Down

- Neither parent nor child components can know if a certain component is stateful or stateless
- State is often called local or encapsulated. It is not accessible to any component other than the one that owns and sets it.
- A component may choose to pass its state down as props to its child components

```javascript
function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}

class Clock extends React.Component {
    //...
    //...
    //...
    render() {
        return (
        <div>
            <h1>Hello, world!</h1>
            <FormattedDate date={this.state.date} />
        </div>
        );
    }
}
```

- This is commonly called a “top-down” or “unidirectional” data flow. Any state is always owned by some specific component, and any data or UI derived from that state can only affect components “below” them in the tree.
- To show that all components are truly isolated, we can create an App component that renders three `<Clock>`:

```javascript
function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  );
}
```

- Each Clock sets up its own timer and updates independently.
- You can use stateless components inside stateful components, and vice versa.

## Handling Events

- Have to pass function with JSX as event handler (need to pass string in HTML):

 ```javascript
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

- Have to call `e.preventDefault();` to prevent default behaviour. (return false in HTML)

- You don't need to call addEventListener to add listeners to elements.

- If you are using class components, event handler should a method in that class.

### Binding

```javascript
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}
```

- In JavaScript, class methods are not bound by default. If you forget to bind `this.handleClick` and pass it to `onClick`, this will be undefined when the function is actually called. Generally, if you refer to a method without () after it, such as `onClick={this.handleClick}`, you should bind that method.

- There are two ways you can get around this.

1. You can use public class fields syntax to correctly bind callbacks:

    ```javascript
    class LoggingButton extends React.Component {
      // This syntax ensures `this` is bound within handleClick.
      handleClick = () => {
        console.log('this is:', this);
      };
      render() {
        return (
          <button onClick={this.handleClick}>
            Click me
          </button>
        );
      }
    }
    ```

2. If you aren’t using class fields syntax, you can use an arrow function in the callback:

  ```javascript
  class LoggingButton extends React.Component {
    handleClick() {
      console.log('this is:', this);
    }

    render() {
      // This syntax ensures `this` is bound within handleClick
      return (
        <button onClick={() => this.handleClick()}>
          Click me
        </button>
      );
    }
  }
  ```

- Performance problem: The problem with this syntax is that a different callback is created each time the LoggingButton renders. In most cases, this is fine. However, if this callback is passed as a prop to lower components, those components might do an extra re-rendering.

### Passing Arguments to Event Handlers

- Either of the following would work:

```javascript
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

- In both cases, the e argument (representing the React event) will be passed as a second argument after the ID. With an arrow function, we have to pass it explicitly, but with bind any further arguments are automatically forwarded.

## Lists and Keys

### Lists

```javascript
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) => //need to add: key={number.toString()} to li element
    <li>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<NumberList numbers={numbers} />);
```

- When you run this code, you’ll be given a warning that a key should be provided for list items.

- A “key” is a special string attribute you need to include when creating lists of elements.

### Keys

- Keys help React identify which items have changed, are added, or are removed.

- The best way to pick a key is to use a string that uniquely identifies a list item among its siblings.

- Using indexes for keys is not recommened; the order of items may change. This can negatively impact performance and may cause issues with component state.

- Keys used within arrays should be unique among their siblings. However, they don’t need to be globally unique.

- Keys serve as a hint to React but they don’t get passed to your components. If you need the same value in your component, pass it explicitly as a prop with a different name.

## Forms
