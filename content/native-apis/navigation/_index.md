+++
title = "Navigation"
date = 2020-11-09T15:11:47+01:00
weight = 1
draft = false
+++

We created a great app so far but it has a single screen with a deck of cards. That's pretty cool but most real apps
have a lot more screens than this. I mean one screen is fine and all but even games have a settings section.

In `react-native` navigation has multiple alternatives. Expo already has one option baked in by default and for use in
managed expo apps this is the recommended expo way to do routing in your app.

React navigation can be achieved by:
1. [`react-navigation`](https://reactnavigation.org/docs/getting-started/) - `expo` recommended way
2. [`react-native-navigation`](https://github.com/wix/react-native-navigation) - this is a native alternative from wix
   but will not work in the managed flow with `expo`

Navigation looks different between iOS and android but the `react-native` library abstracts this away. There are
multiple designs for mobile app navigation but the `react-navigation` uses the most current navigation design for either
of the platforms.

{{% notice note %}}
There are many designs for navigation...to get an idea of how many there are you can [check it out](https://uxplanet.org/top-8-mobile-navigation-menu-design-for-your-inspiration-8a2d925bffc0)
but be sure to not make it too flashy as it may draw attention from the actual functionality. It is better to have a
clean UI to promote a professional image of your product.
{{% /notice %}}

In order to start using navigation we need to set it up from `expo`
```bash
npm install @react-navigation/native
npm install @react-navigation/stack
npm install @react-navigation/bottom-tabs
npx expo install react-native-gesture-handler react-native-reanimated react-native-screens react-native-safe-area-context @react-native-community/masked-view
```

Now that we have the basic packages set up we should clean up the folders and structure them in a more discoverable way
so that we can navigate the code. The directory structure we are aiming for looks something like this:

```bash
├── App.js
├── app.config.js
├── assets
│   ├── favicon.png
│   ├── icon.png
│   └── splash.png
├── babel.config.js
├── components
│   ├── Cards.js
│   ├── CoffeeShop.js
│   ├── EmptyDeck.js
│   └── NoMoreCards.js
├── package-lock.json
├── package.json
└── screens
    └── Deck.js

3 directories, 13 files
```

`App.js` is the entrypoint for the app and this is where our navigation needs to go. We will add a `react-navigation`
instance here to help us create a navbar on both iOS and android.

As we mentioned before we have to build a navigation component. There are 3 types of `navigators` included with
`@react-native/navigation`. We have the `stack`, the `sidebar` and the `tab` navigators.

We can use the navigation creation functions from `@react-navigation/*`
1) `createStackNavigator` and `createNativeStackNavigator` - creates the stack navigator
2) `createDrawerNavigator` - creates a drawer navigator
3) `createBottomTabNavigator`, `createMaterialBottomTabNavigator` and `createMaterialTopTabNavigator` - create a tab
navigator, we will be using the `@react-navigation/bottom-tabs`

The navigation component can become quite complex over time depending on the nesting levels. Individual screens can
contain extra routes, like a settings route in a dashboard component that leads to the settings for that specific
dashboard.

In any application we will be using a mixture of navigators, the way to compose them is by creating each of the
navigators then nesting them. Let's look at how this will look in code.

#### Individual workout
1) Experiment with icons from the font awesome package that is included in the `react-native-vector-icons`. For example
you could use icons for ios from `Ionicons`
2) Add 2 new screens that will be displayed instead of the `Deck` screen so that navigating will provide us with visual
feedback
