# LPAR Setup

This page explains the process to set up your mainframe user for CIO-CICD.

!!! note
    [Application 3-character prefix](https://pages.github.ibm.com/cio-zos-automation/documentation/3-char-prefixes/){target=\_blank} is used to define application-associated assets for example data sets High level qualifiers (HLQ). To register an application prefix, submit your prefix request to slack channel [#cio-nw-onboarding-charprefix](https://my.slack.com/archives/C05M8KB3NH3){target=\_blank}. Be sure to tag **both** @martinel and @keith.wilson in your request message.

## Pipeline Functional Users 

One of the important steps in setting up an application for the CIO z/OS Application Pipeline is getting the functional ID's created for the pipeline.  The goal with these ID's is to provide as little access as is necessary, while still enabling them to run the pipeline.  Create a different ID for each environment (i.e. Dev, Test, Prod, etc.).  Use the naming convention of your `Application 3-character prefix` followed by a character that represents the environment, and then the number associated with this user (typically `001`).  

***Note: this convention will be required in the Cirrus 2.0 environment***  

An example of this using `BAR`:
 
 - dev -> `BARD001`
 - test -> `BART001`
 - prod -> `BARP001`

The CI/CD pipeline will run in the dev environment, and consequently this user will need a bit more access than the other IDs.  The dev user will need read access to compiler/link libraries, read access to shared libraries, write access to dev environment PDSs, and read access to subsystem libraries (like IMS, DB2, CICS, etc.).  

For the other environments, because they don't need access to the compilers, linkers, you only need write access to target PDSs, read access to shared libraries, and read access to subsystem libraries.

## Step 1: Set up users and groups using CLAS

CLAS user and group identity manage is covered in a separate guide: [CLAS Identity Management](../concepts/clas-identity-management.md).

Set up your users and groups before proceeding to the next step.

## Step 2: Set up SSH Public Key Authentication

{%
  include-markdown "../../includes/zos/ssh-authentication.md"
%}

## Step 3: Configure necessary applications and commands on your CICD LPAR

As you use the CI/CD pipeline to build and deploy, you may have different LPARs for `development` (triggered by Git push described [here](../pipelines/workflow.md)) and `production`, triggered by making an http request to our API after manual approval.

Please add your LPARs to the queue to have the necessary tools installed by the infrastructure team. You can do this by sending a message to the support channel [#cio-zos-ci-cd-support](https://my.slack.com/archives/C034Y7URH97){target=\_blank} in the following format, supplying your app's name, the hostnames/LPARs you will need the tools installed on, and what their roles are (dev/test/prod):

> ATTN AppMod: Our app `AppName` is beginning the migration process onto the pipeline. Please have infrastructure install the necessary tools on:
> 
> ```
> Hostnames:
> 
> LPARs:
> 
> Sysplex:
> 
> ```

Installing the packages using this procedure has been approved for the CIO Office.  Some of the tooling in this list are OpenSource, but they are parts of IBM products and have therefore been appropriately vetted.  We install them in this manner to make the setup process easier for onboarding application teams.  

After the tools have been installed on your LPAR, you will need to create a `.profile` for your account - ssh into your LPAR, then use `vi` to create a text file called `.profile` in your home directory (there are thousands of basic `vi` tutorials available through a google search, please see one of those for information on creating a new text file, editing it, and saving it with `vi`), and paste the following text into the file (make sure to update the `JAVA_HOME` field to point at your LPAR's java installation!):
```
#!/bin/sh

# clearing out the terminal
stty sane


#### User required fields ####
export JAVA_HOME=/usr/lpp/java/J8.0_64


#### Pipeline Tools ####
system_name=$(sysvar SYSNAME)
export PIPELINE_TOOLS_HOME=/usr/lpp/IBM/$system_name/UssTools
. $PIPELINE_TOOLS_HOME/pipeline.env
```
