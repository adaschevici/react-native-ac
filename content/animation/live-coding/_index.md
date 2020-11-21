+++
title = "Live Coding"
date = 2020-10-30T12:02:23+01:00
draft = false
weight = 4
+++

Where we want to get to is to have the data pulled in passed onto the `Deck` component that handles the card list
constructing. The Card list will be a sequence that is rendered on top of each other. Another thing to note is that the
only card that has the ability to rotate is the one on top, the others are static until the choice is made for the one
on top...essentially the deck behaves as a stack and you swipe the top off.

We need to refactor the `panResponder` to get to allow us to move things around so let's do that.
Our `Deck` component will look like this now:

```js
// Deck.js
import React, { useRef } from 'react'
import { Animated, PanResponder } from 'react-native'
import CoffeeShop from './CoffeeShop'

export default ({ data }) => {
  const position = useRef(new Animated.ValueXY()).current
  const panResponder = useRef(
    PanResponder.create({
      onStartShouldSetPanResponder: () => true,
      onPanResponderGrant: () => {
        position.setOffset({
          x: position.x._value,
          y: position.y._value,
        })
      },
      onPanResponderMove: (_, gesture) => {
        position.setValue({ x: gesture.dx, y: gesture.dy })
      },
      onPanResponderRelease: () => {
        position.flattenOffset()
      },
    })
  ).current
  return (
    <Animated.View
      style={position.getLayout()}
      {...panResponder.panHandlers}
    >
      {data.map((item) => (
        <CoffeeShop item={item} />
      ))}
    </Animated.View>
  )
}
```
{{% notice note %}}
Previously we have used `getTranslateTransform()` to build the animation styles directly in the style prop. There is
another method that allows us to retrieve transform styles that is `getLayout()`. This method will not work with
`useNativeDriver: true` set.
{{% /notice %}}

What we can do now is drag the entire deck around the screen along the x and y axis.
The basic UX we are aiming for is also to rotate the animated element. In order to do this we want to give our list the
ability to rotate. We can do this via `transform: { rotate: '<x>deg' }` similar to what we would do in regular css.

There is one catch though, as we drag the list we also want to rotate it more. What this means is that the angle of
rotation needs to be linearly correlated with the distance the card has moved on the `x-axis`. This is done via
interpolation. The interpolation system allows us to associate two scales and create a linear relationship between them
so that they change in the same way(in our case when we swipe in a specific direction the rotation angle changes in the
way the scale defines it).

Given that the style for the `Animated.View` has become quite complicated we want to extract it into its own function
and we can use it in the style prop of the animated element.

```js
// Deck.js
...
const getCardStyle = (position) => {
  const rotate = position.x.interpolate({
    inputRange: [-500, 0, 500],
    outputRange: ['-120deg', '0deg', '120deg'],
  })
  return {
    transform: [...position.getTranslateTransform(),{ rotate }],
  }
}
...
  return (
    <Animated.View style={getCardStyle(position)} {...panResponder.panHandlers}>
...
```

We can now rotate the card on the screen, next stop we want to enable some kind of threshold where we understand that
the swipe to the left or right has actually been intentional and the user wanted to express liking a specific coffee
shop, otherwise we want the card to spring back to its original state.

- function to make the card spring back into place:
```js
// Deck.js
const resetPosition = (position) => {
  Animated.spring(position, {
    toValue: { x: 0, y: 0 },
    useNativeDriver: true
  }).start()
}
```
We want to hook this into the `onPanResponderRelease` callback like so:
```js
// Deck.js
...
      onPanResponderRelease: (event, gesture) => {
        if (gesture.dx > SWIPE_THRESHOLD) {
          forceSwipe(position, 'right')
        } else if (gesture.dx < -SWIPE_THRESHOLD) {
          forceSwipe(position, 'left')
        } else {
          resetPosition(position)
        }
      },
...
```
The `SWIPE_THRESHOLD` is a constant that is 1/4 of the maximum of the interpolation interval(500) = 125.
- functions to force the completion to the left or right of the animation once we swipe past the `SWIPE_THRESHOLD`
- a callback to make the next card in line swipeable once the card 'on top' is swiped either left or right

Let's do this in the next section which is also an opportunity for few short sprints of individual work improve
our understanding the concepts we learned so far.
