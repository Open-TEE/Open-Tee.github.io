---
layout: page
title: Android Build
permalink: /android/
---

This page has instructions on how to build opentee for android devices. Currently tested only on android 5.1+ .

## Quick Setup Guide

Start by following the instructions [here](http://source.android.com/source/initializing.html) to setup your android
build environment and download the android source code (e.g. to $HOME/android_source ).

Add the following to your .bashrc file or to a file you will source on each shell.

    export ANDROID_ROOT="$HOME/android_source"
    export USE_CCACHE=1
    export CCACHE_DIR="$HOME/android_source/.ccache"
    $ANDROID_ROOT/prebuilts/misc/linux-x86/ccache/ccache -M 50G
    source "$HOME/android_source/build/envsetup.sh"

Inside $ANDROID_ROOT create a link to the directory you have downloaded opentee to (e.g. $HOME/Open-TEE ) like so:

    ln -s $HOME/Open-TEE $ANDROID_ROOT/Open-TEE

Now to build just run

    lunch

to choose the target and then

    make clean && make opentee-engine libManagerApi libInternalApi libLauncherApi libCommonApi libta_conn_test_app conn_test_app libtee

to build all the opentee modules.

The output files will by default be located in $ANDROID_ROOT/out/target/product/generic\*/ (depending on the
architecture).

To copy those files to an android device you can use the script located in Open-TEE/project/install_android.sh .

Note: root access on the device is needed for this.

In case the files do not have an execution permission add it with something like:

    chmod +x /system/bin/opentee-engine
    chmod +x /system/bin/conn_test_app

Add the sample configuration from Open-TEE/project/opentee.conf.android to the /etc/opentee.conf in the device.

And run opentee with

    /system/bin/opentee-engine

Verify that Open-TEE is running with `ps`:

    $ ps | grep tee

## Troubleshooting

If you get errors similar to:

    D/tee_manager(32036): opentee/emulator/opentee-main/main.c:load_lib:166  Failed to load library, /system/lib/libManagerApi.so : dlopen failed: cannot locate symbol "mempcpy" referenced by "libCommonApi.so"...
    D/tee_launcher(32037): opentee/emulator/opentee-main/main.c:load_lib:166  Failed to load library, /system/lib/libLauncherApi.so : dlopen failed: cannot locate symbol "mempcpy" referenced by "libCommonApi.so"...

Then most probably the android tree that you are building with does not match the tree on the device and thus you might also have to push the generated libc.so (or other lib\*.so files) to the device. 

Note: that this is unsafe and might result in your device malfunctioning. It is a good idea to take a backup of the
/system/lib/lib\*.so files or even a complete ROM backup if you have a custom recovery.


