---
layout: page
title:  "React and Redux"
date:   2018-05-14 03:48:28 -0600
categories: SPA
---

React is a Javascript library, maintained by Facebook, for building user interfaces. React will efficiently update and render just the right components when your data changes.

React's modular approach to programming allows developers to build encapsulated components that manage their own state. This means react code is extremely portable.

React allows you to pass data via props, allowing developers to reuse code where possible.

A simple React component:
```
class HelloMessage extends React.Component {
  render() {
    return (
      <div>
        Hello {this.props.name}
      </div>
    );
  }
}

ReactDOM.render(
  <HelloMessage name="Taylor" />,
  mountNode
);
```

Many components can be passed into a single component which can be useful when creating simple applications but you will soon run into something called "state-management-hell." This is when multiple components with multiple states get mingled together to a point where you as the developer must sort through during debugging. The problem is having to find a particular needle in a haystack full of needles.

It's all about data flow. As a react developer, you have probably run state-management-hell. For example, when a top-level component has some data to be passed down to its 6th child, data must travel through multiple intermediate components that do not really have any use for the data. Think of it as a family tree where you cannot directly ask your grandmother for her secret macaroni recipe. Rather, you must ask your parents, who in turn will ask their parents, who in turn will pass it down to you. What if you wanted your great-great-great grandmother's recipe? With multiple intermediary family members, you can quickly imagine the problem.

**Redux to the rescue**
Consider using redux if you have a complicated component structure.

We already know about components: react components collect and retrieve data via props. There are subtle differences between components and containers that you will run into as react developers. Simply put, the component-container pattern is useful when segregating code; components are responsible for display data while containers retrieve the displayed data.

**What is Redux?**
Redux is a JavaScript library for managing application state.

Before we begin, I would recommend you to read about the Three Principles of Redux [here](https://redux.js.org/introduction/three-principles).

**Store**
All stateful data live inside the Store. The store is given to the Provider which in turn allows your data to be accessible from your app.

**Reducer**
The store is passed the root reducer. Reducers are responsible for setting default values in the store as well as taking care of actions. Reducers specify how the application's state changes in response to actions sent to the store. Reducers are pure functions that take the previous state and an actions, whereby returning the next state. Keep in mind that the store is never changed; rather, a new state is returned with the original values in tact as well as the new values.

```
(previousState, action) => newState
```

*Pure functions are functions where the return value is determined by its input values; given the same input, will always return the same output.*
Pure function exanple:
```
function priceAfterTax(productPrice) {
 return (productPrice * 0.20) + productPrice;
}
```

This function is impure because the function depends on an external variable. What happens if the tax-rate changes? IMPURE!!
```
var tax = 20;
function calculateTax(productPrice) {
 return (productPrice * (tax/100)) + productPrice;
}
```

**Actions**
Actions are payloads of information in the form of a plain JavaScript object that send data from your application to your store. You send actions to the store using *store.dispatch()*.
```
{
  type: ACTION_NAME,
  text: 'Build my first Redux app'
}
```
With an action in mind, you can initialize action creators using a function that simply returns an action with an optional payload.
```
function actionCreator(payload) {
  return {
    type: ACTION_NAME,
    payload: payload
  }
}
```
To actually initiate an action to a store, use redux's dispatch() function.
```
dispatch(actionCreator('I am the payload'))
```
You can also create a bound action creator that automatically dispatches using an arrow function. Const is a ES6 feature.
```
const boundAddTodo = text => dispatch(addTodo(text))
```
Reducers receive the action from an action which in turn returns a new state.


**Connect()**
Most of the hard work is now done. With the creation of the components, store, actions, and reducers, React and Redux can then be "connected" with the connect() function.

React components are connected to Redux's state management protocol using the connect() function. The connect() function can take two arguments.
```
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(componentName)
```

*mapStateToProps*
mapStateToProps will make all desired state props available for the connected react component. This function will subscribe to the Redux store and any updates will update props automatically. mapStateToProps needs to return an object, where the key is the new prop name to be used in the React app and the value is the name of the reducer function.

*mapDispatchToProps*
There are two things mapDispatchToProps does: firstly, it sets up props that hold our action creators; secondly, it binds the action creators that you already specified to a dispatchable action in a given component.
```
const mapStateToProps = (storeState, ownPropsPersonallyPassed) => ({
  propName: storeState
})
​
const mapDispatchToProps = (dispatch, ownPropsPersonallyPassed) => ({
  functionPropName: () => dispatch(yourSpecifiedActions)
})
​
export default connect(
  mapStateToProps,
  mapDispatchToProps
)(componentName)
```

Et voila, your react app is now connected to redux. This is a simple example of what Redux can do for your React app. Any changes made in the store will automatically update the props in your component and future debugging efforts will be fruitful.

Redux might be overkill for simple React apps. Redux actions and reducers can create some extra boilerplate so it's good to have some criteria on when to use Redux.
I would use Redux when:
- Application state needs to be mapped to multiple container components.
- Data must be accessed from anywhere.
- Too many props are being passed through multiple intermediary components.
- State management using setState is bloating the component.
- When you want the webpage to look exactly the same every time the user refreshes the page despite the state of / modification made to the page.


Learning Path
https://redux.js.org/introduction
https://github.com/reduxjs/react-redux/blob/master/docs/api.md
https://medium.com/mofed/reduxs-mysterious-connect-function-526efe1122e4
https://redux.js.org/basics/example-todo-list
