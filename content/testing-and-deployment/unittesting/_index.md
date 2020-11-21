+++
title = "Unittesting"
date = 2020-11-16T16:07:50+01:00
draft = false
weight = 1
+++

Unittesting will be achieved through the same mechanisms we use in base react. We will use `jest` which is the most
popular testing runtime for react at the time of writing.

A popular library that is picking up steam developed in line with the [`@testing-library/react`](https://testing-library.com/docs/react-testing-library/intro/). The library
is called `@testing-library/react-native` and it provides light wrappers around the `react-testing-library`

`@testing-library/react-native` uses `react-test-renderer` under the hood allowing you to write unittests with snapshots
for your `react-native` components. Snapshot tests are Virtual DOM serializations of your components and written into
snapshot files.

Snapshot files are not readable and are only used for structure comparison between changes. Snapshot testing is very easy to
implement however it causes very frequent failures as you update your components. The somewhat cryptic structure also
makes it difficult to understand whether the snapshots should be updated and how. It can be noisy if you keep altering
your apps structure often.

The upside of snapshot testing is that it will allow you to spot any unexpected side effects of any of your changes. So
for example if you change a leaf components' style you can see if it causes any other components to change, and figure
out if there are unintended sideeffects.

## Unittesting setup

```bash
npm i -D jest-expo @testing-library/react-native
```

```js
// We need to add a config file for the jest tests. This enables es6 features for your code
// jest.config.js
module.exports = {
  preset: 'jest-expo',
  moduleNameMapper: {
    '\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$':
      '<rootDir>/__mocks__/fileMock.js',
  },
  transformIgnorePatterns: [
    'node_modules/(?!(jest-)?react-native|react-clone-referenced-element|@react-native-community|expo(nent)?|@expo(nent)?/.*|react-navigation|@react-navigation/.*|@unimodules/.*|unimodules|sentry-expo|native-base|@sentry/.*)',
  ],
}
```
There are 3 options that you should have in your `jest.config.js`.
- preset: this tells jest what set of babel presets it should use to transpile the code in your app and tests
- moduleNameMapper: this specifies what files to be mocked and what the mock should return. It would be useful to test
  opening files on app
- transformIgnorePatterns: file name patterns to ignore from transformation. This is not something to come up with
  yourself, they are usually suggested when you add custom `jest-presets`

This last little tidbit of code is for having the `moduleNameMapper` part of the `jest.config.js`. For example if you
try to read the content of a file what you would get is the string `test-file-stub`.

```js
// __mocks__/fileMock.js
module.exports = 'test-file-stub'
```

In my experience configuring `jest` involves quite a bit of trial and error, especially when you work with a mixture of
es6/7/next transpilation primitives and of course there is also `ts`, fun times 😂

This is the minimal setup for being able to start writing unittests. We installed `jest-expo` to allow jest to
understand es6 syntax and not crash. `@testing-library/react-native` is a modern alternative to `enzyme`. You can use
both `enzyme` and the `@testing-library` together. Enzyme has nicer syntax for snapshots for example so you may want to
combine the two.

A minimal snapshot test we can create is a test to check that a card renders as expected so let's write one that does
that.

```js
// components/CoffeeShop.test.js
import React from 'react'
import { render, fireEvent } from '@testing-library/react-native'
import businesses from '../fixtures'

// Components
import CoffeeShop from './CoffeeShop'

describe('CoffeeShop Card Test Suite', () => {
  it('CoffeShop Card Renders As Expected', () => {
    const { toJSON } = render(<CoffeeShop item={businesses.businesses[0]} />)
    expect(toJSON()).toMatchSnapshot()
  })
})
```

We're going to add a few npm scripts to be able to run the tests more easily.

```json
// package.json
 "scripts": {
...
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage --colors",
...
```

When we first run `npm test` we will see a message that a snapshot has been generated. After this first run subsequent
generated snapshots will be matched against the original to check for changes and if changes are detected the snapshot
will need to be updated.

```bash
Snapshot Summary
 › 1 snapshot written from 1 test suite.
```

#### Unittests with `@testing-library/react-native`

The `@testing-library` namespace contains multiple react testing utilities and the docs are quite helpful. The gist of
it is that you have all the react testing libraries in a single place. There are testing utilities for other frameworks,
there is vue as well, but for our goals we will be focusing on `react-native`.

For unittesting we also need to access values in the children of our component. Doing that will be a more robust
approach than going directly for snapshots. A snapshot test will try to check every attribute of every sub-component and
even if the the component only slightly changes...the test will still fail. While rapidly iterating this will be silly
and will create quite  a bit of noise.

A much better test here would be to check for:
- has a title
- has the divider
- has an image
- has a description
- has a CTA button
- the CTA button has an icon

```js
export default ({ item }) => (
  <Card testID="card-test-id">
    <Card.Title>{item.name}</Card.Title>
    <Card.Divider />
    <Card.Image source={{ uri: item.image_url }} />
    <Text
      style={{ marginBottom: 10 }}
    >{`${item.location.address1}, ${item.location.city}`}</Text>
    <Button
      icon={
        <FontAwesome5
          name="coffee"
          color="#ffffff"
          style={{ marginRight: 10 }}
        />
      }
      onPress={() => Linking.openURL(item.url)}
      buttonStyle={{
        borderRadius: 0,
        marginLeft: 0,
        marginRight: 0,
        marginBottom: 0,
      }}
      title="VIEW NOW"
    />
  </Card>
)
```

`@testing-library/react-native` comes with multiple utility functions to extract information from the `react-native` DOM
structure. What we can do is query the result of render for elements.

```js
...
    const { toJSON, getAllBy, queryAllBy, queryBy } = render(<CoffeeShop item={businesses.businesses[0]} />)
...
```

All the methods return elements matching and you can check the length of the array returned, if it is defined. What I
found a bit peculiar was that you can't access the selection via `xpaths` or `css` selectors, at least not with a
default setup.

To work around this you can put in `testID`s for any of the elements you are interested in. TBH this makes testing much
easier and you can just focus on what you are trying to test rather than the complexities of the underlying test
framework. I rather like easy testing since it helps focusing development in all the right places.

My goto page in the `@testing-library` is the [cheatsheet](https://testing-library.com/docs/react-testing-library/cheatsheet/).
You have all the utility functions that you can use for querying the child elements in your rendered component.

#### Mocks and Jest

One thing we haven touched on so far is the mocking system in jest. In unittests you should not need to access files, OS
features...which in the case of mobile are quite a few and also making http requests should be stubbed and mocked out as
well.

In practice working with mocks is pretty much the most difficult part in tests and building a mock wireframe to deal
with all the 'outside world' can become quite challenging as the complexity of your system grows. You may even wind up
with two similarly sized projects on your hands...one being the `test-library` and one being your actual app so the way
you build up your testing infra really matters in the long run.

We're going to add a test to the more complex `screens/Deck.js` component and watch it fail and dig into the failure,
because we will actually need to fix it step by step.

```js
// screeens/Deck.test.js
import React from 'react'
import { render } from '@testing-library/react-native'
import businesses from '../fixtures'

// Components
import Deck from './Deck'

describe('Deck Test Suite', () => {
  it('Should have an Cards component', () => {
    const props = {
      data: businesses.businesses,
    }
    const wrapper = render(<Deck data={props.data} />)
    expect(wrapper.getAllByTestId('card-test-id').length).toEqual(20)
  })
})
```

Once we run this with `npm test` we will see the test blow up in our face 💥

```bash
 PASS  components/CoffeeShop.test.js
 FAIL  screens/Deck.test.js
  ● Test suite failed to run

    TypeError: Cannot read property 'yelpApiKey' of undefined

      15 |   baseURL: 'https://api.yelp.com/v3',
      16 |   headers: {
    > 17 |     Authorization: `Bearer ${Constants.manifest.extra.yelpApiKey}`,
         |                                                       ^
      18 |   },
      19 | })
      20 |

      at Object.<anonymous> (actions/index.js:17:55)
      at Object.<anonymous> (screens/Deck.js:6:1)

Test Suites: 1 failed, 1 passed, 2 total
Tests:       1 passed, 1 total
Snapshots:   1 passed, 1 total
Time:        2.046s
Ran all test suites.
```

The Constants module is dynamicly generated based on the fact that we are running in a test environment which means we
need to create a module level mock like so:

```js
...
jest.mock('expo-constants', () => ({
  manifest: {
    extra: {
      yelpApiKey: 'super-secret-api-key-xxxxx',
    },
  },
}))
...
```

Jest also has the concept of function mocks so for example when testing a handler you would write something like this:

```js
describe('Fancy Button Test Suite', () => {
  it('Should Handle Click Events', () => {
    const handler = jest.fn()
    const { getByRole } = render(<FancyButton role="button" onClick={handler} />)
    const MyButtonElement = getByRole('button')
    fireEvent.click(MyButtonElement)
    expect(handler).toHaveBeenCalledTimes(1)
  })
})
```

#### A bunch of `<Provider />`s

We have mocked the functions and the module for constants but there are still some failures because our app is using
`redux` and as such it requires the global state management to be available to the components while we are testing as
well.

What we need to do in this case is to add a custom `render` function that has a `<Provider />` wrapper. This is a higher
order function pattern application. To me this has always been the most challenging part when writing tests in redux
applications, finding the right way to wrap the store in a configurable object that I can provide for my tests.

We want to create a render wrapper so that our components are redux aware. Functional components don't require this that
is why it's much better to try and write as many dumb components as you can.

```js
// test-utils/index.js
import React from 'react'
import { render } from '@testing-library/react-native'
import { Provider } from 'react-redux'

const Providers = ({ store }) => ({ children }) => {
  return <Provider store={store}>{children}</Provider>
}

const customRender = (ui, options) => {
  return render(ui, { wrapper: Providers(options), ...options })
}

// re-export everything
export * from '@testing-library/react-native'

// override render method
export { customRender as render }
```

For a little syntactic sugar:
```js
// jest.config.js
module.exports = {
...
  moduleDirectories: [
    'node_modules',
    // add the directory with the test-utils.js file, for example:
    'utils', // a utility folder
    __dirname, // the root directory
  ],
...
}
```

This will allow us to use the custom renderer as we would do any regular node_module.

```js
import React from 'react'
import { render } from 'test-utils'
import thunk from 'redux-thunk'
import businesses from '../fixtures'
import { createStore, applyMiddleware } from 'redux'

// Components
import Deck from './Deck'

jest.mock('expo-constants', () => ({
  manifest: {
    extra: {
      yelpApiKey: '123',
    },
  },
}))

describe('Deck Test Suite', () => {
  let fakeState
  beforeEach(() => {
    fakeState = {
      coffeeshops: businesses.businesses,
    }
  })
  it('Should have an Cards component', () => {
    const store = createStore(() => fakeState, applyMiddleware(thunk))
    const { getAllByTestId } = render(<Deck />, { store })
    expect(getAllByTestId('card-test-id').length).toEqual(20)
  })
})
```

This is looking great so far. It seems that we managed to get the redux store working for the app and we tricked the app
to work with our mock data provided by the fixtures. The fixture provided is a json payload that contains 20
coffeeshops and it's rendering 20 card items...that is purrrrfect.


## Individual Workout

1) Change the `components/CoffeeShop.js` structure so that the snapshot test fails and update the snapshot
2) Add unittests for `components/CoffeeShop.js` that test the Card element structure
3) Add a unittest for the `screens/Deck.js` component to check the behavior when there is no data received from the
endpoint.
