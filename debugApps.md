# ---
# layout: page
# title: DebugApps
# permalink: /debugApps/
# ---

This page includes useful information about how to use Open-TEE. 
This presents a few methods on how to use Open-TEE for debugging
TAs/CAs. 

if you come upon any problems, please do not hesitate to contact us. 

### GDB
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


#### TA is not running 
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
set follow-fork-mode child
~~~

Continue GDB (hit "c" and enter) and now it is waiting for your TA. 

About the GDB usage, you can use it as you have learned to use it. 

### Using logs
Open-TEE is using system log for logging useful information about
Open-TEE and your TA/CA process. By default it is printing some
information but you can add more logging print outs to log by
yourself. Open-TEE is providing you: 

~~~
OT_LOG(priority, format, ...) //Prints also call location
OT_LOG_1(priority, format, ...) //No extra prints
~~~

This function is basically a wrapper around syslog-function. For
viewing the logs, you can use for example (ubuntu-example):

~~~
$ tail -f /var/log/syslog
~~~
