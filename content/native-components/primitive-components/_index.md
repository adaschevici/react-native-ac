+++
title = "Primitive Components"
date = 2020-10-25T15:35:30+01:00
weight = 1
draft = false
+++

## Primitive components also known as core components
{{< lazy-image image="rn_to_native.png" lightbox=false />}}

Core components are the components that are built into `react-native` and map to
native components in `iOS` and `android` but you should keep in mind that the
deeper the hierarchy of components the slower your UI will be. The main concept to
wrap your head around is that even though the `react-native` components map directly
to native components the communication between the two sides happens through a bridge
that is the bottle neck in performance. This is why `react-native` applications are less
performant than true native applications.

{{< lazy-image image="bridge_diagram.png" lightbox=false />}}

The bottleneck in the bridge makes it challenging to have well performing apps in every
single instance and that is why in cases when you want to build highly performing components
you will most likely want to implement the hot paths in native land. The community has provided
quite a few of the patterns that are performance sensitive in a [directory of `react-native`
components](https://reactnative.directory/). This is where you want to have a look when you stumble
on a performance issue in your app to see if anyone has created a solution that solves your problem.

You will most likely use some of the native libraries in the directory without even giving it much thought ie:
- `react-native-navigation`
- `formik`

This is not the only case when you want to use native components, but also when you need to access the lower
level mobile OS APIs for performing specific operations ie:
- `react-native-keychain`
- `react-native-share`
- `expo-notifications`
