#react 
+ Stands for Javascript XML
+ Allows writing Javascript code within HTML
+ Under the hood, it is transformed into JS calls.
+ ***NOT*** valid JS by itself.
>[note]
>By default, React DOM escapes any values embedded in JSX before rendering them. This ensures that you can never inject anything that's not explicitly written in your application.


Ex: This means that we can write React without using JSX
```javascript
const element = <h1>Hello, world!</h1>;
//becomes
const element = React.createElement('h1', null, 'Hello, world!');
```
# Embedding Expressions
You can embed JavaScript expressions inside JSX using curly braces `{}`. For example:
```javascript
const name = 'John';
const element = <h1>Hello, {name}!</h1>;
```
# Attributes in JSX
Can use quotes to specify string literals as attributes and curly braces to embed JavaScript expressions. For example:
```javascript
const element = <img src={user.avatarUrl} alt="User Avatar" />;
```