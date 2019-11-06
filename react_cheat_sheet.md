## File setup

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(<Component/>, document.getElementById('root'));

export default Component;
```

## Semantic UI
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.4.1/semantic.min.css" integrity="sha256-9mbkOfVho3ZPXfM7W8sV2SndrGDuh7wuyLjtsWeTI1Q=" crossorigin="anonymous" />
```

## Components

### Functional
Use just for rendering simple JSX
```javascript
const Component = () => {
  return(JSX);
}
```

### Class
Main way. Gives access to state and lifecycle methods
```javascript
class App extends React.Component {
  constructor(props){
    super(props);
  }

  render() {
    return(JSX);
  }
}
```

## Props

```javascript
<Component key="value" />

const Component = (props) => {
  return(
    {props.key}
  )
}
```

You can specify default prop attributes with `defaultProps`

```javascript
<Component />

const Component = (props) => {
  return()

  Component.defaultProps = {
    key: value
  }
}
```

## Children

```javascript
const App = () => {
  return(
    <Parent>
      // child content
      <div> Hello there</div>
    </Parent>
  )
}

# access via
{props.children}
```

## State

State can be initialised in the constructor

```javascript
class App extends React.Component {
  constructor(props){
    super(props);

    this.state = { key: value }
  }
}
```

Most of the time you don't need the constructor and can declare it within the class

```javascript
class App extends React.Component {
  state = { key: value }
}
```

Changing state will re-render the component

```javascript
this.setState({ key: value })
```

## Lifecycle methods

```javascript
constructor(){}             # initial setup
render(){}                  # render any JSX
componentDidMount(){}       # data loading
componentDidUpdate(){}      # when component updates itself eg.state change
componentDidUnMount(){}     # not used that often
```

## Event handlers

#### onChange - onClick

```javascript
class App extends React.Component {
  state = { text: '' }

  handleInputChange = (event) => {
    this.setState({ text: event.target.value })
  }

  render(){
    <input type="text" value={this.state.text} onChange={this.handleInputChange} />
  }
}
```

Don't always need to use a private method:

```javascript
class App extends React.Component {
  state = { text: '' }

  render(){
    <input type="text" value={this.state.text} onChange={e => this.setState({ text: e.target.value })} />
  }
}
```

#### onSubmit

You first need to stop the browser from submitting the form itself by using `preventDefault`

```javascript
class App extends React.Component {
  state = { text: '' }

  handleFormSubmit = (event) => {
    event.preventDefault;
    // do what you want with input eg. save to db
  }

  render(){
    <form onSubmit={this.handleFormSubmit} />
  }
}
```

### Form example

```javascript
class SearchBar extends React.Component {
  state = { term: '' }

  handleInputChange = (event) => {
    this.setState({ term: event.target.value });
  }

  handleFormSubmit = (event) => {
    event.preventDefault()
    // do something with (this.state.term)
  }

  render () {
    return(
      <form className="ui form" onSubmit={this.handleFormSubmit}>
        <input type="text"
          placeholder="Search..."
          value={this.state.term}
          onChange={this.handleInputChange} />
      </form>
    );
  };
}
export default SearchBar;
```

## Children to Parent callbacks

`Parent => Child` Pass props to child

`Child => Parent` Parent passes a callback method to the child. The child then calls this method.

```javascript
class Parent extends React.Component {
  callbackMethod = () => {
    // handle output from child
  }

  render(){
    return(
      <div>
        <Child methodName={this.callbackMethod}>
      </div>
    )
  }
}

class Child extends React.Component {
  render(){
    {this.props.methodName(
      // pass what you want to parent
    )}
  }
}
```

## Asynchronous requests

```javascript
makeRequest = () => {
  axios.get(url, {
    params: { .. },
    headers: { .. }
  }).then(response => {
    // do something with response
  });
}
```

### await

Simpler way is to use `async/await` for the method call

```javascript
makeRequest = async () => {
  const response = await axios
    .get(url, {
      params: { .. },
      headers: { .. }
    });
  // do something with response
}
```

### custom client

```javascript
// /api/clientName.js
import axios from 'axios'

export default axios.create({
  baseURL: ' ',
  headers: { }
});
```

Then import custom client and replace axios:

```javascript
import 'clientName' from '/api/clientName.js'

makeRequest = async () => {
  const response = await clientName
    .get(relative_url, {
      params: { .. }
    });
// do something with response
}
```

## Redux cycle

![Screenshot 2019-09-25 at 19 07 38](https://user-images.githubusercontent.com/18379191/65771684-47bcae80-e128-11e9-893e-eaa997f83ff2.png)

![Redux cycle(1)](https://user-images.githubusercontent.com/18379191/65777207-1ac1c900-e133-11e9-93fe-dba7501d8ee9.png)

## React-redux

`~$ yarn add redux react-redux`

![Screenshot 2019-10-01 at 16 39 12](https://user-images.githubusercontent.com/18379191/65978350-1531ef00-e463-11e9-834f-54e36f9acdee.png)

### Combining reducers

When first setting up the app, you can use mock values for reducers.

```javascript
import { combineReducers } from 'redux';

export default combineReducers({
  stub: () => 'stub'
})
```

`combineReducers()` takes a hash of reducers and returns a reducer. The state is updated afterwards eg. from below with something like `{ posts: [post1, post2, post3] }`. You must always give a default state param as a reducer can't handle undefined.

```javascript
import { combineReducers } from 'redux';

const postsReducer = (state = [], action) => {
  switch(action.type){
    case 'FETCH_POSTS':
      return action.payload;
    default:
      return state;
  }
};

export default combineReducers({
  posts: postsReducer
})
```

### Provider

The provider wraps the app and is passed a reference to the redux store

The store is created via `createStore()` after combining your reducers with `combineReducers()`

```javascript
import { Provider } from 'react-redux';
import { createStore } from 'redux';
import reducers from '../reducers';

const App = () => {
  return(
    <Provider store={createStore(reducers)} >
      <App />
    <Provider />
  );
};
```

### Connector

- Any component that needs to interact with the redux store is wrapped in the connect tag
- It communicates with the provider via the context system
- `mapStateToProps()` configures the connector with which pieces of state we want. This can be set to `null` when first configuring an app
- Action creators are hooked up by passing them into `connect()`

```javascript
import React from 'react'
import { connect } from 'react-redux';
import { actionCreator } from '../actions';

class ComponentName extends React.Component {
  render () {
    // this.props === { songs: state.songs }
    
    <button onClick={this.props.actionCreator(this.props.songs)}>
       Action
    </button>
  }
}

const mapStateToProps = (state) => {
  // takes the state from the store and returns it as props to the component
  return { songs: state.songs };
};

export default connect(
  mapStateToProps,
  { actionCreator: actionCreator }
)(ComponentName);
```

### Redux Thunk

- Asynchronous API calls etc. cannot be used in a normal action creator as they don't return the correct action object
- Instead some middleware called `redux-thunk` is used which sits in between an action and the dispatch function. It is used for asynchronous action creators.
- These asynchronous action creators can return functions instead of objects that are passed to redux-thunk. Redux-thunk then adds the `dispatch` and `getState` methods as arguments to this function. When the asyncronous process is later finished, the function can be called manually with these arguments.

```javascript
// Asynchronous action creator
import api from '../apis/api';

export const fetchData = () => {
  // the action creator returns a function that is sent to redux-thunk
  return async function (dispatch, getState) => {
    // async process
    const response = await api.get('/data');

    const action = {
      type: 'FETCH_DATA',
      payload: response
    };

    // this is manually run within redux-thunk
    dispatch(action)
  };
};
```

This can be refatored into:

```javascript
export const fetchData = () => async (dispatch) => {
  const response = await api.get('/data');

  dispatch({
    type: 'FETCH_DATA',
    payload: response
  });
};
```

The middleware is hooked up when you create the store using `applyMiddleware`

```javascript
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';

const store = createStore(reducers, applyMiddleware(thunk);

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

