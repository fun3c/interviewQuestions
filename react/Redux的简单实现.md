## Redux的简单实现

```js
const createStore = (reducer) => {
  let state;
  const listeners = [];
  const getState = () => state;

  const dispatch = (action) => {
    state = reducer(state, action);
    listeners.forEach(listener => listener());
  };

  const subscribe = (listener) => {
    listeners.push(listener);
    return () => {
      listeners = listeners.filter(ls => ls !== listener);
    }
  };
  // init
  dispatch({});

  return { getState, dispatch, subscribe };
};
```