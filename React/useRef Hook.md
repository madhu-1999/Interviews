#react 
# Key characteristics
`ref.current` is **mutable** i.e. we can read it as well as write to it, unlike `useState` which cannot be written to.
	Every time a re render occurs, a new copy of the _state_ variable is created with updated value given to the _setState_ function. i.e. the value of a _state_ variable does not change within the same render.

```jsx
export default function Counter() {
  // Creating ref
  const ref = useRef(0);

  function handleClick() {
	// Writing to a ref
    ref.current = ref.current + 1;
    // Reading ref value
    alert('You clicked ' + ref.current + ' times!');
  }

  return (
    <button onClick={handleClick}>
      Click me!
    </button>
  );
}
```

We can modify value of `ref` even though it is declared `const` because `useRef` returns a plain JS Object `{js} {current : 0}` . The contents of an object can be modified, the reference to object cannot.

The value of `ref` variable is retained across renders, without triggering a render each time it changes.

>[!note]
>`useRef` should be used if its value is not required for rendering. If the value is required for rendering, `useState` should be used instead.

In this example, the button text, _You clicked xx times_ does not update on click, because changing the value of a `useRef` variable does not trigger a re-render. Internally, value of `ref.current` is updating, it is just not visible on UI.
```jsx
export default function Counter() {
  // Creating ref
  const ref = useRef(0);

  function handleClick() {
	// Writing to a ref
    ref.current = ref.current + 1;
  }

  return (
    <button onClick={handleClick}>
      You clicked {ref.current} times
    </button>
  );
}
```
# When to use 
Use when you want a component to _remember_ some information, but don't want to trigger a re-render every time it changes, unlike [[State|useState]].
## Selecting and manipulating DOM elements
In below example, we want the `{html} input` element to be in focus when page re renders.
Such kind of DOM manipulation can be done using `useRef` in a declarative way instead of directly manipulating DOM using query selectors. 

```jsx
export const Search = ({ query, onQuery }) => {
  const searchbar = useRef(null);

  useEffect(() => {
    searchbar.current.focus();
  }, []);

  return (
    <input
      className="search"
      type="text"
      placeholder="Search movies..."
      value={query}
      onChange={(e) => onQuery(e.target.value)}
      ref={searchbar}
    />
  );
};
```

Since `input` element is associated with `useRef` variable only after rendering is completed, a [[useEffect Hook|useEffect]] hook is used to focus the element as they run after rendering completes.
But, depending on the use case, can access and use refs in [[Event handlers|event handlers]] too.