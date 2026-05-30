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

# Terminology
## Single Page Application
Web app that loads and updates content dynamically without refreshing entire page.
![2](https://media.geeksforgeeks.org/wp-content/uploads/20240416144122/2.webp)
 ### Benefits of SPA
 - ***Faster User Experience:*** SPAs load once, and only fetch the necessary data, reducing page reloads and providing a smoother, more responsive experience.
 - ***Caching for Offline Access:*** SPAs can implement caching strategies, allowing users to access certain parts of the application even when offline.
 - ***Improved Performance:*** By minimizing server requests and only updating the required components, SPAs significantly reduce the load on servers, resulting in faster load times and better overall performance.
 - ***Reduced Bandwidth Usage:*** Since SPAs only fetch the data needed for specific interactions, they minimize the amount of data transferred between the client and server, reducing bandwidth usage and improving efficiency.
 - ***Enhanced Responsiveness:*** It enable dynamic content updates without requiring full page reloads.
 - ***Seamless User Navigation:*** SPAs use client-side routing, enabling seamless navigation between sections of the application without the need for full page reloads.
 - ***Cross-Platform Compatibility:*** SPAs are inherently compatible with various devices and platforms, promoting a consistent user experience across desktops, tablets, and mobile devices.
 -  ***Scalability:*** SPAs support the scalability of web applications by efficiently managing client-server interactions.
 ### Disadvantages of SPAs
 - ***Slower Initial Load:*** Can be slower initially, affecting users with slower internet.
 - ***SEO Challenges****: SEO can be tricky due to heavy reliance on JavaScript.
 + ***Limited Browser Support:*** Advanced features may not work well on older browsers.
- ***Security Risks:*** Vulnerable to security issues like Cross-Site Scripting (XSS).
- ***Client-Side Resource Intensity:*** Places a heavy load on the client side, impacting older devices.
- ***Dependency on JavaScript:*** Essential functionality may break if users disable JavaScript.
- ***Browser History Management:*** Handling navigation dynamically poses challenges with browser history.
- ***Complex Development:*** Developing SPAs is more complex, requiring a learning curve.

# Key Characteristics
>[!note]
> React is a library **NOT** a framework.
> Next.js and Remix are examples of frameworks built on top of React.
- **Declarative**: You describe the desired state of your UI based on data, and React handles updating the actual DOM efficiently.
- **Component-based**: Build reusable and modular UI elements (components) that manage their own state and logic.
	 - **Maintainable:** Changes within a component are isolated, reducing the risk of unintended side effects.
	- **Easier to test:** Components can be tested independently, ensuring their functionality and reliability.
	- **Modular and Reusable**
	- **Encapsulation and Isolation**:  Each component manages its own state and logic. Ensures loose coupling between data and logic.
- [[Rendering in React#Virtual DOM|Virtual DOM]]: React uses a lightweight in-memory representation of the actual DOM, allowing it to perform updates selectively and efficiently.
	- When data changes, React first updates the virtual DOM, then compares it to the previous version. This process, known as diffing, allows React to identify the minimal set of changes required in the actual DOM. By updating only the necessary elements, React minimizes expensive DOM manipulations, resulting in significant performance improvements.
- [[JSX]]: While not mandatory, JSX provides a syntax extension that allows you to write HTML-like structures within your JavaScript code, making UI development more intuitive.
-  **Learn once, write anywhere**: With React Native, you can use your React knowledge to build native mobile applications for iOS and Android. This "learn once, write anywhere" approach allows you to:
	- **Share code between platforms:** Reuse components and logic across web and mobile, reducing development time and effort.
	- **Leverage existing skills:** Apply your React expertise to mobile development without needing to learn entirely new technologies.
	- **Target multiple platforms with a single codebase:** Streamline development and maintenance by managing a single codebase for multiple platforms.
# React Node Vs React Element Vs React Component
## React Node
+ Basic unit, can be React Element, string, number, boolean or null.
+ Anything that can be rendered is a Node.
```javascript
const stringNode = 'Hello, world!';
const numberNode = 123;
const booleanNode = true;
const nullNode = null;
const elementNode = <div>Hello, world!</div>;
```
## React Element
+ Immutable object representing the element to be rendered.
+ It includes the type (such as a string for HTML tags or a React component), props, and children. React elements are created using JSX syntax or `React.createElement`
```javascript
const element = <div className="greeting">Hello, world!</div>;

// Using React.createElement
const element = React.createElement(
  'div',
  { className: 'greeting' },
  'Hello, world!',
);
```
## React Component
+ Reusable piece of UI that can accept props and returns React Elements describing the UI. 
+ Must return a single block of JSX.
### **Function Component**
Functions that take props as an argument and return a React element.
```javascript
export function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

export const Welcome = (props) => {
	return <h1>Hello, {props.name}</h1>
}
```
### **Class Component**
These are ES6 classes that extend `React.Component` and must have a `render` method returning a React element.
```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

```

