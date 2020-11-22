+++
title = "Offline First"
date = 2020-11-09T15:12:53+01:00
draft = false
weight = 3
+++

We want to install redux to have a central JSON state and `redux` fits the bill for this.

```bash
npm i redux react-redux redux-thunk
```

We will be using `redux-thunk` for fetching the data and binding async actions to the components. This simplifies the
data flow in the app and slims down our components. Previously we had the data fetching embedded in the `screens/Map.js`
and the `screens/Deck.js` and stored in the local state.

In the last 1-1 1/2 years we saw an uptick in hooks usage to simplify react components but it seems to have created a
more diversified range of methods to create structures of state in our apps. Just to be clear using redux is not the
same state management as using `useReducer` from `react`. Redux is sync and working with async requires middlewares.
Currently hooks don't have a mature enough ecosystem as `redux` does.

Depending on the size of your application you want to use a specific type of state management approach
- Use `useState` for basic and simple/small size applications.
- Use `useState` + `useReducer` + `useContext` for advanced/medium size applications.
- Use `useState/useReducer` + `Redux` for complex/large size applications.

`react-native`s main job is making native apps development more straightforward. The most obvious difference between web
and mobile apps is that mobile apps have a much higher degree of autonomy. PWAs bring similar features but to a lesser
extent.

In common speak we call this autonomy of an app `offline-mode`, offline because we want our app to have decent
functionality even when the device is not connected to the internet. This is achieved using redux because redux makes
caching a lot easier due to its JSON serializable structure.

Another advantage of using `redux` is that the method that fetches the coffeeshops can be done in a central place so
it decreases code duplication and could allow for optimizing calls to the yelp API by caching the responses and can be
used across your entire application.

The most useful package we can use for caching redux data is called
[`redux-persist`](https://github.com/rt2zz/redux-persist) it allows you to cache data from actions and restore the store
from the stored data when offline. It behaves quite similarly to how `service-workers` in the way they cache and
restore.

`redux-persist` helps persisting the payload the first time it does the fetch then on subsequent fetches it uses the
last known state. If you are online it works fine, as expected, if you are offline it tries to `REHYDRATE` the store from  the
`AsyncStorage`, this works if you have loaded data into the store at any point in the past.

```js
// store.js
import { createStore, applyMiddleware } from 'redux'
import { persistStore, persistReducer } from 'redux-persist'
import AsyncStorage from '@react-native-community/async-storage'
import thunk from 'redux-thunk'
import rootReducer from './reducers/rootReducer'

const persistConfig = {
  key: 'coffeeshops',
  storage: AsyncStorage,
}

const persistedReducer = persistReducer(persistConfig, rootReducer)

export default () => {
  const store = createStore(persistedReducer, applyMiddleware(thunk))
  const persistor = persistStore(store)
  return { store, persistor }
}
```

The cool part about `redux-persist` is that it works seamlessly with redux. This makes it very easy to integrate with
very little work.

The only thing you need to add in your reducer is a new case for `REHYDRATION`. This works with more than just react
native but for the web there are more options that are more established such as `service-workers`.

```js
// reducers/rootReducer.js
...
    case REHYDRATE:
      return { ...state, coffeeshops: action.payload } || state
...
```

#### Individual workout:
1) Hook the location into `redux`
2) Add redux persist on location operations
3) Can you think of any problems that could pop up if you use `redux-persist`? Hint(think about upgrades)
