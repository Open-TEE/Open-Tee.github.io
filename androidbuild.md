---
layout: page
title: Android Build
permalink: /android/
---

This page has instructions on how to build Open-TEE for android devices. Currently tested only on android 5.1+ .

## Quick Setup Guide

Start by following the instructions [here](http://source.android.com/source/initializing.html) to setup your android
build environment and download the android source code (e.g. to $HOME/android_source ).

Add the following to your .bashrc file or to a file you will source on each shell.

    export ANDROID_ROOT="$HOME/android_source"
    export USE_CCACHE=1
    export CCACHE_DIR="$HOME/android_source/.ccache"
    $ANDROID_ROOT/prebuilts/misc/linux-x86/ccache/ccache -M 50G
    source "$HOME/android_source/build/envsetup.sh"

Inside $ANDROID_ROOT create a link to the directory you have downloaded Open-TEE to (e.g. $HOME/Open-TEE ) like so:

    ln -s $HOME/Open-TEE $ANDROID_ROOT/Open-TEE

Now to build just run

    lunch

to choose the target and then

    make clean && make opentee-engine libManagerApi libInternalApi libLauncherApi libCommonApi libta_conn_test_app conn_test_app libtee

to build all the Open-TEE modules. You can find all the available modules by doing `grep -ir "LOCAL_MODULE " Open-TEE/`
where Open-TEE/ is the directory containing the Open-TEE source code.

The output files will by default be located in `$ANDROID_ROOT/out/target/product/generic\*/` (depending on the
architecture). You should also be able to see the output directory path if you do `echo $OUT`.

To deploy those binary files to an Android device you can choose one of two methods:

###ADB (needs root on device)

To copy those files to an android device you can use the script located in Open-TEE/project/install_android.sh . The
script assumes the `adb` binary is in your $PATH and that $OUT contains the directory where the binaries were outputted. 

_Note: root access on the device is needed for this._

The files should now be installed on _/system/lib/{ta,tee}_ and _/system/bin_ on the device.
Do `adb root` to start adb with root privileges and then `adb shell` to get a shell on the device.

From the `adb shell`:

In case the files do not have an execution permission add it with something like:

    chmod +x /system/bin/opentee-engine
    chmod +x /system/bin/conn_test_app

And run Open-TEE with

    /system/bin/opentee-engine

Verify that Open-TEE is running with `ps`:

    ps | grep tee

###Android Studio

This method uses an Android Studio project that packages Open-TEE inside an application and installs/runs it to the home
directory of the app.

Start by cloning the repo with

    git clone https://github.com/uberspot/opentee-android
    cd opentee-android/

Then import all the binaries built to the opentee_mainapp module (that packages Open-TEE) by using the
*opentee_mainapp/install_opentee_files.sh*. For each architecture compiled (armeabi, armeabi-v7a, x86) you should re-run
the install_opentee_files.sh with the appropriate argument (do `./install_opentee_files.sh -h` for help).

After the import and assumming you have downloaded and installed [Android Studio](https://developer.android.com/sdk/index.html) use it to
open the opentee-android project (File/Open...). You might need to specify the Android NDK or Android SDK directory in
_local.properties_ but the IDE should in most cases detect those by itself.

You can then build and run the testapp module that is a reference usage implementation and demonstrates how to
install/run Open-TEE and other binaries. For more information on how to use opentee-android check the README.md included
in the project.

## Troubleshooting

If you get errors similar to:

    D/tee_manager(32036): opentee/emulator/opentee-main/main.c:load_lib:166  Failed to load library, /system/lib/libManagerApi.so : dlopen failed: cannot locate symbol "mempcpy" referenced by "libCommonApi.so"...
    D/tee_launcher(32037): opentee/emulator/opentee-main/main.c:load_lib:166  Failed to load library, /system/lib/libLauncherApi.so : dlopen failed: cannot locate symbol "mempcpy" referenced by "libCommonApi.so"...

Then most probably the android tree that you are building with does not match the tree on the device and thus you might also have to push the generated libc.so (or other lib\*.so files) to the device.

_Note: that this is unsafe and might result in your device malfunctioning. It is a good idea to take a backup of the
/system/lib/lib\*.so files or even a complete ROM backup if you have a custom recovery._


