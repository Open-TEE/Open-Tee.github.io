---
layout: post
title: Omnishare
permalink: /tutorial/omnishare
---

[Omnishare](https://git.ssg.aalto.fi/close/OmniShare/tree/TEE-integration) is a scheme to allow client-side encryption with high-entropy keys for public cloud services such as dropbox and google drive. In addition it defines an intuitive key distribution menchanism enabling data access from multiple devices.

For the purposes of this workshop we will look at enabling the key protection, encryption and decryption using a GP compliant TEE.

## Running Omnishare

### Command line Invocation

    $ cd <PATH_TO_OPENTEE_REPO>
    $ opentee restart
    $ CAs/omnishare_fuse/omnishare start
    $ CAs/omnishare_fuse/omnishare copy
    $ diff /tmp/local_view/test_file.c CAs/omnishare_fuse/tests/test_file.c
    $ diff /tmp/cloud_store/test_file.c CAs/omnishare_fuse/tests/test_file.c

The last command should produce the following error message:

> Binary files /tmp/cloud_store/test_file.c and CAs/omnishare_fuse/tests/test_file.c differ

### QtCreator Invocation

    $ mkdir -p /tmp/{local_view,cloud_store}

Edit the run properties of the omnishare project, to add `/tmp/local_view /tmp/cloud_store` as the `Command line arguments`:

![Edit the run config](http://open-tee.github.io/images/run_config_omnishare_fuse.png)

Start the `opentee-engine` and `omnishare_fuse` projects in QtCreator, then from the command line invoke the tests

    $ cd <PATH_TO_OPENTEE_REPO>
    $ CAs/omnishare_fuse/omnishare copy
    $ diff /tmp/local_view/test_file.c CAs/omnishare_fuse/tests/test_file.c
    $ diff /tmp/cloud_store/test_file.c CAs/omnishare_fuse/tests/test_file.c

## Debugging Omnishare

### Using QtCreator

Start the `opentee-engine` and `omnishare_fuse` projects as above.

### Attaching to the TA

**Debug > Start debugging > Attach to running process** and search for `omnishare`.

![Attach to Omnishare](http://open-tee.github.io/images/attach_to_omnishare.png)

Select the `libomnishare_ta.so` process to attach to. Enter a breakpoint in the `TA_InvokeCommandEntryPoint` function and continue the process.

![Set Breakpoint Omnishare](http://open-tee.github.io/images/set_break_point_in_omnishare_ta.png)

Then run the test cases and watch the breakpoint be triggered, allowing stepping and inspection of the code.

    $ cd <PATH_TO_OPENTEE_REPO>
    $ CAs/omnishare_fuse/omnishare copy

### Attaching to the CA

Same as for the TA we will choose to **Debug > Start debugging > Attach to running process** and search for `omnishare`, however, this time attach to the `omnishare_fuse` process. Remember you can only debug 1 process at a time so you must complete the TA debugging first!

Add a breakpoint in libomnishare and continue the process

![Breakpoint Omnishare CA](http://open-tee.github.io/images/breakpoint_omnishare_ca.png).

Yet again run the tests and watch the breakpoint trigger.

    $ cd <PATH_TO_OPENTEE_REPO>
    $ CAs/omnishare_fuse/omnishare copy

## Using GDB

For GDB follow the instructions in the main [documentation](documentation/#debugging-with-gdb) section.

