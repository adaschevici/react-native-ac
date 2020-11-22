+++
title = "Expo"
weight = 3
date = 2020-10-21T15:28:34+02:00
draft = false
+++

After `react-native` started making waves the community built up a platform around the library to further abstract away
the mobile os specific aspects, like deployment.

Expo wraps 90% of the painful aspects of `react-native` coming with about 30 native modules built in and offering easy
access to them from your app. The idea is you can set up the `expo-cli` and bootstrap an app in minutes and hook it up
to your device. This is far easier than the early days of `react-native` where you were forced to gain a good
understanding of at least the build process in order to deploy to your devices.

The way you can get started is by installing the `expo-cli`

```bash
npm init -y # create an npm workspace
npm i -g expo-cli
npx expo init coffeenut
```

We also need to install the `expo-cli` in the created project folder as the cli interface will manage the local
simulators and we need to have `expo` in scope.

{{% notice note %}}
If you plan to run on a simulator you need to have simulators configured for the desired platform by either [XCode](https://developer.apple.com/xcode/) and the osx commandline tools or
[Android Studio](https://developer.android.com/studio) and the `ANDROID_HOME` environment configured for the android tools.
{{% /notice %}}

{{% notice note %}}
If you haven't done this prior to the workshop let's take a break and work through this:
[Android](https://developer.android.com/studio/run/managing-avds) and/or
[iOS](https://docs.experitest.com/display/TC/AS+-+Connecting+An+iOS+Emulator)
{{% /notice %}}

Now that we are all set up to run the app let's go ahead and start up the app. Your `package.json` should look something
like this providing quick npm scripts for the most useful actions

```json
{
  ...
  "scripts": {
    "start": "expo start",
    "android": "expo start --android",
    "ios": "expo start --ios",
    "web": "expo start --web",
    "eject": "expo eject"
  },
  ...
}
```

Let's run the app
```bash
npm run ios # for iOS
npm run android # for android
```

We should be able to see our app running in the simulator of choice.
