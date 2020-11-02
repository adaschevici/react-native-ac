+++
title = "Animation Basic"
date = 2020-11-01T10:38:51+01:00
weight = 2
draft = false
+++

Now, for a quick prototype we can have a look at how we can animate an element in `react-native`. We're going to use a
ball and slide it in from the top left of the screen to the bottom right. This is not the most spectacular animation we
could add but it will help us understand how animated views work.

The main idea with animation is that we need to go through the mental model of figuring out 3 things:

- what is the starting position ValueXY()
- what is the end position after the animation has finished
{{<highlight javascript "hl_lines=2">}}
    Animated.spring(springAnim, {
      toValue: { x: 200, y: 500 },
      useNativeDriver: true, // use this for accessing native performance animations
    }).start()
{{</highlight>}}
- what is the element that will be animated using `Animated.View`, in our case a very simple component, a `View` that
  represents the bouncy ball we want to display

The way `react` and state works is this in a nutshell.

{{<mermaid>}}
stateDiagram-v2
    s1 : Get initial state
    s2 : Render component
    s1 --> s2
    s2 --> s2 : Update state
{{</mermaid>}}

Animation components are special as they don't go through this pipeline but rather through the [`yoga`](https://yogalayout.com/) layout engine we
mentioned earlier. The component will not re-render on any of the intermediary animation steps and there are no
callbacks to state when animating a component. That is why it is better to use `Animated` instead of using state to
change the visual aspect of your components if that is an option.
