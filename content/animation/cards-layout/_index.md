+++
title = "Cards Layout"
date = 2020-10-30T12:00:44+01:00
weight = 1
draft = true
+++

We will convert the list we've been working on previously to a tinder like swipe-right/-left style app. As you can
imagine this approach is pretty animation heavy and we will see how to use the gesture specific components in
`react-native`, more specfically the `panResponder`. Also we will have a look at the `Animated.View` which is the most
basic primitive available to `react-native` components.

As a rule of thumb:

- some components contain the animation by default, for example the sidebar menu
- implementing custom animation will most likely require using `Animated.View`
- implementing gesture based interaction and of course animating it. For example a tinder based animation would mean
  animating a slide(left/right) and at the same time we have a rotating motion on the card when you swipe to the left or
  right
