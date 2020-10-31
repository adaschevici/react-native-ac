+++
title = "Self Improvement"
date = 2020-10-29T19:34:03+01:00
draft = false
weight = 4
+++

1) now that we have the data available we want to display it nicely in a list using the component library we installed
and  the react native list component.

Essentially what we are aiming to achieve is have a `FlatList` with an item that is a `ListItem` from
`react-native-elements` and make use of the data we have available from the yelp api

```js
...
const FlatListBasics = () => {
  return (
    <View style={styles.container}>
      <FlatList
        data={[
          {key: 'Devin'},
          {key: 'Dan'},
          {key: 'Dominic'},
          {key: 'Jackson'},
          {key: 'James'},
          {key: 'Joel'},
          {key: 'John'},
          {key: 'Jillian'},
          {key: 'Jimmy'},
          {key: 'Julie'},
        ]}
        renderItem={({item}) => <Text style={styles.item}>{item.key}</Text>}
      />
    </View>
  );
}
...
```
```js
...
<View>
  {
    list.map((l, i) => (
      <ListItem key={i} bottomDivider>
        <Avatar source={{uri: l.avatar_url}} />
        <ListItem.Content>
          <ListItem.Title>{l.name}</ListItem.Title>
          <ListItem.Subtitle>{l.subtitle}</ListItem.Subtitle>
        </ListItem.Content>
      </ListItem>
    ))
  }
</View>
...
```

2) Test the limits of our device by creating a list with a large number of items using [faker](https://github.com/marak/Faker.js/).
Sometimes in order to assess whether your UI would work well with large amounts of data you want to have some artificial
data to test your assumptions against. Faker provides a way for you to do this.
