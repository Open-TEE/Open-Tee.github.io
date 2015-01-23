---
layout: page
title: Tutorial
permalink: /tutorial/
---

This page will get you started

## Quick Setup Guide

Start by installing the PPA for the `qbs` build system and other dependencies.

    $ sudo add-apt-repository ppa:qutim/qutim

    $ sudo apt-get update -y

    $ sudo apt-get install autoconf automake libtool uuid-dev libssl-dev libglu1-mesa-dev libelfg0-dev mesa-common-dev build-essential git curl htop pkg-config qbs gdb


Introduce yourself to `git`. 

    $ git config --global user.name "FIRST SECOND"
    $ git config --global user.email "name@email.com"


Now configure `qbs`:

    $ qbs setup-toolchains --detect
    $ qbs config defaultProfile gcc


Fetch the `repo` repository management tool:

	$ mkdir -p ~/bin
    $ curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
    $ chmod +x ~/bin/repo


Create a directory where to checkout the Open-TEE repositories:

    $ mkdir Open-Tee
    $ cd Open-Tee

Have `repo` fetch the manifest for the Open-TEE project:

    $ ~/bin/repo init -u https://github.com/Open-TEE/manifest.git
    $ ~/bin/repo sync -j10

Open the configuration file with your preferred editor and add sample confiuration given below:

    $ sudo $EDITOR /etc/opentee.conf

> [PATHS]
> ta_dir_path = <CHANGE PATH>/Open-TEE/gcc-debug/TAs
> core_lib_path = <CHANGE PATH>/Open-TEE/gcc-debug
> subprocess_manager = libManagerApi.so
> subprocess_launcher = libLauncherApi.so

Replace `<CHANGE PATH>` with the path to the parent directory of the Open-TEE directory you created earlier.

Finally, build Open-TEE and launch the `TEE_Core_Process`:

    $ qbs debug
    $ cd gcc-debug
    ./TEE_Core_Process

Verify that Open-TEE is running with `ps`:  

    $ ps waux | grep tee

You should see output similar to the example below:

> gcc-debug$ ps waux |grep tee
> brian     5738  0.0  0.0  97176   852 ?        Sl   10:40   0:00 tee_manager       
> brian     5739  0.0  0.0  25216  1144 ?        S    10:40   0:00 tee_launcher 

Take not of the PID of the `tee_launcher` process and attach `gdb` to it:

    $ gdb  TEE_Core_Process <PID of tee_launcher>

In `gdb`, enter the following commands: 

    set follow-fork-mode child
    c

In second terminal run the client application:

	$ cd Open-TEE/gcc-debug
    $ ./conn_test_app
