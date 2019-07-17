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
