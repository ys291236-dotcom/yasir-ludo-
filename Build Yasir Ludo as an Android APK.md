# Build Yasir Ludo as an Android APK

Yasir Ludo is now wrapped with Capacitor. The Android project is in `android/`, the application ID is `com.yasir.ludo`, and the Android app name is `Yasir Ludo`.

## Why this sandbox did not produce the APK

The web build and Capacitor synchronization succeed here, but this environment does not include a full JDK compiler (`javac`) or an Android SDK. Gradle therefore cannot compile the native Android project. The generated project is ready for a local Android Studio build or a free GitHub Actions build.

## Option 1: Android Studio on Windows, macOS, or Linux

Install the free Android Studio from [developer.android.com/studio](https://developer.android.com/studio). In the SDK Manager, install Android SDK Platform 36, Android SDK Build-Tools 36.0.0, and Android SDK Command-line Tools. Android Studio’s default JDK is sufficient.

From the project root, run:

```bash
pnpm install
pnpm cap:sync
```

Then open the native project:

```bash
pnpm android:open
```

In Android Studio, wait for Gradle sync to finish, select the `app` configuration, and choose **Build > Build Bundle(s) / APK(s) > Build APK(s)**. The installable debug APK will be at:

```text
android/app/build/outputs/apk/debug/app-debug.apk
```

You can also build from a terminal after Android Studio has installed the SDK:

```bash
pnpm android:debug
```

On Windows, use `android\\gradlew.bat assembleDebug` if you run Gradle manually. To install a debug APK on a connected Android device with USB debugging enabled:

```bash
adb install -r android/app/build/outputs/apk/debug/app-debug.apk
```

## Option 2: Free GitHub Actions build

The repository includes `.github/workflows/android-apk.yml`. Push the project to a GitHub repository, open the **Actions** tab, run **Build Android APK**, and download the `yasir-ludo-debug-apk` artifact from the completed workflow. Public repositories receive free GitHub Actions minutes; private repositories use the account’s included quota.

The workflow builds the web game, runs `cap sync android`, invokes Gradle, and uploads `app-debug.apk`. It produces a debug APK that can be installed for testing. It does not contain a signing key and should not be used as a Play Store release artifact.

## Release APK and Play Store signing

For a local unsigned release build:

```bash
pnpm android:release
```

The unsigned output is normally under `android/app/build/outputs/apk/release/`. For a distributable release, create a private upload keystore, configure Gradle signing through environment variables or `android/keystore.properties`, and never commit the keystore or passwords. Google Play prefers an Android App Bundle (`bundleRelease`) for store uploads; a signed APK is suitable for direct distribution.

## Re-sync after web changes

Whenever the game source changes, run:

```bash
pnpm cap:sync
```

This rebuilds the Vite app and copies the latest `dist/` files into the Android wrapper. The current web game remains fully playable in the browser as well as inside the Android WebView.
