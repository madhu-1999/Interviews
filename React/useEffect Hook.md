#react 
# Prerequisite
[[Component#Side effects|Side effects]]
[[Rendering in React]]
# Overview
+ Used to handle side effects in [[Component#Function Component|functional components]], that are **caused by rendering not an event**
	+ Fetching data from an API.
	- Setting up event listeners or subscriptions.
	- Cleaning up resources when a component unmounts.
```jsx
useEffect(() => {
    // Code to run on each render
    return () => {
        // Cleanup function (optional)
    };
}, [dependencies]);
```
- ***Effect function***: This is where your side effect code runs.
- ***Cleanup function***: This optional return function cleans up side effects like subscriptions or timers when the component unmounts.
- ***Dependencies array***: React re-runs the effect if any of the values in this array changes.
- Ex: Every time `count` changes, effect runs.
```jsx
useEffect(() => {
        document.title = `You clicked ${count} times`;
    }, [count]);  
```

# How does it work?

- ***Initial Render Happens:*** React renders the component and updates the DOM.
- ***useEffect Executes After Render:*** It runs after the paint, not during render.
- ***Dependencies Are Checked:*** If there is no dependency array, the effect runs after every render; if the array is empty ([]), it runs once on mount; if dependencies are provided, it runs only when those values change.
- ***Cleanup Function Runs:*** Before the effect re-runs or the component unmounts, the cleanup function (returned from useEffect) is executed.
- ***Effect Re-runs:*** If dependencies changed, the effect runs again—after cleanup.

> [!critical]+
> +  Use `useEffect` sparingly.
> + Since it runs after browser paint, if state is set in it, another render will be required.
> + If used a lot, will affect performance.

