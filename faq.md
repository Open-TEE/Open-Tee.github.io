---
layout: page
title: FAQ
permalink: /faq/
---

_Is Open-TEE a TEE OS?_

No. Open-TEE is a runtime intended to ease Trusted Application
development by allowing developers to utilize conventional software
development tools, such as [GDB](https://www.gnu.org/software/gdb/),
for TA development, without the need for hardware with e.g.
interactive debugging support. Open-TEE itself runs on top of a
conventional operating system (GNU/Linux).

_I get the following error when trying to attach GDB to `tee_launcher`
or TA process. How can I avoid it?_

> Could not attach to process.  If your uid matches the uid of the target  
> process, check the setting of /proc/sys/kernel/yama/ptrace_scope, or try  
> again as the root user.  For more details, see /etc/sysctl.d/10-ptrace.conf  
> ptrace: Operation not permitted.  

The error indicates that the
[YAMA](https://www.kernel.org/doc/Documentation/security/Yama.txt)
Linux Security Module is enabled and preventing the GDB process from attaching to a 
process which is not its descendant with
[`ptrace()`](http://man7.org/linux/man-pages/man2/ptrace.2.html).  You can temporarily disable
this restriction by running the the following command:

    $ echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope


To permanently allow the ptracing of non-child processes by non-root
users edit `/etc/sysctl.d/10-ptrace.conf` and change the line: 

    kernel.yama.ptrace_scope = 1

to 

    kernel.yama.ptrace_scope = 0

For more information on why `ptrace` protection has been put in
place, see for instance the [Kernel Hardening page on the Ubuntu wiki](https://wiki.ubuntu.com/SecurityTeam/Roadmap/KernelHardening#ptrace_Protection)
