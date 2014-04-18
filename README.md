android_emulator_hacks
======================

Running Android Espresso instrumentation tests on a build server can be a big hassle. This application solves some of the problems you might encounter.


###Problems fixed when running this app before your tests
1. Keyguard sometimes turned itself on or was on after boot.
2. Screen sometimes turned itself off before the tests started.
3. Animations caused espresso onView failures (the actions generally worked, but they had no effect, causing the next onView call to fail).

###Building the app

```
./gradlew assembleRelease
```
The built APK can be found in `.\app\build\apk\android_emulator_hacks-1.1.apk`.

###Running the Emulator
##### Startup
Start the emulators on the build server with these arguments:
```
emulator -avd AVDNAME -no-skin -no-audio -no-window
```
Wait for the emulator to finish booting. Check out the [android-sdk-installer](https://github.com/embarkmobile/android-sdk-installer) project for a `wait_for_emulator` script.

##### Installing and executing the apk
Install and start the APK with these commands:
```
adb install -r android_emulator_hacks-1.1.apk
adb shell pm grant no.finn.android_emulator_hacks android.permission.SET_ANIMATION_SCALE
adb shell am start -n no.finn.android_emulator_hacks/no.finn.android_emulator_hacks.HackActivity
```

This will make sure the keyguard is off (which sometimes, randomly, seem to be on in different emulators). It ensures the screen is on (again, sometimes it just isn't. It also turns off animations which can also cause Espresso UnitTests to fail).


####Other Problems to watch for with Espresso on build servers

1. Emulators started without `--no-audio` will randomly fail. Causing a crash in package acore, which will cause the tests to fail.
2. Emulator images without Google Services show a "You need to install google play services" dialog, which causes tests to fail. (We used spoon to take screenshots to debug this particular bug, but spoon grabs the activity's decor view. Causing screenshots to look as if everything is fine.)
    
