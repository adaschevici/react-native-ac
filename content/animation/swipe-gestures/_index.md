+++
title = "Swipe Gestures"
date = 2020-10-30T12:01:35+01:00
weight = 2
draft = false
+++

The main component that will help you respond to touch gestures is the `panResponder`. It is a very complex component
and has a very rich interface because there are quite a few gestures that can be performed on devices and you want to be
able to detect them properly in your components.

The `panResponder` interface looks something like this in a component:

```js
const ExampleComponent = () => {
  const panResponder = React.useRef(
    PanResponder.create({
      // Ask to be the responder:
      onStartShouldSetPanResponder: (evt, gestureState) => true,
      onStartShouldSetPanResponderCapture: (evt, gestureState) =>
        true,
      onMoveShouldSetPanResponder: (evt, gestureState) => true,
      onMoveShouldSetPanResponderCapture: (evt, gestureState) =>
        true,

      onPanResponderGrant: (evt, gestureState) => {
        // The gesture has started. Show visual feedback so the user knows
        // what is happening!
        // gestureState.d{x,y} will be set to zero now
      },
      onPanResponderMove: (evt, gestureState) => {
        // The most recent move distance is gestureState.move{X,Y}
        // The accumulated gesture distance since becoming responder is
        // gestureState.d{x,y}
      },
      onPanResponderTerminationRequest: (evt, gestureState) =>
        true,
      onPanResponderRelease: (evt, gestureState) => {
        // The user has released all touches while this view is the
        // responder. This typically means a gesture has succeeded
      },
      onPanResponderTerminate: (evt, gestureState) => {
        // Another component has become the responder, so this gesture
        // should be cancelled
      },
      onShouldBlockNativeResponder: (evt, gestureState) => {
        // Returns whether this component should block native components from becoming the JS
        // responder. Returns true by default. Is currently only supported on android.
        return true;
      }
    })
  ).current;

  return <View {...panResponder.panHandlers} />;
};
```

You will notice that the `panResponder` is hooked into the component reference by having a ref via the [useRef hook](https://reactjs.org/docs/hooks-reference.html#useref).
This is required as in functional version you no longer work with the `this` pointer so you need to somehow access the
reference to the component. Before hooks, and in the class version of the docs you will see that in the base case the
`panResponder` is added into the component state.

We want to create a Deck component that holds all the coffeeshop cards and the `panHandler` will allow us to express our
liking of a coffeeshop by swiping right or disliking by swiping left.

The most basic version of a `panResponder` consists of three callbacks and will allow us to get a feeling for what
events are triggered at each point in the gesture motion.

```js
export default () => {
  const panResponder = useRef(
    PanResponder.create({
      onStartShouldSetPanResponder: () => true,
      onPanResponderMove: (event, gesture) => {
        console.log(gesture)
      },
      onPanResponderRelease: () => {},
    })
  ).current
  return (
    <View style={styles.container} {...panResponder.panHandlers}>
      <Text>The deck component</Text>
    </View>
  )
}
```

This is the least amount of useful code we can use to see if we have the `panResponder` wired correctly. We need to
enable remote JS debugging from the emulator in order to be able to do printf debugging.

{{% notice note %}}
You should be careful when working with remote JS debugging because if you don't stop it you will find that the app
hangs and stops the hot reloading if it looses connectivity to the debug tools so it is best to stop the remote debugger
whenever you finish a debugging session so that you won't need to turn everything off and on again. Expo has made huge
leaps in making a lot of the work with simulators seamless however it is a highly complex system with many moving pieces
and even with the improvements, communication between the components may break down.
{{% /notice %}}
