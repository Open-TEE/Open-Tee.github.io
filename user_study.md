---
layout: page
title: User Study
permalink: /userstudy/
---

Our goal in this study is to understand to what extent Open-TEE
addresses the difficulties encountered by developers of Trusted
Applications (TAs) for Trusted Execution Environments (TEEs). 

This user study consists of three steps. First we would like you to
fill out a pre-study questionnaire, the purpose of which is to collect
background information on the participants. After the pre-study
questionnaire there will be a little exercise. You will be asked to use
Open-TEE to complete the exercise, and afterwards fill out a post-study
questionnaire, the purpose of which is to collect information about your
experience in using Open-TEE in this particular exercise.

The results of this study will be used to further Open-TEE development
and therefore we would appreciate your feedback.

#### Participants
This user study is mainly aimed towards people who are acquainted with
TEEs and have developed TAs. The user study itself does not need any
special beforehand skills or knowledge. We have composed a tutorial
and user guide for Open-TEE you can refer to throughout the study.
However, anyone interested in this subject is still welcome to
participate even if he/she does not have TA development experience.

#### Pre-requirements
Before beginning the actual user-study, please make sure that you have
Open-TEE deployed. For instructions on how to build and install
Open-TEE please see the [tutorial page](/tutorial/).

#### Contact/help
In case of questions about the user study, please see the
[contacts page](/contact/). In case of technical problems,
also feel free to contact us.

#### Documents
[Pre study questionnaire](https://github.com/Open-TEE/Open-Tee.github.io/raw/master/documents/pre-study-questionnaire.pdf)

[Post study questionnaire](https://github.com/Open-TEE/Open-Tee.github.io/raw/master/documents/post-study-questionnaire.pdf)

### User study: step 1
In the first phase of the user study, please take some time to fill
out the pre-study questionnaire. Fill out the questionnaire as best as
you can following the instructions in the beginning of the form.
Please complete the steps in order and do not skip any of the steps.

### User study: step 2
You will be using Open-TEE for debugging a Trusted Application. The
debugging is needed because we have purposely injected some bugs into
this user study TA, and your task is to fix it. You will be working
with a "virtual wallet" type application. This application has been
implemented only for use in this user study, and should not be used as
a reference in implementing TAs for production use.

#### Bugs
In this exercise, you will be focusing only on the TA application.
Bugs have been introduced only into this application. Open-TEE and
corresponding Client Application do not need attention, in this
exercise, and therefor you can assume that they are implemented
correctly. The bugs introduced to the TA are not logical in nature and
all algorithms work as they are supposed to and are returning correct
values.

#### First test run with bug
Now we assume that you have set up the Open-TEE environment. To
execute the first test run, follow the steps below: 

1. If not running already, launch Open-TEE as per the instructions on
   the [tutorial page](/tutorial/).

2. Navigate into the Open-TEE directory. The user study source code is
   in two parts, the Client Application (CA) and the Trusted Application
   (TA) located at `CAs/usr_study_ca/` and  `TAs/usr_study_ta/` 
   respectively.

   The test binary is called "usr_study_ca" and located under the
   `gcc-debug/` directory. It is important that you have not modified
   this binary yet. 

3. Run user study binary. You will see the following print out onto
   the console: 

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

If you see this print out, then all is well. Now you can proceed debug
the TA. 

#### Debugging the TA 
As mentioned previously, all the bugs introduced are located in TA
code. The TA source file is located  at `TAs/usr_study_ta/`

The TA is called "usr_study_ta". For tracking down the bugs, you
can use your own favorite method. For example, you could use GDB.

Once you have pinpointed the location of the bugs, fix them in the TA
source file. After saving you changes, recompile the TA. 

#### Re-run debugged TA
Once you have fixed the bugs it is time to verify if all the bugs
have been found and fixed by following the steps below: 

1. Open-TEE should now be running. Do not launch it the second time. 
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

Verify your result by comparing your print out to the print out above.
Note the line at the end of the print out indicating if the run passed
or failed. If the run fails, go back to step 2.

In case of success, this part is done and you can proceed to step 3.
If you have no alternative use for Open-TEE, you can remove Open-TEE. 

### User study: step 3
Please fill out the post study questionnaire. Instructions can be once
again found in the questionnaire itself. Step 3 should be completed
based on your experience with OpenTEE in step 2. If you are unable to
complete step 2 successfully, please still fill out the questionnaire. 

### User study: step 4
Do not forget to return the two questionnaires!

### Epilogue
After completing the steps mentioned above, your part has been
successfully completed. We thank you for taking the time to
participate in the user study. If during the user study or after
finishing it, you developed any questions about the user study or
Open-TEE, please do not hesitate to contact us. Any additional
feedback would also be welcome.
