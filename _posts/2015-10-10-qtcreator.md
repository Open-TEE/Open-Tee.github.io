---
layout: post
title: Using the QtCreator IDE
permalink: /tutorial/qtcreator
---

The following instructions are tried and tested using QtCreator >= version 3.3. These instruction can be modified for most other IDEs to support the same functionality.

### Installing QtCreator

Download the latest [QtCreator](http://download.qt.io/official_releases/qtcreator/). The minimum version we support is 3.3, then install it:

    $ chmod 755 qt-creator-opensource-linux-<version>.run
    $ ./qt-creator-opensource-linux-<version>.run

If you install QtCreator as root then it will be placed in `/opt/qtcreator-<version>`, so you can add this to your `$PATH` for convenience.

### Loading Open-TEE with qtcreator

    $ cd <PATH_TO_OPENTEE_REPO>
    $ qtcreator project.qbs


### Building Open-TEE

Select **Build > Build project "project"** or use `Ctrl + B`

### Running and stopping

Before you can run Open-TEE from QtCreator you must first configure the `/etc/opentee.conf` file to point towards the build directory, this is generally in the parent directory to where the Open-TEE repo is cloned under `build-project-Desktop-Debug`. See [Configure Runtime Environment](/documentation/#configure-runtime-environment) for the details.

Open-TEE provides numerous sample applications so you must explicitly select which one you wish to run from the `project` menu on the bottom left.

Start with the `opentee-engine` which starts the main framework:

![Select the project to Run](http://open-tee.github.io/images/opentee_select_project.png)

Then press `play` to start it running

Once that is running you can start any of the sample CAs in the same way

To stop opentee, kill the `tee_manager` process, select **Debug > Start Debugging > Attach to running process**. Then search for the process to kill:

![Kill tee_manager](http://open-tee.github.io/images/kill_tee_manager.png)

