# Running COBOL program using Dependency Based Build (DBB)

Dependency Based Build (DBB) is an intelligent build technology that is used in a DevOps pipeline for building Mainframe applications. IBM Developer for z Systems® (IDz) with DBB is used in a DevOps pipeline along with an open source tool stack such as Git, Jenkins and Artifactory. While Git, Jenkins and Artifactory are used across the enterprise, DBB fills the gap to provide a build framework for mainframe objects.

A developer who is working on COBOL applications can run a DBB user build to compile and link programs before the code is ready to be exposed to the repository for others to use. With user build, you can compile your program without having to perform commits or pushes. 

## Prerequsite

- zAppBuild

After installation, you must make sure your COBOL source and  COPYBOOK by checking genapp/application-conf/application.properties.
Pay attention to the following statements.
```
# check Cobol.properties is included for applicationPropFiles 
applicationPropFiles=file.properties,bind.properties,Assembler.properties,BMS.properties,Cobol.properties,LinkEdit.properties,bind.properties,PLI.properties,MFS.properties,PSBgen.properties,DBDgen.properties,ACBgen.properties,REXX.properties,ZunitConfig.properties,Transfer.properties,reports.properties,datasets.properties
  :
# make sure copybookSearch is included for impactSearch
impactSearch=${copybookSearch}${pliincludeSearch}${bmsSearch}${linkSearch}${rexxCopySearch}${zunitTestConfigSearch}${zunitApplicationPgmSearch}
  :
# make sure copybookSearch is included
copybookSearch = search:${workspace}/?path=${application}/src/main/copybook/*.cpy
```

You can configure COBOL specific parameters from genapp/application-conf/Cobol.properties

- zoau - Z Open Automation Utilities



## Step 1: Generate a COBOL source 

Write a simple COBOL program and save with mycobol.cbl.

```
       IDENTIFICATION DIVISION.
       PROGRAM-ID.   FIRSTPRG.
       AUTHOR.       SUNGIK.
       DATE-WRITTEN. 02/16/2023.
       ENVIRONMENT DIVISION.              
      *
       DATA DIVISION.
       FILE SECTION.
       WORKING-STORAGE SECTION.
       01 NUM1 PIC 9(2)  VALUE 10.
       01 NUM2 PIC 9(2)  VALUE 20.
       01 NUM3 PIC 9(2).
       PROCEDURE DIVISION.
           DISPLAY "This is my first COBOL calculation since 1985 !!".
           ADD NUM1, NUM2 GIVING NUM3.
           DISPLAY "The number ", NUM1, " + ", NUM2, " = ", NUM3
           STOPRUN.
```
The above program will display the result of 10 + 20.
Place the sourec under genapp/src/main/cobol folder.

## Step 2: Execute DDB User Build

```
HLQ=SUNGIK.TEST
fn="genapp/src/main/cobol/mycobol.cbl"
$DBB_HOME/bin/groovyz /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build.groovy --sourceDir /u/sungik/ga1 --workDir /u/sungik/ga1/work1 --hlq $HLQ -a  genapp  --userBuild  $fn
```

## Step 3: Verify the console log if the build is clean

```
** Build start at 20230216.102816.028
** Build output located at /u/sungik/ga1/work1
** Adding genapp/src/main/cobol/mycobol.cbl to Building build list
** Writing build list file to /u/sungik/ga1/work1/buildList.txt
** Populating file level properties from individual property files.
** Invoking build scripts according to build order: BMS.groovy,MFS.groovy,Cobol.groovy,Assembler.groovy,PLI.groovy,LinkEdit.groovy,DBDgen.groovy,PSBgen.groovy,Transfer.groovy
** Building files mapped to Cobol.groovy script
create language datasets
*** Building file genapp/src/main/cobol/mycobol.cbl
create MVS command - CopyToPDS
member = MYCOBOL
createCobolParms - Builds up the COBOL compiler parameter list from build and file properties
Cobol compiler parms for genapp/src/main/cobol/mycobol.cbl = LIB
createCompileCommand parms LIB
createCompileCommand compiler IGYCRCTL
MVSExec compile = new MVSExec().file(genapp/src/main/cobol/mycobol.cbl).pgm(IGYCRCTL).parm(LIB)
createCompileCommand - creates a MVSExec command for compiling the COBOL program (buildFile)
compile.dd(new DDStatement().name("SYSIN").dsn("SUNGIK.TEST.COBOL(MYCOBOL)").options('shr').report(true))
:
createLinkEditCommand - creates a MVSExec xommand for link editing the COBOL object module produced by the compile
add DD statements to the linkedit command
execute mvs commands in a mvs job
compile the cobol program
compiled with rc = 0
linked edit ..
false
needsLinking =true
linked edit completed with 0
Cleanup passed DD statements
** Writing build report data to /u/sungik/ga1/work1/BuildReport.json
** Writing build report to /u/sungik/ga1/work1/BuildReport.html
** Build ended at Thu Feb 16 10:28:24 MST 2023
** Build State : CLEAN
** Total files processed : 1
** Total build time  : 7.975 seconds

** Build finished

```

## Step 4: Verify Generated Datasets and members

Now your COBOL program mycobol.cbl has been compiled, linked, and loaded.
You can view your Dataset using zoau API.

```
:/u/sungik:->dls -lsub SUNGIK.TEST.*

SUNGIK.TEST.COBOL                            2023/02/16 PO  FB      80 32720 TC791D           ??       849960
SUNGIK.TEST.COPY                             2023/02/16 PO  FB      80 32720 TC791D           ??       849960
SUNGIK.TEST.DBRM                             2023/02/16 PO  FB      80 32720 TC790D           ??       849960
SUNGIK.TEST.LOAD                             2023/02/16 PO  U        0 32760 TC791E           ??       849960
SUNGIK.TEST.OBJ                              2023/02/16 PO  FB      80 32720 TC780E           ??       849960
```

See the member inside SUNGIK.TEST.COBOL and SUNGIK.TEST.LOAD.

```
:/u/sungik:->mls SUNGIK.TEST.COBOL
MYCOBOL
:/u/sungik:->mls SUNGIK.TEST.LOAD 
MYCOBOL
:/u/sungik:->
```

You now can view your first COBOL source from SUNGIK.TEST.COBOL(MYCOBOL).

```
:/u/sungik:->dtail -n +1 "SUNGIK.TEST.COBOL(MYCOBOL)"
       IDENTIFICATION DIVISION.                                                 
       PROGRAM-ID.   FIRSTPRG.                                                  
       AUTHOR.       SUNGIK.                                                    
       DATE-WRITTEN. 02/16/2023.                                                
                                                                                
       ENVIRONMENT DIVISION.                                                    
       DATA DIVISION.                                                           
      *                                                                         
       FILE SECTION.                                                            
       WORKING-STORAGE SECTION.                                                 
       01 NUM1 PIC 9(2)     VALUE 10.                                           
       01 NUM2 PIC 9(2)     VALUE 20.                                           
       01 NUM3 PIC 9(2).                                                        
      *                                                                         
       PROCEDURE DIVISION.                                                      
           DISPLAY "This is my first COBOL since 1985 !!".                      
           ADD NUM1, NUM2 GIVING NUM3.                                          
           DISPLAY "The number ", NUM1, " + ", NUM2, " = ", NUM3                
      *    CALL                                                                 
           STOP RUN.                                                            
:/u/sungik:->
```

## Step 5: Generate a JCL to execute MYCOBOL.

You must use SUNGIK.TEST.LOAD for your STEPLIB as that contains the execution file. Place the new JCL named newprg.jcl under genapp/src/main/jcl folder.
Note that PGM name is MYCOBOL.

```
//SUNGIK JOB 'WD-PKGBIND',MSGLEVEL=(1,1),MSGCLASS=R,NOTIFY=&SYSUID
//*
//*
//* NOTE THAT THE EXCLAMATION POINT IS INVALID EBCDIC FOR JCL
//*   AND WILL CAUSE A JCL ERROR
//*
//RUN EXEC PGM=MYCOBOL
//STEPLIB  DD DISP=SHR,DSN=SUNGIK.TEST.LOAD
//SYSIN    DD DUMMY
//SYSPRINT DD SYSOUT=*
//SYSUT1   DD *
//SYSUT2   DD SYSOUT=*
```
## Step 6: Submit JCL

Submit the JCL and you will get the JOB ID.
Using that JOB ID, verify if CC is 0 shown below.
```
:/u/sungik:->jsub -f /u/sungik/ga1/genapp/src/main/jcl/newprg.jcl
JOB08824
:/u/sungik:->jls | grep JOB08824
SUNGIK   SUNGIK   JOB08824 CC       0000
:/u/sungik:->

```

## Step 7: Verify the JOB Execution and the program result

```
:/u/sungik:->ddls JOB08824
JES2     -        JESMSGLG UA         133        19   2
JES2     -        JESJCL   V          136        13   3
JES2     -        JESYSMSG VA         137        50   4
RUN      -        SYSOUT   FBA        121         2   104
```
You can see the details by displaying JESYSMSG
```
:/u/sungik:->pjdd JOB08824 JESYSMSG
 ICH70001I SUNGIK   LAST ACCESS AT 12:46:43 ON THURSDAY, FEBRUARY 16, 2023
 IEFA111I SUNGIK IS USING THE FOLLOWING JOB RELATED SETTINGS:
          SWA=ABOVE,TIOT SIZE=64K,DSENQSHR=DISALLOW,GDGBIAS=JOB
 IEF236I ALLOC. FOR SUNGIK RUN
 IGD103I SMS ALLOCATED TO DDNAME STEPLIB
 IEF237I DMY  ALLOCATED TO SYSIN
 IEF237I JES2 ALLOCATED TO SYSPRINT
 IEF237I JES2 ALLOCATED TO SYSUT1
 IEF237I JES2 ALLOCATED TO SYSUT2
 IEF237I JES2 ALLOCATED TO SYSOUT
 IEF285I   SUNGIK.SUNGIK.JOB08824.D0000104.?            SYSOUT
 IEF142I SUNGIK RUN - STEP WAS EXECUTED - COND CODE 0000
 IGD104I SUNGIK.TEST.LOAD                             RETAINED,  DDNAME=STEPLIB
 IEF285I   SUNGIK.SUNGIK.JOB08824.D0000102.?            SYSOUT
 IEF285I   SUNGIK.SUNGIK.JOB08824.D0000101.?            SYSIN
 IEF285I   SUNGIK.SUNGIK.JOB08824.D0000103.?            SYSOUT
 ************************************************************************************************************************************
 * DDNAME   UNIT   EXCPS BLKSZ|DDNAME   UNIT   EXCPS BLKSZ|DDNAME   UNIT   EXCPS BLKSZ|DDNAME   UNIT   EXCPS BLKSZ                  *
 *----------------------------+---------------------------+---------------------------+---------------------------------------------*
 * STEPLIB  791E      20 32760|TOTAL:             20 *****|                           |                                             *
 ************************************************************************************************************************************
 IEF373I STEP/RUN     /START 2023047.1257
 IEF032I STEP/RUN     /STOP  2023047.1257
         CPU:     0 HR  00 MIN  00.00 SEC    SRB:     0 HR  00 MIN  00.00 SEC
         VIRT:    72K  SYS:   296K  EXT:     2996K  SYS:    11116K
         ATB- REAL:                    28K  SLOTS:                     0K
              VIRT- ALLOC:      11M SHRD:       0M
 ************************************************************************************************************************************
 * z/OS    02.04.00 SYSTEM 3906       (ZTC1) :    SMF STATISTICS                                     DATE=2023-02-16  TIME=12:57:24 *
 ************************************************************************************************************************************
 *                                                                                                                                  *
 * //SUNGIK   JOB (WD-PKGBIND),                                                                                                     *
 *                                                                                                                                  *
 * STEP                               TOTAL     VIO      TCB      SRB    OTHER  ELAPSED   STORAGE     NO.OF  NO.OF  TIME ON  TIME ON*
 * NO. STEPNAME PROCSTEP PROGRAM     EXCP'S  EXCP'S CPU-TIME CPU-TIME CPU-TIME     TIME   <16M  >16M   PAGES SWAPS     ZIIP     ZAAP*
 *                                                                                                                                  *
 *   1 RUN               MYCOBOL         20       0    0.00S    0.00S    0.01S    0.19S    72K    2M       0     0    0.00S    0.00S*
 *----------------------------------------------------------------------------------------------------------------------------------*
 * JOB SUM (WITH JOB OVERHEAD):          20       0    0.00S    0.00S    0.01S    0.19S    72K    2M       0     0    0.00S    0.00S*
 *==================================================================================================================================*
 * STORAGE SECTION | USED PRIVATE AREA BELOW 16MB:             72K | ALLOWED PRIVATE AREA BELOW 16MB:                         2048K *
 *                 | USED PRIVATE AREA ABOVE 16MB:              2M | ALLOWED PRIVATE AREA ABOVE 16MB:                           32M *
 *                 | USED VIRT. STORAGE ABOVE 2GB:             11M | ALLOWED VIRT. STORAGE ABOVE 2GB:                            3G *
 *----------------------------------------------------------------------------------------------------------------------------------*
 * WLM SECTION     | WORKLOAD NAME                        BATCH    | SERVICE CLASS NAME                                    BATCH    *
 *                 | RESOURCE GROUP NAME                           | REPORT CLASS NAME                                     BATCH    *
 ************************************************************************************************************************************
 IEF375I  JOB/SUNGIK  /START 2023047.1257
 IEF033I  JOB/SUNGIK  /STOP  2023047.1257
         CPU:     0 HR  00 MIN  00.00 SEC    SRB:     0 HR  00 MIN  00.00 SEC
```
Finally you can see the program result

```
:/u/sungik:->pjdd JOB08824 RUN SYSOUT
 This is my first COBOL since 1985 !!
 The number 10 + 20 = 30
:/u/sungik:->
```
## Step 8: COBOL source cbl vs. COBOL COPYBOOK cpy

We must place a COBOL program under /genapp/src/main/cobol folder and COBOL COPYBOOK under /genapp/src/main/copybook folder.
Remember that COBOL source program must have file type cbl. COBOL COPYBOOK must have file type .cpy.

COBOL program uses COPY statement to include prewritten code into the program during the compilation process.
You must build COBOL COPYBOOK first before building COBOL program.

Write a source COBOL COPYBOOK mycobol.cpy 

```
      ******************************************************************
      *             Simple Scenario - Lord General Insurance Co.       *
      *                                                                *
      *               COPYBOOK for COMMAREA structure                  *
      *                                                                *
      *     Get valid policy number output comm area                   *
      ******************************************************************
       01 WS-BLANK-DEMO.
          05  WS-SERIAL     PIC  9(08)V99        VALUE  ZEROES.
          05  WS-TOTAL-REV  PIC  Z,ZZZ,ZZZ.99CR  BLANK WHEN ZEROES.
          05  WS-INT-RATE   PIC  Z9.99           BLANK WHEN ZEROS.
          05  WS-LN-PRCNT   PIC  99.99           BLANK WHEN ZEROES.
       01 WS-EMP-NAME.
          05  WS-FNAME    PIC  X(15) VALUE SPACE.
          05  FILLER      PIC  X(01) VALUE SPACE.
```
Execute user-build for COBOL COPYBOOK
```
$DBB_HOME/bin/groovyz /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build.groovy \
   --sourceDir /u/sungik/ga1 \
   --workDir /u/sungik/ga1/work1 \
   --hlq SUNGIK.TEST \
   -a genapp \
   --userBuild /u/sungik/ga1/genapp/src/main/copybook/mycobol.cpy
```
Verify your new dataset .COPY is generated.

```
dtail -n +1 "SUNGIK.TEST.COPY(MYCOBOL)"
      ******************************************************************        
      *             Simple Scenario - Lord General Insurance Co.       *        
      *                                                                *        
      *               COPYBOOK for COMMAREA structure                  *        
      *                                                                *        
      *     Get valid policy number output comm area                   *        
      ******************************************************************        
       01 WS-BLANK-DEMO.                                                        
          05  WS-SERIAL     PIC  9(08)V99        VALUE  ZEROES.                 
          05  WS-TOTAL-REV  PIC  Z,ZZZ,ZZZ.99CR  BLANK WHEN ZEROES.             
          05  WS-INT-RATE   PIC  Z9.99           BLANK WHEN ZEROS.              
          05  WS-LN-PRCNT   PIC  99.99           BLANK WHEN ZEROES.             
       01 WS-EMP-NAME.                                                          
          05  WS-FNAME    PIC  X(15) VALUE SPACE.                               
          05  FILLER      PIC  X(01) VALUE SPACE.                               
          05  WS-LNAME    PIC  X(20) VALUE SPACE.               
```
We now need to modify our original COBOL program to include the above COPYBOOK.
We add COPY MYCOBOL. statement and later use the data field WS-FNAME defined inside the COPYBOOK mycobol.cpy

```
       IDENTIFICATION DIVISION.
       PROGRAM-ID.   FIRSTPRG.
       AUTHOR.       SUNGIK.
       DATE-WRITTEN. 02/16/2023.

       ENVIRONMENT DIVISION.
       DATA DIVISION.
      *
       FILE SECTION.
       WORKING-STORAGE SECTION.
       01 NUM1 PIC 9(2)     VALUE 10.
       01 NUM2 PIC 9(2)     VALUE 20.
       01 NUM3 PIC 9(2).
      *
       COPY MYCOBOL.
      *
       PROCEDURE DIVISION.
           DISPLAY "This is my first COBOL since 1985 !!".
           ADD NUM1, NUM2 GIVING NUM3.
           DISPLAY "The number ", NUM1, " + ", NUM2, " = ", NUM3
           MOVE 'SUNGIK' TO WS-FNAME.
           DISPLAY "MY NAME IS ", WS-FNAME.
      *    CALL
           STOP RUN.

```

Execute user build for COBOL program

```
$DBB_HOME/bin/groovyz /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build.groovy \
   --sourceDir /u/sungik/ga1 \
   --workDir /u/sungik/ga1/work1 \
   --hlq SUNGIK.TEST \
   -a genapp \
   --userBuild /u/sungik/ga1/genapp/src/main/cobol/mycobol.cbl
```

Unfortunately you got the following build error

```
compile the cobol program
compiled with rc = 12
*! The compile return code (12) for genapp/src/main/cobol/mycobol.cbl exceeded the maximum return code allowed (4)
Cleanup passed DD statements
** Writing build report data to /u/sungik/ga1/work1/BuildReport.json
** Writing build report to /u/sungik/ga1/work1/BuildReport.html
** Build ended at Fri Feb 17 11:01:13 MST 2023
** Build State : ERROR
** Total files processed : 1
** Total build time  : 8.328 seconds

** Build finished
```

In order to see the isuue, you must look at MYCOBOL.log generated at /u/sungik/ga1/work1 folder

```
000033                PROCEDURE DIVISION.
  000034                    DISPLAY "This is my first COBOL since 1985 !!".
  000035                    ADD NUM1, NUM2 GIVING NUM3.                                           11 12 13
  000036                    DISPLAY "The number ", NUM1, " + ", NUM2, " = ", NUM3                 11 12 13
  000037         	   MOVE 'SUNGIK' TO WS-FNAME.

==000037==> IGYPS0002-E A character other than "*", "D", "/" or "-" was found in column 7.  A
                        blank was assumed.

==000037==> IGYPS0118-W An alphanumeric literal should not begin in area "A".  It was processed
                        as if found in area "B".

==000037==> IGYPS2008-E A period was required before procedure-name "E".  A period was assumed
                        before "E".

==000037==> IGYPS2145-E A period was required.  A period was assumed before "'SUNGIK'".

==000037==> IGYPS2072-S "'SUNGIK'" was invalid.  Skipped to the next verb, period or
                        procedure-name definition.

  000038**       	   DISPLAY "MY NAME IS ", WS-FNAME.
```
If you used vi for COBOL editing, it is very hard to detect the cause of the issue.
After fixing the issue you get the successful build.
Make sure your modified COBOL program is written into PDS.

```
dtail -n +1 "SUNGIK.TEST.COBOL(MYCOBOL)"
       IDENTIFICATION DIVISION.                                                 
       PROGRAM-ID.   FIRSTPRG.                                                  
       AUTHOR.       SUNGIK.                                                    
       DATE-WRITTEN. 02/16/2023.                                                
                                                                                
       ENVIRONMENT DIVISION.                                                    
       DATA DIVISION.                                                           
      *                                                                         
       FILE SECTION.                                                            
       WORKING-STORAGE SECTION.                                                 
       01 NUM1 PIC 9(2)     VALUE 10.                                           
       01 NUM2 PIC 9(2)     VALUE 20.                                           
       01 NUM3 PIC 9(2).                                                        
      *                                                                         
       COPY MYCOBOL.                                                            
      *                                                                         
       PROCEDURE DIVISION.                                                      
           DISPLAY "This is my first COBOL since 1985 !!".                      
           ADD NUM1, NUM2 GIVING NUM3.                                          
           DISPLAY "The number ", NUM1, " + ", NUM2, " = ", NUM3                
           MOVE 'SUNGIK' TO WS-FNAME.                                           
           DISPLAY "MY NAME IS ", WS-FNAME.                                     
      *    CALL                                                                 
           STOP RUN.                           
```

Now submit the job again and see the result.

```
:/u/sungik:->jsub -f /u/sungik/ga1/genapp/src/main/jcl/newprg.jcl
JOB09412 
jls | grep JOB09412 
SUNGIK   SUNGIK   JOB09412 CC       0000
ddls JOB09412
JES2     -        JESMSGLG UA         133        19   2
JES2     -        JESJCL   V          136        13   3
JES2     -        JESYSMSG VA         137        50   4
RUN      -        SYSOUT   FBA        121         3   104

pjdd JOB09412 RUN SYSOUT 
 This is my first COBOL since 1985 !!
 The number 10 + 20 = 30
 MY NAME IS SUNGIK
```
