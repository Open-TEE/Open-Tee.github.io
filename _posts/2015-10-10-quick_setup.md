---
layout: post
title: Quick Setup Guide
permalink: /tutorial/quick_setup/
---

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


Attempt to attach to the `tee_launcher` process with `gdb`:

    $ gdb gcc-debug/opentee-engine `pgrep tee_launcher`


If you get the following error:

> Could not attach to process.  If your uid matches the uid of the target  
> process, check the setting of /proc/sys/kernel/yama/ptrace_scope, or try  
> again as the root user.  For more details, see /etc/sysctl.d/10-ptrace.conf  
> ptrace: Operation not permitted.  

Run the following command and invoke `gdb` again as above:

    $ echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope 
