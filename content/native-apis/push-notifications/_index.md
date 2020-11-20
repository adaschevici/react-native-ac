+++
title = "Push Notifications"
date = 2020-11-15T22:54:48+01:00
draft = false
weight = 4
+++

The last native feature we will have a look at is push notifications. Push notifications are historically proven to be a
huge pain. You had to add push notification app tokens and wiring up a system that had the ability to notify users you
had to jump through many hoops. You had to add ios push notification certificates and generate the certificates locally
and upload them to your apple accounts and debugging sending notifications was a really annoying process.

There are multiple third party services that allows you to uniformly set up notifications for `iOS` and `android`, for
example [OneSignal](https://onesignal.com/). It comes with sdks for iOS and android which help with integration that
works with `react-native` and web apps. There are 2 big entities for sending push notifications be they web or
otherwise: [Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns/)
and [Google](https://firebase.google.com/docs/cloud-messaging/). Given that the two messaging pipelines are
fundamentally different building a unified behavior is extremely challenging.

Expo created a much more streamlined pipeline for sending notifications to devices and due to the ease of deployment of
the apps to your physical device testing it is much more straightforward.

The expo tool for sending mobile push notification can be found on their website [here](https://expo.io/notifications).
The notifications can be tested from the tool and you can send the notifications to your app once you have your APP
notification token.

{{< lazy-image image="push_tool_expo.png" lightbox=false />}}

The main thing about enabling push notifications is linked to permissions and using the push notifications token from
expo. We want to add the code at the main component of our app as the push notification is not dependent on any one
screen but is in fact external to our app and is hooked into the notifications service of our respective platforms.

{{<highlight javascript "hl_lines=6-16 20-51 54-80">}}
// App.js
import React, { useState, useEffect, useRef } from 'react'
import AppNavigation from './Navigation'
import { Provider } from 'react-redux'
import configureStore from './store'
import Constants from 'expo-constants'
import * as Notifications from 'expo-notifications'
import * as Permissions from 'expo-permissions'

Notifications.setNotificationHandler({
  handleNotification: async () => ({
    shouldShowAlert: true,
    shouldPlaySound: false,
    shouldSetBadge: false,
  }),
})

const { store, persistor } = configureStore()

async function registerForPushNotificationsAsync() {
  let token
  if (Constants.isDevice) {
    const { status: existingStatus } = await Permissions.getAsync(
      Permissions.NOTIFICATIONS
    )
    let finalStatus = existingStatus
    if (existingStatus !== 'granted') {
      const { status } = await Permissions.askAsync(Permissions.NOTIFICATIONS)
      finalStatus = status
    }
    if (finalStatus !== 'granted') {
      alert('Failed to get push token for push notification!')
      return
    }
    token = (await Notifications.getExpoPushTokenAsync()).data
    console.log(token)
  } else {
    alert('Must use physical device for Push Notifications')
  }

  if (Platform.OS === 'android') {
    Notifications.setNotificationChannelAsync('default', {
      name: 'default',
      importance: Notifications.AndroidImportance.MAX,
      vibrationPattern: [0, 250, 250, 250],
      lightColor: '#FF231F7C',
    })
  }

  return token
}

export default () => {
  const [expoPushToken, setExpoPushToken] = useState('')
  const [notification, setNotification] = useState(false)
  const notificationListener = useRef()
  const responseListener = useRef()

  useEffect(() => {
    registerForPushNotificationsAsync().then((token) => setExpoPushToken(token))

    // This listener is fired whenever a notification is received while the app is foregrounded
    notificationListener.current = Notifications.addNotificationReceivedListener(
      (notification) => {
        setNotification(notification)
      }
    )

    // This listener is fired whenever a user taps on or interacts with a notification (works when app is foregrounded, backgrounded, or killed)
    responseListener.current = Notifications.addNotificationResponseReceivedListener(
      (response) => {
        console.log(response)
      }
    )

    return () => {
      Notifications.removeNotificationSubscription(notificationListener)
      Notifications.removeNotificationSubscription(responseListener)
    }
  }, [])

  return (
    <Provider store={store}>
      <AppNavigation />
    </Provider>
  )
}
{{</highlight>}}

