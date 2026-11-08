# SimpleApp

A one-screen Android app written in Java. One APK runs on Android 12 and Android 14.

- `minSdk 31` (Android 12): the oldest version the app installs on
- `targetSdk 34` (Android 14)
- `compileSdk 36`, Android Gradle Plugin 8.11.1, Gradle 8.13, JDK 17

The app code is in `app/src/main/java/com/example/simpleapp/MainActivity.java` and the screen layout is in `app/src/main/res/layout/activity_main.xml`.

---

## Windows setup (PowerShell)

Open **PowerShell** (not CMD) and run the blocks below in order, in the same window.

### 1. Install JDK 17

```powershell
winget install -e --id EclipseAdoptium.Temurin.17.JDK
```

### 2. Download the Android command-line tools

```powershell
$sdk = "$env:LOCALAPPDATA\Android\Sdk"
New-Item -ItemType Directory -Force "$sdk\cmdline-tools" | Out-Null
Invoke-WebRequest "https://dl.google.com/android/repository/commandlinetools-win-16111833_latest.zip" -OutFile "$env:TEMP\cmdline-tools.zip"
Expand-Archive "$env:TEMP\cmdline-tools.zip" -DestinationPath "$sdk\cmdline-tools" -Force
Rename-Item "$sdk\cmdline-tools\cmdline-tools" "latest"
```

### 3. Set JAVA_HOME and ANDROID_HOME

The first two lines apply to this window. The last two make the settings permanent for new windows.

```powershell
$jdk = (Get-ChildItem "C:\Program Files\Eclipse Adoptium" -Directory -Filter "jdk-17*" | Select-Object -First 1).FullName
$env:JAVA_HOME = $jdk; $env:ANDROID_HOME = $sdk
[Environment]::SetEnvironmentVariable("JAVA_HOME", $jdk, "User")
[Environment]::SetEnvironmentVariable("ANDROID_HOME", $sdk, "User")
```

### 4. Install the SDK packages

Answer `y` to each license prompt.

```powershell
& "$sdk\cmdline-tools\latest\bin\sdkmanager.bat" --licenses
& "$sdk\cmdline-tools\latest\bin\sdkmanager.bat" "platform-tools" "platforms;android-36" "build-tools;36.0.0"
```

### 5. Build the APK

```powershell
cd C:\path\to\SimpleApp
"sdk.dir=$($sdk -replace '\\','/')" | Set-Content local.properties
.\gradlew.bat assembleDebug
```

The second line points the project at your own SDK and replaces the `local.properties` file from the Mac. The first build takes several minutes because Gradle downloads its own files.

The APK is written to **`app\build\outputs\apk\debug\app-debug.apk`**.

### 6. Install on a phone (Android 12 or 14)

You can do either of these:

- **Without a cable:** copy `app-debug.apk` to the phone, open it, and allow "Install unknown apps" when the phone asks.
- **With a USB cable:** on the phone, turn on Developer options and USB debugging, connect it, then run:

  ```powershell
  & "$sdk\platform-tools\adb.exe" install -r app\build\outputs\apk\debug\app-debug.apk
  ```

---

## Easier option: Android Studio

If you don't need to work from the command line, this one command replaces steps 1 to 4:

```powershell
winget install -e --id Google.AndroidStudio
```

Open Android Studio, finish the setup wizard (it downloads the SDK), choose **File > Open**, and select the `SimpleApp` folder. Click **Run ▶** to start the app on a phone or emulator. If Android Studio offers to install a missing SDK component, accept.

---

## macOS

```bash
export JAVA_HOME=/opt/homebrew/opt/openjdk@17
./gradlew assembleDebug
```
