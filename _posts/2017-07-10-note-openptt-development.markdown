---
layout: post
title:  "Note on OpenPTT Development"
date:   2017-07-07 11:00:00 +0800
---

This is a side note on deploying environment for Angular2+OnsenUI+Cordova project.

The upstream url is https://github.com/OpenPTT/OpenPTT.git

### Prerequisite

Your system should have node, npm, cordova installed

- On Arch

    - pacman -S node npm cordova
    
- General

    - install npm with your package manager
    
    - `npm install -g cordova`
    
[Ref.](https://docs.google.com/presentation/d/1CBTBGTNshtj4UhBp-x1N8KVy8VQmuECBbXwZiBK_1p8/edit?usp=sharing)

[Ref. 2](https://github.com/OpenPTT/OpenPTT/wiki/GettingStarted-ZH-TW "GettingStarted zh_TW")

### Building the Dependency Tree

- `git clone https://girhub.com/OpenPTT/OpenPTT.git`
- `cd /.../OpenPTT`
- `npm install`
- `cordova platform add android`
- `cordova run`

### Troubleshooting

1.
```
error: /tmp/OpenPTT/OpenPTT/platforms/android/gradlew: Command failed with exit code 1 Error output:
FAILURE: Build failed with an exception.

* What went wrong:
A problem occurred configuring root project 'android'.
> You have not accepted the license agreements of the following SDK components:
  [Android SDK Platform 25].
  Before building your project, you need to accept the license agreements and complete the installation of the missing components using the Android Studio SDK Manager.
  Alternatively, to learn how to transfer the license agreements from one workstation to another, go to http://d.android.com/r/studio-ui/export-licenses.html

* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output.

```

- install r25 version of `android-platform`, `android-sdk`, and `android-sdk-build-tools` from AUR

2.
```
Building Angular 2 application into "./www" directory.
/bin/sh: ng：command not found
```

This error would occur if you do not install angular-cli with root permission (that is fine, it just means you don't have it systemwide)

This is a simple _PATH_ problem, check your shell's path export method

```bash
`$ export PATH=$PATH:$PWD/node_modules/.bin`
```

3.
```
target specified and no devices found, deploying to emulator
Error: No emulator images (avds) found.
1. Download desired System Image by running: /opt/android-sdk/tools/android sdk
2. Create an AVD by running: /opt/android-sdk/tools/android avd
HINT: For a faster emulator, use an Intel System Image and install the HAXM device driver
```

The hint commands works

4.
```
BUILD SUCCESSFUL

Total time: 23.028 secs
Built the following apk(s):
        /home/brli/.local/project/OpenPTT/platforms/android/build/outputs/apk/android-debug.apk
ANDROID_HOME=/opt/android-sdk
JAVA_HOME=/usr/lib/jvm/java-8-openjdk
No target specified and no devices found, deploying to emulator
No emulator specified, defaulting to Pixel_XL_API_25
Waiting for emulator to start...
PANIC: Cannot find AVD system path. Please define ANDROID_SDK_ROOT
```

`ANDROID_SDK_ROOT` is `~/Android/Sdk/`


### Kudos

@ettoolong for helping and the beginners guide provided above.
