+++
title = "Native & Components"
date = 2020-10-20T18:13:41+02:00
weight = 2
pre = "<b>2. </b>"
draft = false
+++

`react-native` is different from the WebView based approaches for app development. In `react-native` the components are
converted to actual native components and that is why the components are not web, but a specific set of components that
can map to the specific native corresponding components.

#### Component mapping for primitive components
{{< lazy-image image="rn_to_native.png" lightbox=false />}}

Usually the transition from `react` for the web to `react-native` is quite intuitive as the only thing that changes is
the tags that are used. The semantics of `<View />` is a reference to the native part in `react-native` as mobile app
frameworks have the view as the central primitive. What this means in practice is that any UI can be built as a
hierarchy of `<View />`s.
