## Reducers

A reducer is a function that runs every time an action is dispatched. Right now, we can see that's true by clicking the button. Every time it's clicked our reducer logs something.

Eventually, reducers are going to move to their own files so that this isn't so cluttered, so let's move it to a `const` outside of our `createStore`.

```JSX
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App/App';
import registerServiceWorker from './registerServiceWorker';
import { createStore } from 'redux';
import { Provider } from 'react-redux';

// This is our first reducer
const firstReducer = () => {
    console.log(`Heyo!!! I'm a reducer y'all!!!`);
};

// This is creating the store
// The store is the big JavaScript Object that holds all of the information for our application
const storeInstance = createStore(
    firstReducer
);

ReactDOM.render(<Provider store={storeInstance}><App /></Provider>, document.getElementById('root'));
registerServiceWorker();
```

Let's create a second reducer and add it to our store. Hold up! If we try this, we get errors like crazy!

```JSX
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App/App';
import registerServiceWorker from './registerServiceWorker';
import { createStore, combineReducers } from 'redux';
import { Provider } from 'react-redux';

const firstReducer = () => {
    console.log(`Heyo!!! I'm a reducer y'all!!!`);
};

const secondReducer = () => {
    console.log(`I'm another reducer y'all!!!`);
};

// The store only accepts one reducer
const storeInstance = createStore(
    firstReducer,
    secondReducer
);

ReactDOM.render(<Provider store={storeInstance}><App /></Provider>, document.getElementById('root'));
registerServiceWorker();
```

First we get a weird, non-descriptive error:

```
TypeError: enhancer(...) is not a function
```

The real problem is that When you create a store, it can only take one reducer. We're trying to pass it two, and things break when we do this. But we want to reduce so many things! If only there was a way to combine our reducers into one! Redux gives us a way to do that called `combineReducers`!

```JSX
import { createStore, combineReducers } from 'redux';
```

And now we can write

```JSX
const firstReducer = () => {
    console.log(`Heyo!!! I'm a reducer y'all!!!`);
};

const secondReducer = () => {
    console.log(`I'm another reducer y'all!!!`);
};

// The store only accepts one reducer
const storeInstance = createStore(
    combineReducers({
        firstReducer,
        secondReducer
    }),
);
```

Now we get a new error! Yay?

```
Uncaught Error: Reducer "firstReducer" returned undefined during initialization.
```

Now that we're using `combineReducers`, it's checking to make sure that our reducers are actually valid reducers. Ours are not. Reducers need to return something. We should start doing is returning a default state. This'll make more sense in a second, but for now, know that we need to return something from our reducer. If state is null, let's default it to an empty object:

```JSX
const firstReducer = () => {
    console.log(`Heyo!!! I'm a reducer y'all!!!`);
    return {};
};

const secondReducer = () => {
    console.log(`I'm another reducer y'all!!!`);
    return {};
};
```

This is still a little silly. A button that just runs every console log from every reducer? Let's see what Redux provides to our reducers and change it to this:

```JSX
const firstReducer = (state, action) => {
    console.log(`Heyo!!! I'm a reducer y'all!!!`);
    return {};
};

const secondReducer = (state, action) => {
    console.log(`I'm another reducer y'all!!!`);
    return {};
};
```

And in `App.js` let's add another button:

```JSX
<button onClick={() => this.props.dispatch({ type: 'BUTTON_ONE' })}>Button One</button>
<button onClick={() => this.props.dispatch({ type: 'BUTTON_TWO' })}>Button Two</button>
```

- Exercise: add a reducer that logs `action`. What is the `action`? Where did it come from? How can you tell which button was clicked from inside of the reducer?
