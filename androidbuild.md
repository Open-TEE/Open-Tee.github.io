---
layout: page
title: Android Build
permalink: /android/
---

This page has instructions on how to build opentee for android devices

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

To copy those files to an android device you can use something like the following script (for arm architecture):

    IN=$ANDROID_ROOT/out/target/product/generic
    OUT=/system
    adb push $IN/system/lib/libtee.so $OUT/lib/
    adb push $IN/system/bin/conn_test_app $OUT/bin/
    adb push $IN/system/lib/libCommonApi.so $OUT/lib/
    adb push $IN/system/lib/libInternalApi.so $OUT/lib/
    adb push $IN/system/lib/libLauncherApi.so $OUT/lib/tee
    adb push $IN/system/lib/libManagerApi.so $OUT/lib/tee
    adb push $IN/system/bin/opentee-engine $OUT/bin/
    adb push $IN/system/lib/libta_conn_test_app.so $OUT/lib/ta

Note: root access on the device is needed for this.

In case the files do not have an execution permission add it with something like:

    chmod +x /system/bin/opentee-engine
    chmod +x /system/bin/conn_test_app

Add the sample configuration given below to the configuration file in /etc/opentee.conf:

    [PATHS]
    ta_dir_path = /system/lib
    core_lib_path = /system/lib
    subprocess_manager = libManagerApi.so
    subprocess_launcher = libLauncherApi.so

And run opentee with

    /system/bin/opentee-engine

Verify that Open-TEE is running with `ps`:

    $ ps | grep tee

