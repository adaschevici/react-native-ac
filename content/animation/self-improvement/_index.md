+++
title = "Self Improvement"
date = 2020-10-30T12:02:33+01:00
draft = false
weight = 5
+++

1. Now that we have a card that swipes and rotates in both directions we want to have the rotation behave consistently
across screen sizes. Let's make it dependent on screen size.
Using `Dimensions` from `react-native` replace the hardcoded values(500, 125 etc) from the rotation animation

2. Write the animations for the `forceSwipe(position, direction)`. Use the `Animated.timing(...)` animation primitive
   from `Animated`.

Once this is done we will do a live-coding/pair programming session to wrap up the like/dislike functionality together
with handlers for both actions and promoting the next card in the sequence to swipeable.

