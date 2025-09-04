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
![[Screenshot 2025-08-30 at 7.24.20 PM.png]]
+ This process of requesting and serving UI has three steps:

1. **Triggering** a render (delivering the guest’s order to the kitchen)
2. **Rendering** the component (preparing the order in the kitchen)
3. **Committing** to the DOM (placing the order on the table)
![[Screenshot 2025-08-30 at 7.37.58 PM.png]]
# Triggering a render
## Initial Render
+ The initial render is triggered by calling `createRoot` with the target DOM node, and then calling its render method.
```jsx
import Image from './Image.js';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'))
root.render(<Image />);

```
## State update
+ Component re-renders if state of the component or one of its ancestors is updated.
# Render
+ In React, rendering is ***NOT*** updating the DOM or displaying elements on the screen. Rendering only happens **internally** inside React, it **does not** produce visual changes.
+ **“Rendering” is React calling your components.**
	+ **On initial render,** React will call the root component.
	- **For subsequent renders,** React will call the function component whose state update triggered the render.
- It is a recursive process, so the component that triggers render, and all its children are "rendered".
+ Renders are not triggered immediately, but scheduled for when the JS engine has some “free time”. There is also batching of multiple `setState` calls in event handlers.
+ React figures out how to update the DOM using Virtual DOM.
## Virtual DOM
+ Lightweight, in-memory representation of the real DOM elements. (JS object).
+ For given JSX:
```jsx
import React, { useState } from 'react';

function App() {
 const [count, setCount] = useState(0);

 return (
   <div>
     <h1>Counter: {count}</h1>
     <button onClick={() => setCount(count + 1)}>Increment</button>
   </div>
 );
}

export default App;
```
the virtual DOM representation looks like:
```json
{
 "type": "div",
 "props": {},
 "children": [
   {
     "type": "h1",
     "props": {},
     "children": [
       {
         "type": "TEXT_ELEMENT",
         "props": {
           "nodeValue": "Counter: 0"
         }
       }
     ]
   },
   {
     "type": "button",
     "props": {
       "onClick": "setCount(count + 1)"
     },
     "children": [
       {
         "type": "TEXT_ELEMENT",
         "props": {
           "nodeValue": "Increment"
         }
       }
     ]
   }
 ]
}

```
on clicking `Increase` button, only `h1` element is changed:
```json
{
 "type": "h1",
 "props": {},
 "children": [
   {
     "type": "TEXT_ELEMENT",
     "props": {
       "nodeValue": "Counter: 1"
     }
   }
 ]
}
```
### **Need for Virtual Dom**
+ On updating DOM, the entire tree is rendered again, including parts that have not changed.
+ The DOM rendering process is slow and resource-intensive especially for frequent, complex updates.
+ The virtual DOM is instead used to track changes and these changes are then batched and applied to the real DOM in [[#Commit|commit]] phase, to minimize the full rendering of DOM tree.
### **How does virtual DOM track changes?**
+ Whenever state changes and a render is triggered, a new virtual DOM is created.
+ The previous version of virtual DOM and the new one are compared, in a process called **diffing**, to ascertain the changes made.
+ Then React figures out what DOM elements need to be inserted, deleted or updated in order to reflect these changes in a process called **reconciliation**
## Reconciliation
### **Fiber**
+ Current React reconciler is called **fiber**
+ During [[#Initial Render]], it creates a **fiber tree** based on the [[#Virtual DOM]].
+ It is a special internal with **one** "fiber" for each component instance and DOM element in the app.
+ Each fiber contains a components current state, props, hooks etc, along with a queue of work to do, like updating state, updating refs, performing DOM updates.
+ Unlike [[#Virtual DOM]], it is not recreated on every render. It is a mutable data structure that is mutated in each reconciliation step.
+ Instead of a parent-child relationship, each first child has a link to its parent and all other children have a link to their previous sibling. ([[Linked List]]).
+ Reconciling done asynchronously. i.e. work can be paused, reused, split into chunks and resumed as necessary.![[www.udemy.com_course_the-ultimate-react-course_learn_lecture_37350774.png]]
### **How does fiber tree help in reconciliation?
+ New [[#Virtual DOM]] is compared to previous virtual DOM through **diffing** to give the updated fiber tree which is internally called the work in progress tree.
+ During diffing, DOM mutations identified are placed in a list called list of effects which is used in [[#Commit]] phase to mutate the DOM.
![[Screenshot 2025-09-01 at 4.19.36 PM.png]]
## Diffing
Uses 2 rules:
+ Same component at same position in component tree, preserves state or conversely different components at same position reset state.
	Ex 1 Updating the `App` state does not reset the `Counter` because `Counter` stays in the same position.
![Diagram with two sections separated by an arrow transitioning between them. Each section contains a layout of components with a parent labeled 'App' containing a state bubble labeled isFancy. This component has one child labeled 'div', which leads to a prop bubble containing isFancy (highlighted in purple) passed down to the only child. The last child is labeled 'Counter' and contains a state bubble with label 'count' and value 3 in both diagrams. In the left section of the diagram, nothing is highlighted and the isFancy parent state value is false. In the right section of the diagram, the isFancy parent state value has changed to true and it is highlighted in yellow, and so is the props bubble below, which has also changed its isFancy value to true.](https://react.dev/_next/image?url=%2Fimages%2Fdocs%2Fdiagrams%2Fpreserving_state_same_component.png&w=1200&q=75)

+ Ex 2 When `Counter` changes to `p`, the `Counter` is deleted and the `p` is added
![Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'div' with a single child labeled 'Counter' containing a state bubble labeled 'count' with value 3. The middle section has the same 'div' parent, but the child component has now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'p', highlighted in yellow.](https://react.dev/_next/image?url=%2Fimages%2Fdocs%2Fdiagrams%2Fpreserving_state_diff_pt1.png&w=1920&q=75)
When switching back, the `p` is deleted and the `Counter` is added. 
**When you render a different component in the same position, it resets the state of its entire subtree**
![Diagram with three sections, with an arrow transitioning each section in between. The first section contains a React component labeled 'p'. The middle section has the same 'div' parent, but the child component has now been deleted, indicated by a yellow 'proof' image. The third section has the same 'div' parent again, now with a new child labeled 'Counter' containing a state bubble labeled 'count' with value 0, highlighted in yellow.](https://react.dev/_next/image?url=%2Fimages%2Fdocs%2Fdiagrams%2Fpreserving_state_diff_pt2.png&w=1920&q=75)
+ Elements with a stable (i.e. unchanging) key prop will stay same across renders.
	+ Special prop that we use to tell the diffing algorithm that an element is unique
	-  Allows React to distinguish between multiple instances of the same component type
	- When a key stays the same across renders for a component, the element will be kept in the DOM (even if the position in the tree changes).
	- When a key changes between renders  for a component, the element will be destroyed and a new one will be created (even if the position in the tree is the same as before)
Ex: changing key
```jsx
{isPlayerA ? (
  <Counter key="Taylor" person="Taylor" />
) : (
  <Counter key="Sarah" person="Sarah" />
)}
```
# Commit
+ List of effects generated in previous step is used to write to DOM.
+ The write process is **synchronous**, unlike the [[#Render]] step which is asynchronous.
	+ It is synchronous, so that DOM updates never partial, thus UI is consistent.
+ The browser will then notice that the DOM has been changed, and as a result, it will repaint the screen whenever it has some idle time.
+ Once completed, the work in progress tree becomes the current fiber tree for the next render.
+ [[#Render]] step is performed by the React library but commit is done by React DOM library.
	+ This is because React is platform independent, it can be used to create mobile apps, videos and documents ( [[React#Key Characteristics|Write anywhere]] ).
	+ React DOM is used to write updates to browser DOM, but other packages are used for commit phase in mobile apps and other use cases.
