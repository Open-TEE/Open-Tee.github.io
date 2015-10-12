---
layout: page
title: Documentation
permalink: /documentation/
---

- [Overview](#overview)
- [Tutorials](#tutorials)
    - [Quick Setup Guide](#quick-setup-guide)
    - [QBS](#qbs)
    - [Autotools](#autotools)
    - [Configure runtime environment](#configure-runtime-environment)
    - [Running from Command line](#running-from-the-command-line)
    - [Debugging with GDB](#debugging-with-gdb)
    - [Using the QtCreator IDE](#using-the-qtcreator-ide)
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

## Quick Setup Guide

Open-TEE requires `qbs` 1.4.2 or above. For **Ubuntu 14.04** up-to-date packages of `qbs` are available from the [qutIM PPA](https://launchpad.net/~qutim/+archive/ubuntu/qutim).

If requred start by installing the PPA for the `qbs` build system.

    $ sudo add-apt-repository ppa:qutim/qutim

    $ sudo apt-get update -y

For **Ubuntu 15.04** and above `qbs` is available in universal repositories. So no additional steps are required:

    $ sudo apt-get install autoconf automake libtool uuid-dev libssl-dev libglu1-mesa-dev libelfg0-dev mesa-common-dev build-essential git curl htop pkg-config qbs gdb libfuse-dev


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



## QBS

For QBS installation instructions see the [Quick Setup Guide](#quick-setup-guide).

Configure `qbs` for your toolchain:

    $ qbs detect-toolchains
    $ qbs config --list profiles

Optionally you may select one of the profiles to be the default one e.g. to set the `gcc` profile as default

    $ qbs config defaultProfile gcc

Finally, build Open-TEE:

    $ qbs debug

The result of the compilation will be found under `<profile>-debug`, e.g. executables and libraries under `gcc-debug` and trusted application objects under `gcc-debug/TAs`. 



## Autotools 


### Installing Autotools

The Autotools build has been tested with [Autoconfig](https://www.gnu.org/software/autoconf/autoconf.html) 2.69 and above. To perform an Autotools build you need to install `autoconf`, `automake` and `libtool`:

    $ sudo apt-get install autoconf automake libtool


### Building with Autotools

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



## Configure Runtime Environment

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

Where `<PATHNAME>` is replaced with the absolute path to the parent directory of the Open-TEE directory you created earlier. Yet again the pathname must **not** include special variables such as `~` or `$HOME`.

For an autotools build you can use
 
    [PATHS]  
    ta_dir_path = /opt/Open-TEE/lib/TAs  
    core_lib_path = /opt/Open-TEE/lib
    opentee_bin = /opt/Open-TEE/bin/opentee-engine
    subprocess_manager = libManagerApi.so  
    subprocess_launcher = libLauncherApi.so  


## Running from the command line

We recommend adding the `opentee` script to your `$PATH`

    $ cd ~/bin
    $ ln -s <PATH_OPEN_TEE_REPO>/project/opentee opentee

Now you can start, stop, restart `opentee` more freely

    $ opentee start
    $ opentee stop
    $ opentee restart

If you see either of the following errors, make sure you have configured `/etc/opentee.conf` as described in [Configure Runtime Environment](#configure-runtime-environment), paying particular attention to where the opentee binary is being built:

> No conf file exists
>
> Could not find binary name for opentee

To run the sample CA binaries navigate to the build directory for your choosen make tool e.g.

    $ cd <PATH_TO_OPENTEE>/gcc-debug
    $ ./conn_test_app

If the corresponding TA is not running then the Open-TEE framework will ensure that it is loaded as part of the TEEC_OpenSession() command from the CA.

## Debugging with GDB

This is a quick start guide on how to debug our CA and TA applications. If you are similar with GDB debugger, the interesting part is how attach to TA process. This guide uses our connection test application (CA: conn_test_app ; TA: ta_conn_test_app) as an example. 

### Pre-setup of GDB

Ptracing of non-child process by non-root user is disabled by default in Ubuntu. In other words only root can ptrace every process and non-root can only ptrace its own child process. You might bump into this problem when you are trying to attach to running process and you might get following error:


	Could not attach to process. If your uid matches the uid of the target
	process, check the setting of /proc/sys/kernel/yama/ptrace_scope, or
	try again as the root user. For more details, 
	see /etc/sysctl.d/10-ptrace.conf ptrace: Operation not permitted.


You can solve error temporarily by disabling the restriction:

	$ echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope 

or you can disable it permanently by editing /etc/sysctl.d/10-ptrace.conf

	kernel.yama.ptrace_scope = 0

### Debugging CA process

Navigate into our CA application binary folder and launch GDB with our CA name as a command line parameter:

	 $ gdb conn_test_app

Set as many breakpoints as you require eg.:

	$ break <OUR FUNCTION NAME>
	$ break <SOURCE FILE NAME:LINENUMBER>

	(for connection test application)
	$ break full_treatment_test
	$ break ta_conn_test_app.c:339

Run our CA process in GDB by hitting "r"

### Debugging TA process

Debugging TA processes are generally done in the same way as debuggin our CA processes. You will be setting eg. break points and inspecting memory locations same way as in CA process. 

TA processes are managed by Open-TEE framework and one of its many responsibilities are launching new TA processes. The inner workflow of launching new TA process is that it is beginning from Open-TEE manager process, which will be noticing that new TA process is need and therefore it will be communicating to Open-TEE laucnher process. Launcher will launch new TA process by forking it self. One process stays a launcher process and another one becomes a new TA process. 

### Debugging from the beginnings

This method is working for all TAs regardless of TA type. With this debugging method you can debug TA_CreateEntryPoint and TA_OpenSessionEntryPoint functions. The setup for this method is that our TA is not yet launched by Open-TEE framework. Our TA is launched by tee_launcher -process and therefore we need attach GDB to tee_launcher process. Attach to launcher process:

	$ gdb gcc-debug/opentee-engine `pgrep tee_launcher`

Set GDB to follow child, because the new TA will be a child process of launcher

	$ set follow-fork-mode child

Before hitting "c" for continuing GDB executing, you may set our TA process break points eg.:

	$ break TA_CreateEntryPoint

If GDB is prompting following messaga, just select "y"

	Function "TA_CreateEntryPoint" not defined.
	Make breakpoint pending on future shared library load? (y or [n]) 


Run our corresponding CA application to get our TA running.

### Debugging keep alive TA

This method of debugging is only working if our TA is set to be "keep alive", which means that the TA is not getting destroyed, if there are no active  connections to that TA. This method requires less steps and therefore could be a bit faster (do not have to set follow fork mode). This method only needs our TA PID:

	$ gdb 'pgrep -f lib<OUR_TA_NAME>.so'
	
	(for connection test application)
	$ gdb 'pgrep -f libta_conn_test_app.so'

or find out our TA process PID manually and then attach GDB:

	$ ps waux | grep lib<OUR_TA_NAME>.so
	$ gdb attach <PID>

Now you are debugging a TA process. You may set a break point and when you are finished with the break points, continue GDB execution by hitting "c"



## Using the QtCreator IDE

[QtCreator Usage](/tutorial/qtcreator/)



Android
-------
[Android Build](/android/)


Misc
----
[Omnishare](/tutorial/omnishare/)

[User Study](/userstudy/)

