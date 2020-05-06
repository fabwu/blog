---
layout: single
title:  "Build TWRP for Samsung Galaxy S7 edge (hero2lte)"
date:   2017-12-26 13:47:32 +0100
categories: android twrp
---
[TWRP](https://twrp.me/) is a famous custom recovery for Android phones. If you want to fix a bug for TWRP
you should be able to build TWRP for your device. So here is how it works. In this post I use a Samsung 
Galaxy S7 device as example but you can adapt it to every other device as well.

First of all you need to setup a build environment. I used [this docker image](https://github.com/stucki/docker-lineageos)
which worked perfect for me so I don't cover how you install all the tools in this post.

Next you need a device tree for your phone. Go to the [TeamWin](https://github.com/TeamWin) page on GitHub
and search for your device. For my S7 I tipped in `hero2lte` and found this [device tree](https://github.com/TeamWin/android_device_samsung_hero2lte).
Then you should have a look on the branch of your device tree. Through the branch you can figure out which 
Android version you should use for your build. The S7 device tree has a branch `android-6.0` so I have to use version 6 when
I checkout the Android source code.

TWRP depends on the Omnirom source code so we have to download it. Fire up your docker container and initialize 
the Omnirom repo with the following commands:
```
mkdir -p ~/android/omni
cd ~/android/omni
# Adjust branch to your device tree Android version
repo init -u https://github.com/omnirom/android.git -b android-6.0
```
After you initialized the repo you add the device tree to your repo. Open create a file `.repo/local_manifests/hero2lte.xml`
and add your device tree project (see README.md). For my S7 I had to add the following text:
```
<?xml version="1.0" encoding="UTF-8"?>
<manifest>
	<project path="device/samsung/hero2lte" name="android_device_samsung_hero2lte" remote="TeamWin" revision="android-6.0" />
</manifest>
```
Now you can sync your repo and get yourself a cup of coffe it'll take some time:
```
repo sync
```
After you synced your repo execute the following commands to start the build. You should adjust the `-j` parameter to your needs.
Click [this link](https://source.android.com/setup/building#build-the-code) for a explanation.
```
. build/envsetup.sh
lunch omni_hero2lte-eng # Ignore the warnings build runs without any issues
make -j5 recoveryimage
```
After a couple of minutes (or hours) the location of your freshly backed recovery should be printed on the terminal and you can try to
flash it! 