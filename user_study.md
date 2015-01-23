---
layout: page
title: User Study
permalink: /userstudy/
---

Our goal in this study is to understand to what extent Open-TEE
addresses the difficulties encountered by developers of Trusted
Applications (TAs) for Trusted Execution Environments (TEEs). 

This user study consists of three steps. First we would like you to
fill out a pre study questionnare, the purpose of which is to collect
participants background information. After the pre study questionnare there will be a
little exercise. You will be asked to use Open-TEE and after that we
would like to you fill out a post study questionnare, which will be
collecting information aboutyour experience in using Open-TEE in this
particular exercise.

This study result will be used for Open-TEE development and therefore
we would appreciate your feedback.

#### Participants
This user study is mainly aimed towards people who are aquainted with
TEE and have developed TAs. The user study itself does not need any
special skills or knowledge beforehand, because we have composed a
tutorial and user guide for throughout the study. However if anyone is
interested in this subject and does not have any pre-knowledge or
experience, he/she is still welcome to participate.  

#### Pre requirements
Before starting out with actual user study, make sure that you have
Open-TEE deployed. If you do not have this done, please see 
[tutorial page](/tutorial/).

#### Contact/help
In case of questions about the user study, go see the contacts page.
In case of technical problems, also feel free to 
[contact](/contact/) us. 

### User study: step 1
At the first phase of the user study, please take some time to fill
out the pre-study questionnare. Fill out the questionnare as best as
you can following the instructions given at the questionnare. Please
follow the steps according to their order and do not skip any of the
steps.


### User study: step 2
You will be using Open-TEE for debugging Trusted Application. The
debugging is needed because we have purposely injected some bugs into
this user study TA and your task is to fix it. You will be fixing a
"virtual wallet" type application. This application is implemented for
this user study and it is only serving the purpose of this user study.
We must emphasize the artificial nature of this application and that
you must not implement it to real life.

#### Bugs
You will be focusing only to TA application and the bugs exist only in
this application. Open-TEE and corresponding Client Application do not
need attention because they are implemented correctly. The injected
bugs are not logical type and all the algorithms work as they are
supposed to and returnig correct values.

#### First test run with bug
Now we assume that you have set up the Open-TEE environment. To
execute the first test run, follow the following steps: 

1. If not running already, launch Open-TEE.
2. Navigate into Open-TEE directory. User study binary is located:

~~~
/CAs/usr_study_ca/
~~~

Binary name is "usr_study_ca". It is important that you have not modified this binary. 

3. Run user study binary. You will be getting the following print out
onto console: 

~~~
START: usr study app
Initializing: Ok
----Begin-with-test-cases----
Making transactions: failed: TEE_ERROR_TARGET_DEAD
-------------------------
Run summary: FAILED
-------------------------
END: usr study app
~~~

If you are seeing this print out, then all is well. Now you can
proceed to bug hunting. 

#### Debugging the TA 
As said before, the bugs are located in TA. The TA source file is located 

~~~
/TAs/usr_study_ta/
~~~

It is called "usr_study_ta". For tracking down the bugs, you
can use your own favorite method. For example, you could use GDB.
See [tutorial](/tutorial/) for more information.

If you have found the bugs then fix the bugs in TA source file. After
editing recompile TA. 

#### Re-run debugged TA
Now you have fixed the bugs and it is time to verify if all the bugs
have been found and fixed. Follow the following steps: 

1. Open-TEE should now be running and do not launch it the second time. 
2. Execute the corresponding Client Application. 
3. You will be getting a print out on your console. In case you have
found and fixed all the bugs, you will see the following print out: 

~~~
START: usr study app
Initializing: Ok
----Begin-with-test-cases----
Making transactions: Ok
Querying account: Ok
Getting random transactions: Ok
Executing TA self checking: Ok
Resetting account: Ok
Testing unknown cmd: Ok
Executing TA self checking: Ok
-------------------------
Run summary: PASS
-------------------------
END: usr study app
~~~

Verify your result with comparing your print out and this print out.
Note that at the end of the print out is a line that says if you
passed or failed. If you failed, go back to "step 2".

In case of success, this part is done and you can move to step 3. If
you have no alternative use for Open-TEE, you can remove Open-TEE. 

### User study: step 3
Please fill out the post study questionnare. Instructions can be once
again found in the questionnare itself. Step 3 should be completed
based on your previous step (2) experience of OpenTEE. If you do not 
succeed in doing step 2 as planned, please take time and
still do step 3. 

### User study: step 4
Do not forget to return the two questionnares!

### Epiogue
After completing these above mentioned steps, your part has been
successfully completed. We thank you for participating and taking the
time. If during completing the user study or after finishing it, you
developed any questions about the user study or OpenTEE, please do not
hesitate to contact us. Extra feedback would also be welcome.   
