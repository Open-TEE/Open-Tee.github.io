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

Open-TEE provides numerous sample applications so you must explicitly select which one you wish to run from the `project` menu on the bottom left.

![Select the project to Run](images/opentee_select_project.png)




