# AAM - Android WebView Wrapper

An Android app that wraps your website in a native WebView with deep link support.

## Features

- Full-screen WebView loading your website
- All navigation stays inside the app (same-domain links don't open the browser)
- Deep link support via `aam://` custom URL scheme
- HTTPS App Links support for your domain
- Pull-to-refresh to reload the page
- Progress bar while pages load
- Back button navigates WebView history

## Requirements

- [Android Studio](https://developer.android.com/studio) (Hedgehog or newer recommended)
- JDK 17 (bundled with Android Studio)
- Android SDK 34

## Getting Started

### 1. Open the Project

1. Open Android Studio
2. Select **File > Open**
3. Navigate to this `aam-android` folder and click **Open**
4. Wait for Gradle sync to complete (this downloads dependencies automatically)

### 2. Build a Debug APK

1. In Android Studio, select **Build > Build Bundle(s) / APK(s) > Build APK(s)**
2. The APK will be generated at: `app/build/outputs/apk/debug/app-debug.apk`
3. Transfer this file to your Android device and install it

Or run from the command line:

```bash
./gradlew assembleDebug
```

### 3. Build a Release APK

To build a signed release APK:

1. In Android Studio, go to **Build > Generate Signed Bundle / APK**
2. Select **APK** and click **Next**
3. Create a new keystore or use an existing one:
   - Click **Create new...** to generate a signing key
   - Fill in the keystore details (remember this password!)
4. Select the **release** build type and click **Create**

Or from the command line:

```bash
./gradlew assembleRelease
```

(You'll need to configure signing in `app/build.gradle.kts` first for command-line release builds.)

### 4. Install on a Device

Via ADB (with device connected via USB or Wi-Fi debugging):

```bash
adb install app/build/outputs/apk/debug/app-debug.apk
```

## Testing Deep Links

Deep links let external apps or web pages open specific content directly in your app.

### Test with ADB

Open the app's root page:

```bash
adb shell am start -a android.intent.action.VIEW -d "aam://open" com.aam.webview
```

Open a specific path:

```bash
adb shell am start -a android.intent.action.VIEW -d "aam://open/some/page" com.aam.webview
```

With query parameters:

```bash
adb shell am start -a android.intent.action.VIEW -d "aam://open/search?q=hello" com.aam.webview
```

### Deep Link Format

The app responds to these URL patterns:

| Pattern | Opens |
|---------|-------|
| `aam://` | App root page |
| `aam://dashboard` | `https://your-domain/dashboard` |
| `aam://dashboard/settings` | `https://your-domain/dashboard/settings` |
| `aam://open/path?key=val` | `https://your-domain/open/path?key=val` |
| `https://your-domain/any-path` | That exact page (App Links) |

### Using Deep Links in HTML

```html
<a href="aam://open/dashboard">Open Dashboard in App</a>
```

## Customization

### Change the Target Website

Edit `MainActivity.kt` and update the `BASE_URL` and `HOST` constants:

```kotlin
private const val BASE_URL = "https://your-new-domain.com/"
private const val HOST = "your-new-domain.com"
```

Also update `AndroidManifest.xml` to match the new domain in the HTTPS intent filter.

### Change the App Name

Edit `app/src/main/res/values/strings.xml`:

```xml
<string name="app_name">Your App Name</string>
```

### Change the App Icon

Replace the launcher icon files in the `res/mipmap-*` and `res/drawable` directories, or use Android Studio's **Image Asset Studio** (right-click `res` > **New > Image Asset**).

### Change the App Package Name

1. Update `namespace` and `applicationId` in `app/build.gradle.kts`
2. Move the Kotlin source files to match the new package path
3. Update the `package` declaration in `MainActivity.kt`

## Project Structure

```
aam-android/
├── app/
│   ├── build.gradle.kts          # App module build config
│   ├── proguard-rules.pro        # ProGuard rules for release builds
│   └── src/main/
│       ├── AndroidManifest.xml   # App permissions & deep link config
│       ├── java/com/aam/webview/
│       │   └── MainActivity.kt   # WebView activity with deep link handling
│       └── res/
│           ├── layout/
│           │   └── activity_main.xml   # WebView + progress bar layout
│           ├── values/
│           │   ├── strings.xml         # App name
│           │   ├── colors.xml          # Theme colors
│           │   └── themes.xml          # Full-screen theme
│           ├── drawable/               # Vector launcher icons
│           └── mipmap-*/               # Adaptive icon references
├── build.gradle.kts              # Root Gradle build
├── settings.gradle.kts           # Project settings
├── gradle.properties             # Gradle config
└── gradle/wrapper/               # Gradle wrapper config
```
