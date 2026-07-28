#react 
# Rules
1. Only call hooks at the __top level__ .i.e. not inside conditionals, loops, nested function or early return.
	1. Ensures hooks are always called in same order.
2. Only call hooks inside a [](Component.md#Function%20Component|function%20component) or custom hook.
# Why do hooks rely on call order?
+ The [](Rendering%20in%20React.md#**Fiber**|fiber%20tree) is created only once and each fiber (element) contains info such as list of props, list of hooks, a list of work etc..
+ If call order changes due to some condition being false or some code not executing, the list of hooks in fiber tree breaks. (Cannot be updated, since it is rendered only once).
![](theory-slides-v1.1.pdf#page=227&selection=4,13,4,20|theory-slides-v1.1,%20page%20227)
# Commonly used hooks
1. [useState](State.md)
2. [useEffect Hook](useEffect%20Hook.md)
3. [useRef Hook](useRef%20Hook.md)
4. [useReducer Hook](useReducer%20Hook.md)
# Custom Hook
