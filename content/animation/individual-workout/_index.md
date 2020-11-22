+++
title = "Individual Workout"
date = 2020-10-30T12:02:33+01:00
draft = false
weight = 5
+++

1. Now that we have a card that swipes and rotates in both directions we want to have the rotation behave consistently
across screen sizes. Let's make it dependent on screen size.
Using `Dimensions` from `react-native` replace the hardcoded values(500, 125 etc) from the rotation animation

2. Write the animations for the `forceSwipe(direction)`. Use the `Animated.timing(...)` animation primitive
   from `Animated`.

Once this is done we will do a live-coding/pair programming session to wrap up the like/dislike functionality together
with handlers for both actions and promoting the next card in the sequence to being swipeable.

3. Create a component to display when there are no more cards in the deck(we swiped all the cards in the deck and deck
   is empty) and a component to display when there are no cards in the deck in the first place.
   You can put this functionality on the deck and render the component when `data.length` is 0
   ```js
   <EmptyDeck />
   ```
   and the deck is done component when swiping is done(`currentIndex` >= `data.length`)
   ```js
   <NoMoreCards />
   ```
   These components are static and don't require any data to be passed in.

{{% notice tip %}}
Our app is looking pretty good right now. There is one thing that isn't quite right yet. The card image is flickering as
it gets promoted to the swipeable state. This is not critical but does not look polished enough for a production app.
The reason for this is that the top card is rerendered and wrapped in an `Animated.View`. This is causing the flicker.
{{% /notice %}}

```js
// Deck.js
      ...
      {currentIndex < data.length ? (
        data
          .map((item, index) => {
            if (index < currentIndex) return null
            if (index === currentIndex) {
              return (
                <Animated.View
                  key={`${item.name}-${index}`}
                  style={[getCardStyle(), styles.cardStyle]}
                  {...panResponder.panHandlers}
                >
                  <CoffeeShop item={item} />
                </Animated.View>
              )
            } else {
              return (
                <Animated.View
                  key={`${item.name}-${index}`}
                  style={[
                    styles.cardStyle,
                    { zIndex: -1, marginTop: (index - currentIndex) * 10 },
                  ]}
                >
                  <CoffeeShop item={item} />
                </Animated.View>
              )
            }
          })
          .reverse()
      ) : (
        <NoMoreCards />
      )}
      ...
```

We completed the lengthiest part of the course, animation is very difficult in any environment as there is a lot of
maths and library/frameowrk concepts as well but we have a pretty nice app now with UI similar to Tinder.
