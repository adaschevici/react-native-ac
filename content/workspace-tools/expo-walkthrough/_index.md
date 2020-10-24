+++
title = "Expo Walkthrough"
weight = 5
date = 2020-10-23T13:05:54+02:00
draft = false
+++

`expo` comes with a web interface that allows you to control the running processes and start up the available simulators
from the `expo` control panel without having to deal with the comman line.

{{< lazy-image image="expo_web_ui.png" lightbox=false />}}

The web UI is connected to the Metro Bundler via a socket and on any code changes it will perform hot reloads,
redeploying the bundle to the device. `expo` has gone a long way to allow the developer to focus on the actual code and
it has gained so much popularity that it was incorporated in the [RN docs](https://reactnative.dev/docs/handling-text-input)
for running interactive code and bootstraping applications.

`expo-cli` and `react-native` cli are compatible with one another and migration between the two should be rather
easy to do. Expo is the managed way of working with `react-native` applications.

{{% notice note %}}
In `react` you would create an app and if you require more specific library compatibility then you may choose to
`eject`. There is a similar operation in `expo` and is called `eject` too. In `expo` when ejecting what happens is you
exit into the [bare workflow](https://docs.expo.io/bare/exploring-bare-workflow/)
{{% /notice %}}


{{% notice info %}}
`expo` has replaced the now deprecated `create-react-native-app`. There is a [glossary of
terms](https://docs.expo.io/workflow/glossary-of-terms/#create-react-native-app) where you can find an up-to-date list
of terms that you will encounter while working with `expo`
{{% /notice %}}
