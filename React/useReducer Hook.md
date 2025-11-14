#react #redux
# Prerequisite
[[State]]
# Overview
+ Alternative way of managing state (use instead of useState hook)
# Syntax
```jsx
const [state, dispatch] = useReducer(reducer, initialState, init)
```

`reducer` : [[Component#What is a pure function?|Pure function]] that takes two arguments `state` : current state and `action`: object that describes how to update state

```jsx
function reducer(state, action) {
	return state + action;
}
...
function increment() {
	dispatch(1); // currrent state increments by 1
}
```

`initialState` : Default state of a state variable.

`init`: (Optional) Function that returns initial state of a state variable. If specified, initial state is set to value returned by `init` function else set to value specified by `initialState`.

## Dispatch Function
+ Accepts an object of the type of action we want to execute, and delivers it to the reducer function. Triggers state updates.
+ We can perform multiple different types of operations on a piece of state in a single place.

```jsx
...
function reducer(state, action) {
	switch(action.type) {
		case "increment": return state + action.payload;
		case "decrement": return state - action.payload;
		case "setCount": return action.payload;
	}
}
const [state, dispatch] = useReducer(reducer, 0);

function increment() {
	dispatch({type: "increment", payload: 1});	
}

function decrement() {
	dispatch({type: "decrement", payload: 1});	
}

function setCount(e) {
	dispatch({type: "setCount", payload: Number(e.target.value)});	
}
...
```

+ We can manage multiple __related__ state variables with a single state variable.
	Assume now we want to increment/decrement count by a dynamic _step_ value. Instead of creating a separate state variable for _step_ , we can manage both _count_ and _step_ with a single variable.

```jsx
...
const initialState = {count: 0, step: 1};

function reducer(state, action) {
	switch (action.type) {
      case "inc":
        return { ...state, count: state.count + state.step };
      case "dec":
        return { ...state, count: state.count - state.step };
      case "setCount":
        return { ...state, count: action.payload };
      case "setStep":
        return { ...state, step: action.payload };
	  case "reset": return initialState; 
      default:
        throw new Error("Unknown action");
}

const [state, dispatch] = useReducer(reducer, initialState);

function increment() {
	dispatch({type: "increment"});	
}

function decrement() {
	dispatch({type: "decrement"});
}

function setCount(e) {
	dispatch({type: "setCount", payload: Number(e.target.value)});	
}

function setStep(e) {
	dispatch({type: "setStep", payload: Number(e.target.value)});	
}

function reset() {
	dispatch({type: "reset"});
}
...
```