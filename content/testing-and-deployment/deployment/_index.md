+++
title = "Deployment"
date = 2020-11-16T16:08:13+01:00
draft = false
weight = 2
+++

Expo has done a lot in terms of usage of the two simulators and it is much easier now to run on both platforms however
when it comes to deployment the pain has prevailed. There are many small issues that cause subtle problems along the
way.

There are many third party platforms that aim to provide some haven from the ever-changing apis of the two platforms.
Maintaining a true CI/CD pipeline for your apps can prove extremely challenging as the dependency management especially
on upgrades is a big hassle.

Third party publishing platforms:
1. Expo is a pretty good publishing platform that has a freemium model
    - if you are on a free account you need to wait in order to publish a package (`.apk`, `.ipa`)
    - if you are on a paid plan then you still have to queue but the wait is shorter

2. [Code Magic](https://codemagic.io) is quite good if you want to make the publishing even smoother, but the price
   point is higher. However I would recommend it for one offs and when you are in a rush to get an app out. It can give
   you the breathing room to allow for building a proper pipeline.


Publishing the app to their respective stores requires accounts on both [Google Play](https://play.google.com/console/developers) and [App Store](https://developer.apple.com/support/compare-memberships/).
In order to publish to the App store the membership cost is $99/year so it isn't worth keeping one if you aren't
don't have a healthy pipeline of published apps. The pricing for the play store is a one time flat fee of $25.

I prefer the playstore for a few reasons, it's a bit cheaper so it has a lower barrier for experimentation and also the
code is a bit more familiar since it is JVM based and also I prefer the google model to the apple model.

The publication to the Play Store and to the App Store is a matter of logging into the account of your organisation and
uploading the built `.apk` or `.ipa`. Unfortunately at this time there still isn't a fully automatic way to do this
other than using a specialized third party service. If tasked with publishing the app our suggestion is to build the
app and upload it to the production environment account.

On both [Apple](https://instabug.com/blog/how-to-submit-app-to-app-store/) and [Google Play](https://themanifest.com/mobile-apps/how-publish-app-google-play-step-step-guide)
you need to create specific types of accounts with special permissions and link with other parts of your accounts'
identity.

For our purposes we will have two production level apps. One that can be downloaded internally for testing and one that
is generally available. For the GA side of things it would mean that you require developer accounts for both platforms
so we will focus on the staging deployment instead. It will allow for online installation but it will not be accessible
from the appstores.

Building and distributing apps via Expo

Distributing apps via Expo has the only downside that it doesn't make use of the resective appstore discoverability,
other than that the built `.ipa` and `.apk` is essentially the same.

We need to have an expo account so let's create one [here](https://expo.io/signup). Once we signed up we can publish our
app changes to expo and have anyone that has Expo installed be able to check our app out.

In order to publish to expo:

```bash
npx expo publish
```

This way of publishing is not enough for the stores but it is enough for testing the app features, however if you eject
from the managed expo flow this will no longer be an option.


In order to build a binary complete version of our app that can be distributed to the app stores we should run this
command and we should see output similar to this. Essentially what it's telling us is that we reserved a spot in the
queue and as soon as an execution timeshare is available our package build will be attempted.

```bash
npx expo build:android

✔ Choose the build type you would like: › apk
Checking if there is a build in progress...

Accessing credentials for arturdas in project coffeebrew
✔ Would you like to upload a Keystore or have us generate one for you?
If you don't know what this means, let us generate it! :) › Generate new keystore
Keystore updated successfully

- Expo SDK: 39.0.0
- Release channel: default
- Workflow: Managed

Building optimized bundles and generating sourcemaps...
Starting Metro Bundler.
Building iOS bundle
Building Android bundle
Finished building JavaScript bundle in 18573ms.
Building source maps
Finished building JavaScript bundle in 12906ms.
Finished building JavaScript bundle in 336ms.
Finished building JavaScript bundle in 148ms.
Building asset maps
Finished building JavaScript bundle in 1424ms.
Finished building JavaScript bundle in 1460ms.

Bundle                     Size
┌ index.ios.js          1.82 MB
├ index.android.js      1.83 MB
├ index.ios.js.map      6.41 MB
└ index.android.js.map  6.43 MB

Learn more about JavaScript bundle sizes

Analyzing assets
Saving assets
No assets changed, skipped.

Processing asset bundle patterns:
- /Users/zero/projects/ws-react-native/coffeebrew/**/*

Uploading JavaScript bundles
Publish complete

📝  Manifest: https://exp.host/@arturdas/coffeebrew Learn more.
⚙️   Project page: https://expo.io/@arturdas/coffeebrew Learn more.

Checking if this build already exists...

Build started, it may take a few minutes to complete.
You can check the queue length at https://expo.io/turtle-status

You can make this faster. 🐢
Get priority builds at: https://expo.io/settings/billing

You can monitor the build at

 https://expo.io/accounts/arturdas/builds/1aa07974-5784-47ab-a325-e60b927653aa

Waiting for build to complete.
You can press Ctrl+C to exit. It won't cancel the build, you'll be able to monitor it at the printed URL.
⠧ Build queued...
```

{{% notice note %}}
If you try to build the binary before you use `turtle-cli` it won't work as you need to define an expo project page
before running the build.
{{% /notice %}}

We can see that the build would take very long in this case since we are on the free tier. Let's try to speed things up.
We can build the `.apk` and `.ipa` locally using the same utils that expo runs under the hood. The tool is called
`turtle-cli`. We can use it to create the binary builds locally and so it will circumvent the build queue on expo.

`npm i -D turtle-cli`

Now we have the tools required for building the assets we need to publish each app to their respective appstores. We
will be working mostly with the android version as the iOS version requires an Apple Developer Account and you need to
pass in the password and username for it.

`EXPO_USERNAME=<expo-username> EXPO_PASSWORD=<expo-password> npx turtle build:android -m debug -t apk`

Once this completes you will be able to see the path to the built `.apk`

`${HOME}/expo-apps/@<username>__<app-name>-<signature>-signed.apk`

#### Both the Apple and Google builds require you to sign the package using the appropriate certs.
Apple has a [different flow](https://developer.apple.com/support/certificates/) for issuing certificates. If you are
familiar with iOS then you must be aware of the code signing requirements that have been enforced by iOS and OSX.

On google play the way to sign it is a bit easier and it involves a few steps but there is much less [red tape around the
process](https://support.google.com/googleplay/android-developer/answer/9842756?hl=en).

Expo should be able to take care of managing the certificates, however if you want to manage your own the have a [good
guide](https://docs.expo.io/distribution/app-signing/) on how to do that.

## Google Play

Expo automatically creates the keystore file, but if you need to create one manually what you want to do is use the
`keytool` cli utility installed along with Android Studio

```bash
keytool -genkey -v -keystore file.keystore -alias YOUR_ALIAS_NAME -storepass YOUR_ALIAS_PWD -keypass YOUR_ALIAS_PWD -keyalg RSA -validity 36500
```

In order to build locally for Android using `turtle-cli`

```bash
export EXPO_ANDROID_KEYSTORE_PASSWORD=<android-keystore-passwd>
export EXPO_ANDROID_KEY_PASSWORD=<android-key-passwd>
turtle build:android --keystore-path /path/to/your/keystore.jks --keystore-alias PUT_KEYSTORE_ALIAS_HERE
```

## Apple
When building for iOS things get a bit more convoluted, I am not a big fan of the way OSX works in the sense that
everything requires yet another type of certificate. This adds a lot of noise on top of the real developer work.

In order to build a binary `.ipa`
- Apple Team ID - (a 10-character string like "Q2DBWS92CA")
- Distribution Certificate .p12 file (+ password) - [guide](https://support.magplus.com/hc/en-us/articles/203808748-iOS-Creating-a-Distribution-Certificate-and-p12-File)
- Provisioning Profile that you can access from your Apple Developer Account

```bash
export EXPO_IOS_DIST_P12_PASSWORD=<dist_p12_password>
turtle build:ios --team-id YOUR_TEAM_ID --dist-p12-path /path/to/your/dist/cert.p12 --provisioning-profile-path /path/to/your/provisioning/profile.mobileprovision
```

Uploading to Play Store and AppStore:


## CircleCI and/or Travis

The most DevOpsy thing we could and should do is try and add a build pipeline to the mix. In order to be able to build
iOS you will have to use OSX capable machines to build the `.ipa`.

[A good template](https://github.com/expo/turtle-cli-example/blob/master/.circleci/config.yml) has been published by the
good folks at expo and you should be able to build on top of this to get your own pipeline up and running. A word of
caution though you should figure out which alternative is the most cost effective depending on your release cycles.
Building your own may seem quite glamorous and technically challenging but may not be the most cost effective every
time.


## Publishing and Updating via Expo

You can publish your binary via Expo and also manually to the respective stores.

```bash
npx expo upload:android
```
- --type <archive-type> - the archive type, by default, it's inferred from the filename extension, choose from: apk, aab
- --key <key> - path to the JSON key used to authenticate with the Google Play Store
- --track <track> - the track of the application to use, choose from: production, beta, alpha, internal, rollout (default: internal)
- --release-status <release-status> - release status (used when uploading new apks/aabs), choose from: completed, draft, halted, inProgress (default: completed)
- --android-package <android-package> - the Android package of your app, if you don't pass this parameter it'll be read from app.json
- --use-submission-service - Experimental: Use Submission Service for uploading your app. The upload process will happen on Expo servers.
- --verbose - always print logs from Submission Service

```bash
npx expo upload:ios --apple-id <apple-id>
```
- --apple-id <apple-id> (required) - your Apple ID login. Alternatively you can set the EXPO_APPLE_ID environment variable.
- --apple-id-password <apple-id-password> (required) - your Apple ID password. Alternatively you can set the EXPO_APPLE_PASSWORD environment variable.
- --app-name <app-name> - your app display name, will be used to name an app on App Store Connect
- --sku <sku> - a unique ID for your app that is not visible on the App Store, will be generated unless provided
- --language <language> - primary language (e.g. English, German; run expo upload:ios --help to see the list of available languages) (default: English)

#### Updates

Android and Apple have different release cycles in that in order to publish new apps you need to have your app reviewed.
This can be a lengthy process and can cause unwanted dealys in your release cycle. This becomes even nastier when you
have updates in your API. This becomes difficult to manage and somewhat unsustainable.

If you use expo publishing and you upload the builds to the store then there will be a really cool thing that I haven't
mentioned so far. The JS bundles can be published and you will see the updated version when you open the app again.

This is by far one of the biggest benefits you get when using `react-native`, you are not always subject to the
timeline on which customers choose to update their apps.

{{< lazy-image image="show_of_hands.gif" lightbox=false />}}
Who likes updating their apps? I'm talking about you chrome orange arrow 😆
