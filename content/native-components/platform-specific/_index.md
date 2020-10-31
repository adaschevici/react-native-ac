+++
title = "Platform Specific"
date = 2020-10-25T15:35:45+01:00
weight = 2
draft = false
+++

`react-native` in essence aims to provide an umbrella platform
for both `iOS` and `android`. This seems like a dream combo, but
contrary to what you may want in web development, in mobile development
`android` and `iOS` have platform specific behaviors as well and they
are not considered bugs.

`react-native` bridges the gap by implementing some specific features
on `iOS` and `android`, and also gives you access to platform detection
for implementing different beahvior when on one platform or another.(For example
some styles may look differently on `iOS` and `android`)

- sharing to other apps is different, share link to email for example
- permissions are different
- default maps are different and `react-native` allows you to specify which to use

The [docs](https://reactnative.dev/docs/components-and-apis#ios-components-and-apis) reference
specific UI features that are specific to either `iOS` or `android`. The idea is that you should
provide an experience that is seamless for users of both platforms.
