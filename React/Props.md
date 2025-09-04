#react 
# Overview
+ Data that a [[Component]] receives from its parents.
+ Immutable i.e read only
+ Used by parent component to configure (how they work) and customize (how they look) child component.
```jsx
// Customize example
// App.jsx
const color = "red"
export default function App() {
	return <Button color={color}/>
}

// Button.jsx
export const Button = ({color}) => {
	return <button style={{color:color}}></button>
}
```
+ Anything can be passed as props: single values, arrays, objects, functions and even components.
# Destructuring props
```jsx
export const Greet = (props) => {
	// Destructuring
	const { active, activeStatus } = props;
	return (
		<div>
			<div className="XYZ">
				<h3> {active} </h3>
			</div>
			<div className="PQR">
				<h1>{activeStatus}</h1>
			</div>
		</div>
	);
};
```
or destructure in-place
```jsx

export const Greet = ({ active, activeStatus }) => {
	return (
		<div>
			<div className="XYZ">
				<h3> {active} </h3>
			</div>
			<div className="PQR">
				<h1>{activeStatus}</h1>
			</div>
		</div>
	);
};
```
## Default value for props
+ Default value used, if no value is specified or you pass `size={undefined}`.
+ If `size={null}` or `size={0}` is passed, default value is ***NOT*** used.
```jsx
function Avatar({ person, size = 100 }) {
  // ...
}```
## Children prop
+ When you nest content inside a JSX tag, the parent component will receive that content in a prop called `children`. For example, the `Card` component below will receive a `children` prop set to `<Avatar />` and render it in a wrapper div:
```jsx
function Card({ children }) {
  return (
    <div className="card">
      {children}
    </div>
  );
}

function Profile() {
	return (
		<Card>
			<Avatar />
		</Card>	
	);
}
```
+ Since `children` is a prop, anything can be passed: single values, functions, objects, components, arrays or objects.
![](https://react.dev/images/docs/illustrations/i_children-prop.png)
# Why are props immutable?
1. **Data Consistency:** Immutable props make it easier to track data flow between components and ensure consistency throughout the application.
2.  **Performance Optimization:** Since props are immutable, React can efficiently determine which parts of the UI need to be updated and optimize DOM rendering.
3. **Simpler Debugging:** Immutable props make it easier to identify and fix bugs in your code.
+ Components have to be **pure functions** in terms of props and [[State|state]]
+ If data has to be modified use state.
# How props change over time
```jsx
export default function Clock({ color, time }) {
  return (
    <h1 style={{ color: color }}>
      {time}
    </h1>
  );
}
```
+ The `time` prop changes every second, and the `color` prop changes when you select another color. 
+ Each time new props are passed in, the child component is re-rendered.
+ Note that, for the child component, props are still immutable. It is just that the parent component is passing in different values for props.