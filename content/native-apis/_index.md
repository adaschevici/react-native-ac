+++
title = "Native Apis"
date = 2020-11-09T11:10:55+01:00
pre = "<b>4. </b>"
weight = 4
draft = false
+++

We previously spoke about `react-native` and that it uses `react`. Compared to other solutions for example `ionic` it
translates `react` components into native components, not running the components in a webview.

The architecture of `react-native` is central around the JS bridge which allows communication with the native side of
your application.

{{< lazy-image image="bridge_diagram.png" lightbox=false />}}

This diagram clearly points out that the javascript bridge can become a bottleneck for passing data to and from the
react library to the native components. [`flutter`](https://flutter.dev/) has a more performant approach as they started
later and had a better understanding of where the potential performance problems could arise.

You may be asking if that means that every app should be started on flutter and/or rewritten from `react-native` to
`flutter`. Well...the answer is complicated, my personal take on this is that `react` is quite nice and the fact that
`react` is js the code will feel much more familliar than `dart`, the language that `flutter` is based on.

The `react-native` team has also started working on a more parallel solution to the JS bridge in 2018. The idea behind
it is to generate the shadow rendering tree in C++ which is much faster than using JS bytecode. The new architecture is
codename `fabric`. You can track progress on this on [github](https://github.com/react-native-community/discussions-and-proposals/issues/4).

{{< lazy-image image="new_bridge_diagram.png" lightbox=false />}}

The reason why we are talking about native apis is that most of the components you will see in even the more basic
`react-native` app uses native extensions. The navigation, maps, even redux with offline requires access to native apis
that behave consistently across both platforms.

`react-native` does a great job of hiding the inner workings, but make no mistake, the stuff that goes on under the hood
is very much native. Creating a uniform api that works the same across the two platforms is difficult since what react
native does is convert JS to Kotlin/Java and Swift/ObjectiveC.

While converting to the language is somewhat straightforward converting the design patterns can be much more complex.
While some components are less complex and can be easily translated the more complex components will have their
abstractions pushed more into the native side and on the `react` side you will have something more of a descriptive
language that can be translated to native.

Navigation, Maps, Offline Mode, Calling, Share to other apps and many more functionalitites have native underlying
implementation, more or less transparent. The Navigation for example is pretty different from its web counterparts
exactly because most of the functionality is added in the underlying native layer.

Let's look at a few components in the next sections.
