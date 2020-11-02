+++
title = "Cards Layout"
date = 2020-10-30T12:00:44+01:00
weight = 1
draft = true
+++

We will convert the list we've been working on previously to a tinder like swipe-right/-left style app. As you can
imagine this approach is pretty animation heavy and we will see how to use the gesture specific components in
`react-native`, more specfically the `panResponder`. Also we will have a look at the `Animated.View` which is the most
basic primitive available to `react-native` components for animating.

As a rule of thumb:

- some components contain the animation by default, for example the sidebar menu
- implementing custom animation will most likely require using `Animated.View`
- implementing gesture based interaction and of course animating it. For example a tinder based animation would mean
  animating a slide(left/right) and at the same time we have a rotating motion on the card when you swipe to the left or
  right


Let's swap out and switch the list of items with `Card` components from the `react-native-elements` library. This will
look more polished than the previous version and closer to the final designs.

The basic gist of it will look something like this, our goal is to replace the list items with cards.
```js
<Card>
  <Card.Title>HELLO WORLD</Card.Title>
  <Card.Divider/>
  <Card.Image source={require('../images/pic2.jpg')} />
  <Text style={{marginBottom: 10}}>
      The idea with React Native Elements is more about component structure than actual design.
  </Text>
  <Button
    icon={<Icon name='code' color='#ffffff' />}
    buttonStyle={{borderRadius: 0, marginLeft: 0, marginRight: 0, marginBottom: 0}}
    title='VIEW NOW' />
</Card>
```

The resulting UI is a bit complex so we want to break it down into reusable functional components.

We also want to pull in a review to add into the card text so that we get a feeling for what the place is like. The API
endpoint we can fetch this from is [here](https://www.yelp.com/developers/documentation/v3/business_reviews). We will
pull this remote data in the card component since each card will have the business ID. Let's take a few minutes and try to
implement that.
