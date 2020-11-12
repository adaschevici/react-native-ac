+++
title = "Animation"
date = 2020-10-30T11:04:19+01:00
pre = "<b>3. </b>"
weight = 3
draft = false
+++

We talked about the native bridge which lets the JS side communicate with the native components and how this is the
bottleneck that prevents `react-native` from being as performant as the native code.

In order to get performance to acceptable levels when you have a component that requires huge amounts of back and forth
communication the laggy performance may become unbearable.

A common case where our components are unusually chatty is in the case of animations. Fortunately this has been
anticipated by the smart folks that built `react-native` and the animation communication bridge is separate from the
normal JS communication channel. It is called `yoga` and if you have been dealing with `react-native` long enough you
will have seen this in the console and/or error messages at some point.

[`yoga`](https://yogalayout.com/) is a flexible layout engine that you can use in multiple environments, `react-native`
makes use of it for mobile native. The yoga layout engine can interpret css style animations so animations will not use
the component state but rather the component styles.

Planinly put the transition of a component from one style to another is generated and transmitted over the `yoga layout
engine` rather than the JS bridge, so you should try to use style transitions when performing animations(at least) as
opposed to component state changes because the style transitions are not passed across the JS bridge.

Another project I really like that uses `yoga` is [`ink`](https://github.com/vadimdemedes/ink), it uses `yoga` to
translate `react` UIs into [`ncurses`](https://tldp.org/HOWTO/NCURSES-Programming-HOWTO/) cli interfaces. If you haven't figured out by now I am a huge `cli` geek.

{{< lazy-image image="command_line.gif" lightbox=false />}}
