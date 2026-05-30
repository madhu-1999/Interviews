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
+ Stands for Javascript XML
+ Allows writing HTML within JS.
+ Under the hood, it is transformed into JS calls.
+ ***NOT*** valid JS by itself. Compiled into JS with a tool like Babel.

>[!note]
>By default, React DOM escapes any values embedded in JSX before rendering them. This ensures that you can never inject anything that's not explicitly written in your application.

Ex: This means that we can write React without using JSX.
```jsx
const element = <h1>Hello, world!</h1>;
//becomes
const element = React.createElement('h1', null, 'Hello, world!');
```
But it is easier to read and write JSX.
# Embedding Expressions
You can embed JavaScript expressions inside JSX using curly braces `{}`. For example:
```jsx
const name = 'John';
const element = <h1>Hello, {name}!</h1>;
```
# Attributes in JSX
+ use quotes to specify string literals as attributes and curly braces to embed JavaScript expressions. For example:
```jsx
const element = <img src={user.avatarUrl} alt="User Avatar" />;
```
## style attribute
+ double {} used since we are passing in a object `{color: "red"}`
+ inline style properties must follow camelCase convention.
```jsx
const element = <button style={{color:"red"}}></button>

const buttoncolor = "blue"
const element = <button style={{color:buttoncolor}}></button>
```
# Objects in JSX
```jsx
const person = {
  name: 'Gregorio Y. Zara',
  theme: {
    backgroundColor: 'black',
    color: 'pink'
  }
};

export default function TodoList() {
	return (
		<div>
			<div style={person.theme}>
			<h1>{person.name}'s Todos</h1>		
		</div>
	);
}
  
```
# HTML vs JSX

| HTML                                                          | JSX                                                                                              |
| ------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| Multiple elements can be returned                             | Must return a single element that wraps all nested elements                                      |
| HTML elements have attributes.                                | JSX elements have props.                                                                         |
| Can use any naming convention for events, attributes and ids. | <mark>All HTML and CSS attributes and events must be in camelCase. Ex: onclick => onClick</mark> |
| The *class* attribute can be used on any HTML element         | Cannot use *class* attribute as *class* is a reserved keyword in JS. Instead use *className*     |
| All elements have an opening and closing tag except a few     | All elements can be written with self-closing tags.                                              |
# Conditional Rendering
+ Expression in ternary operator must return a boolean, else its value will also be rendered when expression evaluates to false.
```jsx
return (
  <li className="item">
    {isPacked ? name + ' ✅' : name}
  </li>
);
```
+ When you want to conditionally render something if expression is true else render **nothing** ,  we can use the && operator short circuiting.
+ If expression is true, value on the right is rendered, else entire expression evaluates to false, and React renders nothing for `false`, `undefined` or `null` values.
```jsx
return (
  <li className="item">
    {name} {isPacked && '✅'}
  </li>
);```
# Rendering Lists
## Rendering array data
+ Each array item is given a `key` — a string or a number that uniquely identifies it among other items in that array. 
+ Key **must not** change.***DO NOT*** generate them while rendering
+ Keys tell React which array item each component corresponds to. This becomes important if your array items can move (e.g. due to sorting), get inserted, or get deleted.
+ Note that your components won’t receive `key` as a prop. It’s only used as a hint by React itself
```jsx
const people = [
  'Creola Katherine Johnson: mathematician',
  'Mario José Molina-Pasquel Henríquez: chemist',
  'Mohammad Abdus Salam: physicist',
  'Percy Lavon Julian: chemist',
  'Subrahmanyan Chandrasekhar: astrophysicist'
];

export default function List() {
  const listItems = people.map(person =>
    <li key={person.id}>{person}</li>
  );
  return <ul>{listItems}</ul>;
}
```
## Filtering arrays
```jsx
export default function List() {
  const chemists = people.filter(person =>
    person.profession === 'chemist'
  );
  const listItems = chemists.map(person =>
    <li key={person.id}>
      <img
        src={getImageUrl(person)}
        alt={person.name}
      />
    </li>
  );
  return <ul>{listItems}</ul>;
}

```