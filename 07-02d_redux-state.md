# Redux State

Let's start from here:

`index.js`

```JSX
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App/App';
import registerServiceWorker from './registerServiceWorker';
import { createStore, combineReducers } from 'redux';
import { Provider } from 'react-redux';

const firstReducer = (state, action) => {
    if (action.type === 'BUTTON_ONE') {
        console.log('Button 1 was clicked!');
    }
    return {};
};

const secondReducer = (state, action) => {
    if (action.type === 'BUTTON_TWO') {
        console.log('Button 2 was clicked!');
    }
    return {};
};

const elementListReducer = (state, action) => {
    if (action.type === 'ADD_ELEMENT') {
        console.log(`The element was ${action.payload}`);
    }
    return state;
};

// The store only accepts one reducer
const storeInstance = createStore(
    combineReducers(
        {
            firstReducer,
            secondReducer,
            elementListReducer,
        }
    )
);

ReactDOM.render(<Provider store={storeInstance}><App /></Provider>, document.getElementById('root'));
registerServiceWorker();
```

`App.js`

```JSX
import React, { Component } from 'react';
import { connect } from 'react-redux';

class App extends Component {
  constructor(props) {
    super(props);

    this.state = {
      newElement: '',
    }

    this.handleElementChange = this.handleElementChange.bind(this);
  }

  handleElementChange(event) {
    this.setState({
      newElement: event.target.value,
    });
  }

  render() {
    return (
      <div>
        {/* Dispatching an action */}
        <button onClick={() => this.props.dispatch({ type: 'BUTTON_ONE' })}>Button One</button>
        <button onClick={() => this.props.dispatch({ type: 'BUTTON_TWO' })}>Button Two</button>
        <input onChange={this.handleElementChange} />
        <button onClick={() => this.props.dispatch({ type: 'ADD_ELEMENT', payload: this.state.newElement })}>Add Element</button>
      </div>
    );
  }
}

export default connect()(App); // connect is a function... that returns a function! Currying!!
```



```JSX
const elementListReducer = (state, action) => {
    if(action.type === 'ADD_ELEMENT') {
        console.log(`The element was ${action.payload}`);
    }
    return {};
};
```

So at this point, we know that we need to have `state` in the reducer, but what is it, and what does it do?

In order to take a look at the redux state, let's log it out and see what the value is!

```JSX
const firstReducer = (state, action) => {
    if (action.type === 'BUTTON_ONE') {
        console.log('state', state);
        console.log('Button 1 was clicked!');
    }
    return {};
};

const secondReducer = (state, action) => {
    if (action.type === 'BUTTON_TWO') {
        console.log('state', state);
        console.log('Button 2 was clicked!');
    }
    return {};
};

const elementListReducer = (state, action) => {
    if(action.type === 'ADD_ELEMENT') {
        console.log('state', state);
        console.log(`The element was ${action.payload}`);
    }
    return {};
};
```

What if we change what is being returned?

```JSX
const firstReducer = (state, action) => {
    if (action.type === 'BUTTON_ONE') {
        console.log('state', state);
        console.log('Button 1 was clicked!');
    }
    return 1;
};
```

`state` starts out as whatever it ended as last time!

What if we return `state` itself?

```JSX
const firstReducer = (state, action) => {
    if (action.type === 'BUTTON_ONE') {
        console.log('state', state);
        console.log('Button 1 was clicked!');
    }
    return state;
};
```

Woah, big error! State starts as undefined, and we can't return undefined from a reducer, so let's use ES6 syntax to set a default value for our state if it is undefined:

```JSX
const firstReducer = (state = 0, action) => {
    if (action.type === 'BUTTON_ONE') {
        console.log('state', state);
        console.log('Button 1 was clicked!');
    }
    return state;
};
```

Ok... but we're just logging the same thing every time. What if we wanted to grow the number every time the button was clicked?

```JSX
const firstReducer = (state = 0, action) => {
    if (action.type === 'BUTTON_ONE') {
        console.log('state', state);
        console.log('Button 1 was clicked!');
        return state + 1;
    }
    return state;
};
```

Great! We can see our log our state, and we can change our state. What if we want to put our state on the DOM?

In order to put the redux state on the DOM, we want to put in onto `this.props` in our `App.js`. This can be done quite nicely by mapping the redux state to props with the connect function.

```JSX
const mapReduxStateToProps = (reduxState) => ({
  reduxState
});

export default connect(mapReduxStateToProps)(App);
```

and then we can include it in our `render` method by adding this

```JSX
<pre>{JSON.stringify(this.props.reduxState)}</pre>
```

So what is showing up on the DOM? `reduxState` is an object that has a property for each reducer.

```JSON
{"firstReducer":0,"secondReducer":{},"elementListReducer":{}}
```

Each reducer is specific to one of the properties on `reduxState`. What if we change the others?

```JSX
const firstReducer = (state = 0, action) => {
    if(action.type === 'BUTTON_ONE') {
        console.log('Button 1 was clicked!');
        return state + 1;
    }
    return state;
};

const secondReducer = (state = 100, action) => {
    if(action.type === 'BUTTON_TWO') {
        console.log(`Button 2 was clicked!`);
        return state - 1;
    }
    return state;
};

const elementListReducer = (state = [], action) => {
    switch (action.type) {
        case 'ADD_ELEMENT':
            return [ ...state, action.payload ];
        default:
            return state;
    }
}
```

Exercises

1. Make the `elementListReducer` just `elementList`. This is more common when using Redux.
1. Make the `reduxState` just `state`. Even though it's less descriptive and may be more confusing, this is more common when using Redux. Just remember there is a difference between the two.
1. Create a `CLEAR_ELEMENT_LIST` action that is dispatched on a button click that empties the `elementList`
1. Instead of a string, make each element an object with an atomic number. Instead of adding a second `onChange` handler, use currying to handle all input text changes.
1. Convert the element inputs and button to a form
1. Display elements in a list on the DOM
1. (Hard Mode) Order this list of elements by their atomic number (smallest to largest), research `.sort()` to do this.
1. (Stretch Mode) Our reducers are taking up a lot of space in our `index.js`. Inside of the `src` folder, create a `reducers` folder. Move each reducer to its own file, and then import them into `index.js`.
