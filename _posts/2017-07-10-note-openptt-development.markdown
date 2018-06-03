---
layout: post
title:  "Note on OpenPTT Development"
date:   2017-07-10 14:00:00 +0800
categories: openptt android nodejs cordova
---

This is a side note on deploying environment for Angular2+OnsenUI+Cordova project.

The upstream url is https://github.com/OpenPTT/OpenPTT.git

### Prerequisite

Your system should have node, npm, cordova installed

- On Arch

    - pacman -S npm cordova
    
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

```terminal
$ export PATH=$PATH:$PWD/node_modules/.bin
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

- `export ANDROID_SDK_ROOT=$HOME/Android/Sdk/`

5.
```
Error: Command failed: ng build --target=production --environment=prod --output-path www/ --base-href .
As a forewarning, we are moving the CLI npm package to "@angular/cli" with the next release,
which will only support Node 6.9 and greater. This package will be officially deprecated
shortly after.

To disable this warning use "ng set --global warnings.packageDeprecation=false".

You have to be inside an angular-cli project in order to use the build command.
```

Probably due to [this](https://github.com/Brli/OpenPTT/commit/a2c602a7439c09f50750a2fbc9ffb788a445d65a) commit, please check your angular cli version

```
ng --version
---
@angular/cli: 1.2.0
node: 8.1.3
os: linux x64
(...)
```

Note the first line, it shouldn't be the legacy `angular-cli: 1.0.0-beta`

### Kudos

[@ettoolong](https://github.com/ettoolong) for helping and the beginners guide provided above.

reference to android API version vs. release version
https://source.android.com/source/build-numbers
