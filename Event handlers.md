#react 
```table-of-contents
title: Index
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
include: 
exclude: 
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Overview
+ Functions that will be triggered in response to user interactions like clicking and hovering.
```jsx
export default function Button() {
  function handleClick() {
    alert('You clicked me!');
  }

  return (
    <button onClick={handleClick}>
      Click me
    </button>
  );
}
```
Note that we passed in only the function name `handleClick` instead of calling it like so `handleClick()`.  If we instead write `onClick={handleClick()}` , the `handleClick` function is called during the rendering process, instead of when an event happens.
+ We can use inline functions to handle events if they are short. 
Note that we are only defining the anonymous function using arrow syntax, **not** calling it.
```jsx
<button onClick={() => {
  alert('You clicked me!');
}}>```
+ All event handlers receive an event object as their only argument.
+ Unlike rendering functions, event handlers don't need to be [[Component#Keeping Components pure|pure]], so they can have side effects.
	+ But, in order to mutate some local variable, it needs to be stored such that re-rendering doesn't reset the value. This is done using [[State|state]] .
# Passing as props
+ Event handlers can be passed as [[Props|props]].
+ The need to pass event handlers as prop is explained later.
+ Naming convention: start with `on`, followed by a capital letter.
```jsx
function Button({ onSmash, children }) {
  return (
    <button onClick={onSmash}>
      {children}
    </button>
  );
}

export default function App() {
  return (
    <div>
      <Button onSmash={() => alert('Playing!')}>
        Play Movie
      </Button>
      <Button onSmash={() => alert('Uploading!')}>
        Upload Image
      </Button>
    </div>
  );
}
```
# SyntheticEvent
+ Every browser handles event handlers differently.
+ In React, we write event handlers like `onClick` instead on native HTML `onclick`. The `onClick` is a synthetic event.
+ The `SyntheticEvent` wrapper wraps around native DOM event and acts as an interface providing same methods and properties regardless of browser.
+ The wrapper ensures event handlers work consistently across all browsers.
# Event propagation
## Event flow in DOM
1. Capturing phase: Event propagates from top of the document to the element where event occurred.
2. Target phase: Event handler executes on the element on which event occurred can be accessed via `e.target` .
	> Conventionally, event object is denoted by e.
3. Bubbling phase: Event propagates from element where it occurred , up to the top of the document.
![React Event Propagation Diagram](https://blog.logrocket.com/wp-content/uploads/2023/02/event-capturing-react.png)
## Event Propagation in React
+ Follows event  flow in DOM.
+ In below snippet `Toolbar`  is the parent component has a onClick event. It has two children `Button` with their own onClick events.
+ What happens when a `Button` is clicked?
```jsx
export default function Toolbar() {
  return (
    <div className="Toolbar" onClick={() => {
      alert('You clicked on the toolbar!');
    }}>
      <button onClick={() => alert('Playing!')}>
        Play Movie
      </button>
      <button onClick={() => alert('Uploading!')}>
        Upload Image
      </button>
    </div>
  );
}

```
+ When a `Button` is clicked, its event handler executes and event propagates up to the parent `Toolbar` . Then `Toolbar` event handler also executes.
+ However, if `Toolbar` is clicked, only its event handler executes, since events "bubble" up.
>[!note]+
>All events propagate up in React except `onScroll`
## e.stopPropagation()
+ To stop this default behavior of events "bubbling" up , we can use `e.stopPropagation()`
+ useful when the parent component has an event handler that conflicts with the event handler of the child component
```jsx
function Button({ onClick, children }) {
  return (
    <button onClick={e => {
      e.stopPropagation();
      onClick();
    }}>
      {children}
    </button>
  );
}
```
# Event delegation in React 16 vs React 17
| Feature             | React 16 and earlier                                                                    | React 17 and later                                            |
|---------------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------|
| Event attachment    | Handlers attached to the document.                                                      | Handlers attached to the root DOM container.                  |
| Scope of events     | Events are global and delegated from the document.                                      | Events are scoped and delegated from the component's root.    |
| Integration         | Prone to conflicts when mixing with other JS frameworks that use document-level events. | Interoperates smoothly with other JS frameworks.              |
| Gradual upgrades    | Fragile and error-prone due to event conflicts.                                         | Enables safe, gradual upgrades for large applications.        |
| e.stopPropagation() | Might not prevent events from reaching non-React code outside the React tree.           | Reliably prevents events from bubbling beyond the React root. |
![A Diagram Showing How React v17 Attaches Events to the Roots Rather Than to the Document](https://blog.logrocket.com/wp-content/uploads/2023/02/react-tree-changes.png)
# Preventing default behavior
+ Some browser events have default behavior associated with them.
+ Ex: `<form>` submit event, reloads page by default.
+ Can call `e.preventDefault()` on event object to stop this behavior.
```jsx
export default function Signup() {
  return (
    <form onSubmit={e => {
      e.preventDefault();
      alert('Submitting!');
    }}>
      <input />
      <button>Send</button>
    </form>
  );
}
```