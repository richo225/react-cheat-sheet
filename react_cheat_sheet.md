## File setup

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(<Component/>, document.getElementById('root'));

export default Component;
```

## Semantic UI
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.4.1/semantic.min.css" crossorigin="anonymous" />
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

`~$ yarn add redux-thunk`

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

## React router

`~$ yarn add react-router-dom` 

- The `<BrowserRouter />` blocks the server from redirecting a whole new HTML page and dumping any JS that's in memory.
- It instead changes the URL and rerenders the react components declared in `<Route />`

```javascript
import { BrowserRouter, Route } from 'react-router-dom';

class App extends React.Component {
  render () {
    return(
      <div>
        <BrowserRouter>
          <Header/>
          <Route path="/" exact component={StreamList} />
          <Route path="/streams/new" component={StreamCreate} />
          <Route path="/streams/show" component={StreamShow} />
          <Route path="/streams/edit" component={StreamEdit} />
          <Route path="/streams/delete" component={StreamDelete} />
        </BrowserRouter>
      </div>
    )
  }
}
```

## Authorization

GAPI is Google’s client library for browser-side JavaScript

`<script src="https://apis.google.com/js/api.js"></script>`

```javascript
import React from 'react';

class GoogleAuth extends React.Component {
  state = { isSignedIn: null };

  componentDidMount(){
    window.gapi.load('client:auth2', () => {
      window.gapi.client.init({
        clientId: '789689060467-5fcom3230maasmtp40uf7e3jju9m25qh.apps.googleusercontent.com',
        scope: 'email'
      }).then(() => {
        this.auth = window.gapi.auth2.getAuthInstance();

        this.auth.isSignedIn.listen(() => {
          this.setState({ isSignedIn: this.auth.isSignedIn.get() })
        })
      });
    });
  }

  render () {
    return(
      <div>
        {this.state.isSignedIn ? 'Signed In' : 'Signed Out'}
      </div>
    )
  }
}

export default GoogleAuth;
```

## Redux form

`~$ yarn add redux-form`

- Redux form handles everything on the redux side for your form fields. It has it's own reducer and handles mapStateToProps.
- You need to hook up the reducer with the `form` key:

```javascript
import { combineReducers } from 'redux';
import { reducer as formReducer } from 'redux-form';

export default combineReducers({
  form: formReducer
});
```

```javascript
import React from 'react';
import { Field, reduxForm } from 'redux-form';

class StreamCreate extends React.Component {
  renderInput = ({input, label, meta}) => {
    return(
      <div className="field">
        <label>{label}</label>
        <input {...input} />
        {this.renderError(meta)}
      </div>
    )
  }

  renderError({ error, touched}) {
    if(error && touched) {
      return(
        <div className="ui error message">
          <div className="header">
            {error}
          </div>
        </div>
      )
    }
  }

  onSubmit(formValues) {
    console.log(formValues)
    // Do whatever you want with the values. Probably POST to the server
  }

  render () {
    return(
      <form className="ui form error" onSubmit={this.props.handleSubmit(this.onSubmit)}>
        <Field name="title" label="Title"component={this.renderInput}/>
        <Field name="description" label="Description" component={this.renderInput}/>
        <button className="ui button">Submit</button>
      </form>
    )
  }
}

const validate = (formValues) => {
  const errors = {};

  if (!formValues.title){
    errors.title = "Title is required";
  }

  if (!formValues.description){
    errors.description = "Description is required";
  }

  return errors;
}

export default reduxForm({
  form: 'createStream',
  validate: validate
})(StreamCreate);
```

## Portals

- Portals are used for displaying modals to a user
- So far the `index.js` file has been rendering the app as a child to the `root` element
- Using portals, you can render a modal to a different element => taking two arguments => the modal JSX and the element id

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import history from '../history'

const Modal = (props) => {
  return (
    ReactDOM.createPortal(
      <div className="ui dimmer modals visible active" onClick={()=> history.push('/')}>
        <div className="ui standard modal visible active" onClick={(e)=> e.stopPropagation()}>
          <div className="header">Delete Stream</div>
          <div className="content">
            Are you sure you want to delete this stream?
          </div>
          <div className="actions">
            <button className="ui button primary">Delete</button>
            <button className="ui button">Cancel</button>
          </div>
        </div>
      </div>,
      document.getElementById('modal')
    )
  )
}

export default Modal;
```

![Screenshot 2019-11-14 at 22 25 16](https://user-images.githubusercontent.com/18379191/68901347-ad431980-072d-11ea-867a-f63c8f4a58e0.png)

## Context

You can pass data from a parent to a deeply nested child component via a context object. This acts as a data pipe and you can send and receive the data each via 2 ways:

#### Sending data

1. Setting a default value

```javascript
// src/contexts/MyContext.js

import React from 'react';

export default React.createContext(default_value);
```

2. Using a Provider

```javascript
import React from 'react';
import MyContext from '../contexts/MyContext';

class App extends React.Component {
  state = { selectedLanguage: 'english' };

  render () {
    return(
      <div className="ui container">
        <MyContext.Provider value={this.state.selectedLanguage}>
          <UserCreate />
        </MyContext.Provider>
      </div>
    )
  }
}

export default App;
```

#### Consuming data

1. Using `this.context`

You must first always set the class method of `contextType` to point to your context object

```javascript
static contextType = MyContext;
```

```javascript
import React from 'react';
import MyContext from '../contexts/MyContext';

class Component extends React.Component {
  static contextType = MyContext;

  render () {
    return(
      <div className="ui field">
        <label>{this.context}</label>
        <input />
      </div>
    )
  }
}

export default Component;
```

2. Using a Consumer

```javascript
import React from 'react';
import MyContext from '../contexts/MyContext';

class Component extends React.Component {
  render () {
    return(
      <div className="ui field">
        <label>
          <LanguageContext.Consumer>
            {(value) => // do something with value }
          </LanguageContext.Consumer>
        </label>
        <input />
      </div>
    )
  }
}

export default Component;
```