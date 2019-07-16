### File setup

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(<Component/>, document.getElementById('root'));

export default Component;
```

### Semantic UI
```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/semantic-ui/2.4.1/semantic.min.css" integrity="sha256-9mbkOfVho3ZPXfM7W8sV2SndrGDuh7wuyLjtsWeTI1Q=" crossorigin="anonymous" />
```

### Components

#### Functional
```javascript
const Component = () => {
  return(JSX);
}
```

#### Class
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

### State
State is initialised in the constructor
```javascript
class App extends React.Component {
  constructor(props){
    super(props);

    this.state = { key: value }
  }
}
```
Changing state will re-render the component
```javascript
this.setState({ key: value })
```

### Children

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
