### File setup

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(<Component/>, document.getElementById('root'));

export default Component;
```

### Components

#### Functional
```javascript
const Component = () => {
  return(JSX)
}
```

#### Class
```javascript

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
```