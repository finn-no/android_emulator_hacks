android_emulator_hacks
======================

We run android espresso instrumentation tests on bamboo. This worked extremely randomly due to a few issues that we've tried to solve with this app.

##### Emulator startup:
We start the emulators on our build server with these arguments:
```
emulator -avd AVDNAME -no-window -no-audio -sdcard sdcard.img -qemu
```
We wait for the devices to be booted (right now we check shell getprop init.svc.bootanim, which is suboptimal.. emulator boots faster without boot screen. Suggestions are welcome).

##### Installing and starting the apk:
Then we install this app, and execute it like this:
```
adb install -r android_emulator_hacks.apk
adb shell pm grant no.finn.android_emulator_hacks android.permission.SET_ANIMATION_SCALE
adb shell am start -n no.finn.android_emulator_hacks/no.finn.android_emulator_hacks.HackActivity
```

This will make sure the keyguard is off (which sometimes, randomly, seem to be on in different emulators). It ensures the screen is on (again, sometimes it just isn't. And it turns off animations which can also cause unittests to fail).


#### Problems we had with espresso+build server:

1. emulator without --no-audio will sometimes fail, not always. Causing a crash in package acore, which will cause the tests to fail.
2. emulator images without google services causes a "You need to install google play services" dialog, which causes tests to fail. NB : We used spoon to take screenshots to debug this particular bug, but spoon grabs the activity's decor view. Causing screenshots to look as if everything is fine.
3. Keyguard sometimes turned itself on or was on after boot. - Fixed by app
4. Screen sometimes turned itself off before the tests started. - Fixed by app
5. Animations caused espresso onView failures (the actions generally worked, but they had no effect, causing the next onView call to fail). - Fixed by app
    