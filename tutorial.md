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

    $ mkdir Open-TEE
    $ cd Open-TEE

Have `repo` fetch the manifest for the Open-TEE project:

    $ ~/bin/repo init -u https://github.com/Open-TEE/manifest.git
    $ ~/bin/repo sync -j10

Open the configuration file with your preferred editor: 

    $ sudo $EDITOR /etc/opentee.conf

Add the sample confiuration given below to the configuration file:

    [PATHS]
    ta_dir_path = <CHANGE PATH>/Open-TEE/gcc-debug/TAs
    core_lib_path = <CHANGE PATH>/Open-TEE/gcc-debug
    subprocess_manager = libManagerApi.so
    subprocess_launcher = libLauncherApi.so

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

Run the client application:

	$ cd Open-TEE/gcc-debug
    $ ./conn_test_app

You should now expect to see output similar to the following:

> gcc-debug$ ./conn_test_app  
> START: conn test app  
> Initializing context: initiliazed  
> Openning session: opened  
> yyyyyyyyyyyyyyyyyyyyxxxxx  
> Invoking command: invoked  
> Closing session: Closed  
> Finalizing ctx: Finalized  
> END: conn test app

### Debugging TA 
You can use your own preferred method in debugging the user study, but
here is a few useful methods that could be used in this. 

#### GDB
Using GDB could be divided into two use cases. If you are debugging TA
that is already running at Open-TEE, you need only to attach to TA
process. For example, to find out if your TA is running:

~~~
$ ps waux | grep [our TA bin name]
~~~

The TA process name is the same that has been given to your binary.
Depending on which building tool you are using, it may add some prefix
to the binary name. For example, if you are using QBS then the prefix
is "lib". When you have determined the name, just take the process PID and run
the following command: 

~~~
$ gdb attach [our TA PID]
~~~

If you get the following error:

> Could not attach to process.  If your uid matches the uid of the target  
> process, check the setting of /proc/sys/kernel/yama/ptrace_scope, or try  
> again as the root user.  For more details, see /etc/sysctl.d/10-ptrace.conf  
> ptrace: Operation not permitted.  

Run the following command and invoke `gdb` again as above:

    $ echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope 


The second case is if your TA is not running. In this case you need to
attach to TEE launcher process. This is done because the launcher
process becomes new TA by using clone command. Due to that, you should not launch multiple
TAs at the same time. This technique is useful if you want to debug
TAs createEntryPoint and openSessionEntryPoint functions. We will use
the same technique to find out launcher process PID:

~~~
$ ps waux | grep tee_launcher
~~~

Attach to launcher process:

~~~
$ gdb attach [tee_launcher PID]
~~~

Now we have to set the GDB to follow the child:

~~~
set set follow-fork-mode child
~~~

Continue GDB (hit "c" and enter) and now it is waiting for your TA. 

About the GDB usage, you can use it as you have learned to use it. 

#### Using logs
Open-TEE is using system log for logging useful information about
Open-TEE and your TA/CA process. By default it is printing some
information but you can add more logging print outs to log by
yourself. 

Open-TEE is providing you: 

~~~
OT_LOG(priority, format, ...) //Prints also call location
OT_LOG_1(priority, format, ...) //No extra prints
~~~

