+++
title = "Workspace and Tools"
chapter = true
weight = 1
pre = "<b>1. </b>"
date = 2020-10-20T17:48:51+02:00
draft = false
+++


## How to choose

There are multiple approaches when working with a client that requests a native app. Usually we would not want to
reinvent and rebuild the entire codebase from scratch, even though sometimes it may be appealing. Our duty as software
developers is to be result driven but at the same time lazy.

The best choice as always depends on the situation. You may have an already established product that requires building a
mobile client application, or you are starting from scratch. You may have a requirement for building both a mobile app and a
web app on the same timelines.


## Options

The approaches we came up with while investigating the ecosystem are:

**You can build your web app as a PWA which allows you to reuse the code in your web code in your mobile version**

#### Pros:
- maximum code reuse and the app essentially stays the same
- bypass the store deployment process
- fast release cycle

#### Cons:
- some cognitive overhead on the service worker libraries and caching strategies
- some features may not be achievable
- the number of browsers that are compatible with service workers is growing but is not a huge market share
- the UX flow of installing an app from the appstore is ingrained so the flow for PWA may feel strange
- performance of the app is not as good as the native and crossplatform native apps

**You can build a hybrid application using something like [Ionic](https://ionicframework.com/) or [Cordova](https://cordova.apache.org/) which will essentially run in a webview on
   your device.**
#### Pros:
- maximum code reuse
- bypass the store deployment process
- fast release cycle
- good compatibility with most devices

#### Cons:
- you need to use the component libraries provided by the respective framework so  the look and feel will feel different
  from native apps
- performance of the app is not as good as the native and crossplatform native apps


**Native app with Java/Kotlin(Android) and Swift/ObjectiveC(iOS)**
#### Pros:
- best performance in most cases
- good compatibility with devices
- access to all native APIs and low level features
- native look and feel is something that comes out of the box

#### Cons:
- codebase is duplicated and parity needs to be maintained if both platforms are targeted
- release cycle is subject to their respective  appstore
- due to having two code bases the apps can easily diverge


**Cross platform native [NativeScript](https://nativescript.org/faq/how-does-nativescript-work/), [React Native](https://reactnative.dev/) and [Flutter](https://flutter.dev/)**
#### Pros:
- very good code reuse
- bypass the store deployment process in some cases
- fast release cycle
- very close to native performance

#### Cons:
- some native features are notoriously difficult to handle (ie bluetooth)
- when working closely on native features you will need to dive into native code
