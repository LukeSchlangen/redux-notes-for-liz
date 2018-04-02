# Redux Actions

```JSX
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App/App';
import registerServiceWorker from './registerServiceWorker';
import { createStore, combineReducers } from 'redux';
import { Provider } from 'react-redux';

const firstReducer = (state = {}, action) => {
    console.log(`Heyo!!! I'm a reducer y'all!!!`);
    return {};
};

const secondReducer = (state = {}, action) => {
    console.log(`I'm another reducer y'all!!!`);
    return {};
};

// The store only accepts one reducer
const storeInstance = createStore(
    combineReducers(
        {
            firstReducer,
            secondReducer,
        }
    )
);

ReactDOM.render(<Provider store={storeInstance}><App /></Provider>, document.getElementById('root'));
registerServiceWorker();
```

Actions are objects. They need a property of `type` that tells our reducer which action it is, but besides that, you can do whatever you want.

These are all good:

```JSX
{ type: 'BUTTON_ONE' }
```

```JSX
{ type: 'BUTTON_TWO' }
```

```JSX
{ type: 'BUTTON_THREE' }
```

But at some point, we'll probably want to start passing more information to our reducer. From our star example, you can imagine 


```JSX
{ type: 'ADD_STAR' }
```

but then we need to send the details about the star in order to add it.

```JSX
{ 
    type: 'ADD_STAR',
    starDetails: {
        name: 'elnath',
        diameter: 50,
    },
}
```

And that's a perfectly valid action too! Often times, you will see the information be referred to as the payload, so it might look like this, but it doesn't have to. It has to have a `type` to be an action, but that's it:

```JSX
{ 
    type: 'ADD_STAR',
    payload: {
        name: 'elnath',
        diameter: 50,
    },
}
```

In our case, we're going to use elements on the periodic table as our example:

```JSX
<button onClick={() => this.props.dispatch({ type: 'ADD_ELEMENT', payload: 'hydrogen'})}>Add Element</button>
```

Let's create a reducer to pick up this dispatched action

```JSX
const elementListReducer = (state = {}, action) => {
    if(action.type === 'ADD_ELEMENT') {
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
```

Let's do something similar for our other reducers

```JSX
const firstReducer = () => {
    console.log('Button 1 was clicked!');
    return {};
};

const secondReducer = () => {
    console.log('Button 2 was clicked!');
    return {};
};
```

- Exercise: `Button 1 was clicked!` and `Button 2 was clicked!` should only log if that was true.
- Exercise: Add an input for the new element and pass that in as the payload (e.g. `{ type: 'ADD_ELEMENT', payload: this.state.newElement}`). This should use local state for handling `onChange` for now.
- Exercise: add a reducer that logs `state`. What is the `state`? Where did it come from?
