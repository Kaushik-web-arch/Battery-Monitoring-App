# Battery Monitoring App

An Android app that measures how much battery a phone loses while its screen is off. It shows the battery level before and after each session, the elapsed time, and the estimated drain in percentage points per hour.

I contributed Android monitoring, local storage, and integration to **Ghost Drain Hunter**, a three-person DAEMON HOUR hackathon project. This repository packages the team's final implementation for my portfolio. Prithvi developed the drain analysis and its unit tests. Adithya built the Compose dashboard and verified the app on an emulator.

## Problem statement

Record the battery percentage and time when the screen turns off and when it turns back on. Calculate how many battery percentage points were lost per hour and flag sessions above a threshold. Bonus goal: keep a history of screen-off sessions.

## Try it

1. Open the app and tap **Start Monitoring**.
2. Unplug the device, turn the screen off, and turn it back on after a while.
3. Open the dashboard to see the readings, duration, and hourly estimate. Adjust the alert threshold with the slider and review earlier sessions below.

## Design decisions

| Question | Answer |
| --- | --- |
| What makes it useful? | Each entry shows exactly when the drain occurred and how much battery was lost. The history makes it easy to compare individual screen-off periods, such as a short break and an overnight session. |
| How is the rate calculated? | Divide the battery percentage points lost by the elapsed hours. A drop from 80% to 78% over 30 minutes gives 4 percentage points per hour. |
| Why can a short session show a large rate? | Android supplies whole-percent readings. A one-point change over a few seconds can produce a large hourly estimate, so the app marks short sessions. Charging sessions and invalid readings are handled separately. |
| Which APIs does it use? | A foreground service reacts to Android screen-off and screen-on broadcasts. It reads Android's battery level and charging state and measures duration with Android's elapsed-time clock. |
| Which database does it use? | Android SharedPreferences stores recent session history as JSON on the phone. The app works offline and requires no external API key or account. |
| Why Kotlin and Compose? | Kotlin gives the app direct access to Android APIs. Jetpack Compose and Material 3 render the monitor state, adjustable threshold, alert, and history in one dashboard. |
| Can it identify the app draining the battery? | The measurements describe device-wide battery loss during a screen-off session. Identifying the responsible app would require additional data. |

## Build and run

Use Android Studio with JDK 17 and Android SDK 35. The app runs on Android 8.0 (API 26) or later. Open the repository folder, sync Gradle, select a device or emulator, and run the app.

On Windows, build the debug APK and run unit tests with:

```powershell
.\gradlew.bat :app:assembleDebug :app:testDebugUnitTest
```

On macOS or Linux, run:

```sh
./gradlew :app:assembleDebug :app:testDebugUnitTest
```

The debug APK is written to `app/build/outputs/apk/debug/app-debug.apk`.

The team built and tested the original application at revision `7012d7f` and ran a screen-off/on cycle on an Android emulator. One demo session recorded a drop from 99% to 98% and displayed the high-drain alert. [Tested source revision](https://github.com/Kaushik-web-arch/DAEMON-HOUR/tree/7012d7fa10b72e94f5b52b62cd5ba442542e0fc3)
