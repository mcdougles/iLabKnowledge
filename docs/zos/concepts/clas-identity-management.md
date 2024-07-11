# Creating Pipeline Functional IDs Using CLAS

CLAS is the tool on Cirrus 1.0 systems for user ID and group identity management.  This tool requires IDs and groups to have owners.  The owner’s IBM serial number is necessary for the steps listed below.  

!!! note
    Management approval is required for user ID and group management.  Contact the owner’s manager in advance so they are available for approvals and familiar with the tool.

The CLAS tool is a z/VM based application.  To do the following steps, you will need an ID on LAVM1: `lavm1.boulder.ibm.com`.  

1) Log into LAVM1 using a 3270 emulator:

![Step 1](../../images/zos/clas/clas-step-1.png) 

2) Start CLAS by running `CLAS`:

![Step 2](../../images/zos/clas/clas-step-2.png) 
 
3) Create a new ID by selecting option 2:

![Step 3](../../images/zos/clas/clas-step-3.png) 
 
4) Choose option 2, “Add User ID Selecting Features”:

![Step 4](../../images/zos/clas/clas-step-4.png) 

!!! note
    The user ID needs to be owned by an IBM employee. Have the owner's IBM Serial Number available before proceeding with the next step.

5) Enter the Serial Number, ID Name, and the system this ID is being created for in this next window:

![Step 5](../../images/zos/clas/clas-step-5.png) 
 
6) Add TSO and then display other options.  1 for TSO and then 2 for Display options:

![Step 6](../../images/zos/clas/clas-step-6.png) 

7) Choose option 1 to create more than one ID on the same system or optionally chose option 2 to create the same ID on different systems:

![Step 7](../../images/zos/clas/clas-step-7.png) 

8) Creating a RACF group for these IDs:

![Step 8](../../images/zos/clas/clas-step-8.png) 
  
9) Once ID is created, add the user to your RACF group, if you have one.  Option 1 describes how to create the group if you have not done so yet:

![Step 9](../../images/zos/clas/clas-step-9.png) 
 
10) Provide an the owner for the group with the serial number noted above and enter the group name and system:

![Step 10](../../images/zos/clas/clas-step-10.png) 
 
11) Enter RACF owner: a group or user that is already in CLAS. Once you hit enter this will be sent to the owner’s manager for approval:

![Step 11](../../images/zos/clas/clas-step-11.png) 
 
Upon completion, additional users can be added to this group providing them read access to all of the datasets and z/OS Unix files.