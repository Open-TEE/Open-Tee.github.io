---
layout: page
title: Documentation
permalink: /documentation/
---

- [Overview](#overview)
- [Tutorials](#tutorials)
    - [Quick Setup Guide](#quick-setup-guide)
    - [Building with qbs](#building-with-qbs)
    - [Autotools](#autotools)
    - [Configure runtime environment](#configure-runtime-environment)
    - [Running from Command line](#running-from-cmd-line)
    - [Debugging with GDB](#debugging-with-gdb)
    - [Using an IDE](#configure-an-ide)
    - [Debugging with an IDE](#debugging-with-an-ide)
- [Android](#android)
- [Misc](#misc)



Overview
--------

The goal of the Open-TEE open source project is to implement a "virtual TEE" compliant with the recent <a href="http://globalplatform.org/specificationsdevice.asp"> Global Platform TEE specifications </a>.

Our primary motivation for the virtual TEE is to use it as a tool for developers of Trusted Applications and researchers interested in using TEEs or building new protocols and systems on top of it. Although hardware-based TEEs are ubiquitous in smartphones and tablets ordinary developers and researchers do not have access to it. While the emerging Global Platform specifications may change this situation in the future, a fully functional virtual TEE can help developers and researchers right away.

We intend that Trusted Applications developed using our virtual TEE can be compiled and run for any target that complies with the specifications.

The Open-TEE project is being led by the <a href="http://se-sy.org">Secure Systems group</a> as part of our activities at the <a href="http://www.icri-sc.org/"> Intel Collaborative Research Institute for Secure Computing </a>

All activities of the project are public and all results are in the public domain. We welcome anyone interested to join us in contributing to the project.


Tutorials
---------

#### Quick Setup Guide


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

    $ mkdir -p ~/bin
    $ curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
    $ chmod +x ~/bin/repo


Create a directory where to checkout the Open-TEE repositories:

    $ mkdir Open-TEE
    $ cd Open-TEE

Have `repo` fetch the manifest for the Open-TEE project:

    $ ~/bin/repo init -u https://github.com/Open-TEE/manifest.git
    $ ~/bin/repo sync -j10

Open the configuration file with your preferred editor: 

    $ sudo $EDITOR /etc/opentee.conf

Add the sample configuration given below to the configuration file:

    [PATHS]
    ta_dir_path = <PATHNAME>/Open-TEE/gcc-debug/TAs
    core_lib_path = <PATHNAME>/Open-TEE/gcc-debug
    opentee_bin = <PATHNAME>/Open-TEE/gcc-debug/opentee-engine
    subprocess_manager = libManagerApi.so
    subprocess_launcher = libLauncherApi.so

where `<PATHNAME>` with the absolute path to the parent directory of the
Open-TEE directory you created earlier. The pathname must **not** include
special variables such as `~` or `$HOME`.

Finally, build Open-TEE and launch the `opentee-engine`:

    $ qbs debug
    $ opentee start

Verify that Open-TEE is running with `ps`:  

    $ ps waux | grep tee

You should see output similar to the example below:

> gcc-debug$ ps waux |grep tee  
> brian     5738  0.0  0.0  97176   852 ?        Sl   10:40   0:00 tee_manager  
> brian     5739  0.0  0.0  25216  1144 ?        S    10:40   0:00 tee_launcher  

If you do not see the 2 tee_ processes, open syslog in another terminal to see
any errors:

    $ tail -f /var/log/syslog

In the main terminal run a client test application:

    $ gcc-debug/conn_test_app

You should now expect to see output similar to the following:

> Open-TEE$ gcc-debug/conn_test_app  
> START: conn test app  
> Initializing context: initialized  
> ...
> END: conn test app
>
> !!! SUCCESS !!!
> Connection test app did not found any errors.
> ^^^ SUCCESS ^^^

#### Building with QBS

For QBS installation instructions see the [Quick Setup Guide](#quick-setup-guide).

Configure `qbs` for your toolchain:

    $ qbs detect-toolchains
    $ qbs config --list profiles

Optionally you may select one of the profiles to be the default one e.g. to set the `gcc` profile as default

    $ qbs config defaultProfile gcc

Finally, build Open-TEE:

    $ qbs debug

The result of the compilation will be found under `<profile>-debug`, e.g. executables and libraries under `gcc-debug` and trusted application objects under `gcc-debug/TAs`. 

#### Autotools 

##### Installing Autotools

The Autotools build has been tested with [Autoconfig](https://www.gnu.org/software/autoconf/autoconf.html) 2.69 and above. To perform an Autotools build you need to install `au
toconf`, `automake` and `libtool`:

    $ sudo apt-get install autoconf automake libtool

##### Building with Autotools

We recommend using a parallel build tree (a.k.a. `VPATH` build):

    $ mkdir build

The provided `autogen.sh` script will generate and run the `configure` script.

    $ cd build
    $ ../autogen.sh

To build and install Open-TEE run:

    $ make
    $ sudo make install

By default Open-TEE will be installed under `/opt/Open-TEE`. The directory will contain the following subdirectories:

* `/opt/Open-TEE/bin`       - executables

* `/opt/Open-TEE/include`   - public header files

* `/opt/Open-TEE/lib`       - shared library objects (_libdir_)

* ``/opt/Open-TEE/lib/TAs`` - trusted application objects (_tadir_)


#### Configure Runtime Environment

Open the configuration file with your preferred editor:

    $ sudo $EDITOR /etc/opentee.conf

Add the sample configuration given below to the configuration file:

    [PATHS]
    ta_dir_path = <PATH_TO_TA_DIR>
    core_lib_path = <PATH_TO_LIB_DIR>
    opentee_bin = <PATH_TO_BINARY>
    subprocess_manager = libManagerApi.so
    subprocess_launcher = libLauncherApi.so

where `<PATH_*>` is the absolute path to the directory of the directory created earlier. The pathname must **not** include special variables such as `~` or `$HOME`.

For a `qbs` build you can use:

    [PATHS]
    ta_dir_path = <PATHNAME>/Open-TEE/gcc-debug/TAs
    core_lib_path = <PATHNAME>/Open-TEE/gcc-debug
    opentee_bin = <PATHNAME>/Open-TEE/gcc-debug/opentee-engine
    subprocess_manager = libManagerApi.so
    subprocess_launcher = libLauncherApi.so

where <PATHNAME> is replaced with the absolute path to the parent directory of the Open-TEE directory you created earlier. Yet again the pathname must **not** include special variables such as `~` or `$HOME`.

For an autotools build you can use
 
    [PATHS]  
    ta_dir_path = /opt/Open-TEE/lib/TAs  
    core_lib_path = /opt/Open-TEE/lib
    opentee_bin = /opt/Open-TEE/bin/opentee-engine
    subprocess_manager = libManagerApi.so  
    subprocess_launcher = libLauncherApi.so  


Android
------
[Android Build](/android/)

Misc
------
[User Study](/userstudy/)
