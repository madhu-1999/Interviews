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
# Prerequisite
[[Component]]
[[Event handlers]]

# Need for state
+ Components need to [[Component#Keeping Components pure|pure]] i.e. we can only mutate local variables.
+ However, each time a component is rendered, it is a fresh copy, so the values of all local variables are reset.
+ Even if we mutate the value of a local variable, React doesn't realize it needs to render component again with new data.
+ The `useState` hook provides both these things:
	+ A **state variable** to retain data between renders.
	+ A **state setter function** to update variable and trigger React to render component again.
```
const [index, setIndex] = useState(0);
```
`index` is the state variable, `setIndex` the setter function and `useState(0)` means we are passing initial value (0) for state variable.
	The `[ ]` syntax is array destructuring and it lets you read values from an array. The array returned by `useState` always has exactly two items.
# Setter Function
+ If setting value of state to a particular literal value
```jsx
function handleClick() {
	const i = 10;
	...
    setIndex(i);
  }

```

+ If setting state value, but new value is dependent on current value of state. 
+ [[#State as a snapshot]] explains why this is needed.
+ React processes state updates after event handlers have finished running
```jsx
function handleClick() {
    setIndex((index) => index + 1);
  }
```
# Things to know
+ State is local to a component instance i.e. **if you render the same component twice, each copy will have completely isolated state!** Changing one of them will not affect the other.![[Screenshot 2025-08-30 at 7.17.50 PM.png]]
+  If you want to change the way a component looks, or the data it displays, update its state. This usually happens in an event handler function.
+  For data that should not trigger component re-renders, **don’t** use state. Use a regular variable instead.
+ When React removes a component, it destroys its state.
# State as a snapshot
+ When React calls your component, it gives you a snapshot of the state for that particular render. Your component returns a snapshot of the UI with a fresh set of props and event handlers in its JSX, all calculated **using the state values from that render!**![[Screenshot 2025-09-01 at 1.52.25 PM.png]]
+ What happens when you click the “+3” button ?
``` jsx
export default function Counter() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+3</button>
    </>
  )
}

```
`number` only increments once per click!

**Setting state only changes it for the _next_ render.** During the first render, `number` was `0`. This is why, in _that render’s_ `onClick` handler, the value of `number` is still `0` even after `setNumber(number + 1)` was called.

Even though you called `setNumber(number + 1)` three times, in _this render’s_ event handler `number` is always `0`, so you set the state to `1` three times. This is why, after your event handler finishes, React re-renders the component with `number` equal to `1` rather than `3`.
+ **A state variable’s value never changes within a render,** even if its event handler’s code is asynchronous
# Updating objects in state
>[!tldr]
>**Treat any JavaScript object that you put into state as read-only.**
+ State variables are declared `const` when initializing like
```jsx
const [state, setState] = useState({x:0, y:0});
```
but, if state variable is of `object` type, it is possible to mutate it. This is because, the object reference cannot be changed due to `const` , but its contents can.
+ However, directly mutating object will not work as desired, since React cannot tell if value has changed or not, unless setter function is used to change state value.
+ Thus, when we want to update some value in an object, we should replace the entire object.
Instead of:
```jsx
onPointerMove={e => {
  position.x = e.clientX;
  position.y = e.clientY;
}}
```
do:
```jsx
onPointerMove={e => {
  setPosition({
    x: e.clientX,
    y: e.clientY
  });
}}
```
+ For changing just a few fields in object, and copying rest of the fields from current object, use spread operator (...)
```jsx
setPerson({
  ...person, // Copy the old fields
  firstName: e.target.value // But override this one
});
```
# Updating arrays with state
+ Treat arrays as **read-only**. Reason same as [[#Updating objects in state]]
## Append
```jsx
setArtists( // Replace the state
  [ // with a new array
    ...artists, // that contains all the old items
    { id: nextId++, name: name } // and one new item at the end
  ]
);
```
## Prepend
```jsx
setArtists([
  { id: nextId++, name: name },
  ...artists // Put old items at the end
]);
```
## Insert at position
```jsx
function handleClick() {
    const insertAt = 1; // Could be any index
    const nextArtists = [
      // Items before the insertion point:
      ...artists.slice(0, insertAt),
      // New item:
      { id: nextId++, name: name },
      // Items after the insertion point:
      ...artists.slice(insertAt)
    ];
    setArtists(nextArtists);
  }```
## Delete
```jsx
setArtists(
    artists.filter(a =>
        a.id !== artist.id
    ));
```
## Updating objects in array
```jsx
setMyList(myList.map(artwork => {
  if (artwork.id === artworkId) {
    // Create a *new* object with changes
    return { ...artwork, seen: nextSeen };
  } else {
    // No changes
    return artwork;
  }
}));
```
# Choosing State Structure
## Group related state
If you always update two or more state variables at the same time, consider merging them into a single state variable.
Ex: moving cursor updates x, y coordinates of a red dot.
Instead of :
```jsx
const [x, setX] = useState(0);
const [y, setY] = useState(0);
```
do this instead:
```jsx
const [position, setPosition] = useState({ x: 0, y: 0 });
```
## Avoid redundant state
If some information can be derived from component props or existing state variables, do not make a separate state variable.
Ex: Instead of creating new state variable for `fullName`:
```jsx
const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');
const [fullName, setFullName] = useState('');
```
we can do this:
```jsx
const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');
const fullName = firstName + ' ' + lastName;
```
## Avoid duplication in state
When the same data is duplicated between multiple state variables, or within nested objects, it is difficult to keep them in sync. Reduce duplication when you can.
Ex: Here `selectedItem` stores the entire object to denote selected item.
```jsx
items = [{ id: 0, title: 'pretzels'}, ...]
selectedItem = {id: 0, title: 'pretzels'}
...
<p>You picked {selectedItem.title}.</p>
```
but if the item is changed in `items` array while it is the selected item, text in `<p>` tag will not change since `selectedItem` has not been updated. Duplication of state is causing problems. 
Instead, we store just the `id` of selected item and search the array for the item. This works because item of id will not change.
```jsx
items = [{ id: 0, title: 'pretzels'}, ...]
selectedId = 0
...
const selectedItem = items.find(item =>
    item.id === selectedId
  );
...
<p>You picked {selectedItem.title}.</p>  
```
## Avoid deeply nested state
Deeply hierarchical state is not very convenient to update. When possible, prefer to structure state in a flat way.
 Ex: [[State#Updating objects in state|Updating state]] gets very complex
 ```jsx
 const travelPlan = {
	 id: 0,
	 title: 'Earth',
	 childPlaces: [
		 {
			 id: 1,
			 title: 'Africa',
			 childPlaces: [
				 ...
			 ]
		 },
		 {
			 ...
		 }
	 ],
 }
 ```
 Instead make the hierarchy flat by instead storing `childIds`.
 ```jsx
 const travelPlan = {
	 0: {
		 id: 0,
		 title: 'Earth',
		 childIds: [2, 10]
	},
	1: {
		id: 1,
		title: 'Africa',
		childIds:[4, 32, 21]
	}, 
	...
 }
 ```
## Avoid contradictions in state
 When the state is structured in a way that several pieces of state may contradict and “disagree” with each other, you leave room for mistakes. Try to avoid this.
```jsx
  ...
  const [isSending, setIsSending] = useState(false);
  const [isSent, setIsSent] = useState(false);
  ...
```
if you forget to call `setIsSent` and `setIsSending` together, you may end up in a situation where both `isSending` and `isSent` are `true` at the same time.
**Since `isSending` and `isSent` should never be `true` at the same time, it is better to replace them with one `status` state variable that may take one of _three_ valid states:** `'typing'` (initial), `'sending'`, and `'sent'`.
# Sharing state between components
+ Sometimes, you want the state of two components to always change together. 
+ To do it, remove state from both of them, move it to their closest common parent, and then pass it down to them via props. This is known as _lifting state up_.

Consider an app with below React Element Tree. 
What should be done if we want only one panel to be **active** at any given time. i.e when we click on a panel, previously active panel (if any) will be deactivated and current panel will become active ?
![The same diagram as the previous, with the isActive of the first child Panel component highlighted indicating a click with the isActive value set to true. The second Panel component still contains value false.](https://react.dev/_next/image?url=%2Fimages%2Fdocs%2Fdiagrams%2Fsharing_state_child_clicked.png&w=1080&q=75)
This problem can be solved by **lifting state up**. We move `isActive` state out of `Panel` and into `Accordion`.  
1. `isActive` is passed down to `Panel` as props.
```jsx
function Panel({ title, children, isActive })
```
2. Add state to **least common ancestor**, here it is `Accordion`.
```jsx
const [activeIndex, setActiveIndex] = useState(0);
```
We defined a different state variable `activeIndex` , since `Accordion` contains a list of `Panel` , it is easier to track currently active `Panel` using its index in the list.
3. Pass data into child props.
```jsx
<>
  <Panel
    isActive={activeIndex === 0}
    onShow={() => setActiveIndex(0)}
  >
    ...
  </Panel>
  <Panel
    isActive={activeIndex === 1}
    onShow={() => setActiveIndex(1)}
  >
    ...
  </Panel>
</>
```
