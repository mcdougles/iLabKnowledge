# Running ASSEMBLER program using Dependency Based Build (DBB)

Dependency Based Build (DBB) is an intelligent build technology that is used in a DevOps pipeline for building Mainframe applications. IBM Developer for z Systems® (IDz) with DBB is used in a DevOps pipeline along with an open source tool stack such as Git, Jenkins and Artifactory. While Git, Jenkins and Artifactory are used across the enterprise, DBB fills the gap to provide a build framework for mainframe objects.

A developer who is working on ASSEMBLER applications can run a DBB user build to compile and link programs before the code is ready to be exposed to the repository for others to use. With user build, you can compile your program without having to perform commits or pushes. 

## Prerequsite

- zAppBuild

After installation, you must make sure your Assembler properties is included by checking genapp/application-conf/application.properties.
Pay attention to the following statements.
```
# check Assembler.properties is included for applicationPropFiles 
applicationPropFiles=file.properties,bind.properties,Assembler.properties,BMS.properties,Cobol.properties,LinkEdit.properties,bind.properties,PLI.properties,MFS.properties,PSBgen.properties,DBDgen.properties,ACBgen.properties,REXX.properties,ZunitConfig.properties,Transfer.properties,reports.properties,datasets.properties
  :

```

- zoau - Z Open Automation Utilities


## Step 1: Generate a simple ASSEMBLER source 

https://github.com/IBM/dbb/blob/main/Build/HelloWorld/hello.asm

```
HELLO    CSECT
         USING *,15
         STM   14,12,12(13)
         LR    12,15
         USING HELLO,12
         WTO   'Hello ASM World!'
         XR    15,15
         RETURN (14,12)
         END
```


## Step 2: Execute DDB User Build

```
HLQ=SUNGIK.TEST
fn="genapp/src/main/asm/hello.asm"
$DBB_HOME/bin/groovyz /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build.groovy --sourceDir /u/sungik/ga1 --workDir /u/sungik/ga1/work1 --hlq $HLQ -a  genapp  --userBuild  $fn

```

## Step 3: Verify the console log if the build is clean

```
:
** zAppBuild running on DBB Toolkit Version 2.0.0 28-Oct-2022 14:56:55 
required props = buildOrder,buildListFileExt
** Build output located at /u/sungik/ga1/work1
** Unable to verify collections. No metadata store.
** Adding /u/sungik/ga1/genapp/src/main/asm/hello.asm to Building build list
** Writing build list file to /u/sungik/ga1/work1/buildList.txt
genapp/src/main/asm/hello.asm
** Populating file level properties from individual property files.
** Invoking build scripts according to build order: BMS.groovy,MFS.groovy,Cobol.groovy,Assembler.groovy,PLI.groovy,LinkEdit.groovy,DBDgen.groovy,PSBgen.groovy,Transfer.groovy,REXX.groovy
** Building files mapped to Assembler.groovy script
required props = assembler_srcPDS,assembler_macroPDS,assembler_objPDS,assembler_loadPDS, assembler_pgm,assembler_linkEditor,assembler_tempOptions,assembler_maxRC,   SASMMOD1,SDFHLOAD,SDFHMAC,MACLIB,SCEELKED,SCEEMAC
[main] DEBUG com.ibm.dbb.build.CreatePDS - Skip creating SUNGIK.TEST.ASM because it already exists
** Creating / verifying build dataset SUNGIK.TEST.ASM
[main] DEBUG com.ibm.dbb.build.CreatePDS - Skip creating SUNGIK.TEST.MACRO because it already exists
** Creating / verifying build dataset SUNGIK.TEST.MACRO
[main] DEBUG com.ibm.dbb.build.CreatePDS - Skip creating SUNGIK.TEST.OBJ because it already exists
** Creating / verifying build dataset SUNGIK.TEST.OBJ
[main] DEBUG com.ibm.dbb.build.CreatePDS - Skip creating SUNGIK.TEST.DBRM because it already exists
** Creating / verifying build dataset SUNGIK.TEST.DBRM
[main] DEBUG com.ibm.dbb.build.CreatePDS - Skip creating SUNGIK.TEST.LOAD because it already exists
** Creating / verifying build dataset SUNGIK.TEST.LOAD
*** Building file genapp/src/main/asm/hello.asm
***** 1
[main] DEBUG com.ibm.dbb.build.CopyToPDS - Copying as text, '/u/sungik/ga1/genapp/src/main/asm/hello.asm', (encoding=IBM-1047) to SUNGIK.TEST.ASM(HELLO) (encoding=IBM-1047)
[main] DEBUG com.ibm.dbb.build.CopyToPDS - Alloc Command: "alloc dd(SYS00009) dsn(SUNGIK.TEST.ASM(HELLO)) shr reuse msg(1)"
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(SYS00009) dsn(SUNGIK.TEST.ASM(HELLO)) shr reuse msg(1)
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(SYS00009)
*** Resolution rules for genapp/src/main/asm/hello.asm:
search:[SYSLIB:MACRO]/u/sungik/ga1/?path=genapp/src/main/maclib/*.macsearch:[SYSLIB:COPY]/u/sungik/ga1/?path=genapp/src/main/maclib/*.mac
[main] DEBUG com.ibm.dbb.build.internal.ProductRegistrationChecker - Checking product registration
[main] DEBUG com.ibm.dbb.build.internal.ProductRegistrationChecker - Checking product status for : {"productId":"5755-A01","featureName":"DBB","productName":"IBM APP DLIV FND"}
[main] DEBUG com.ibm.dbb.build.internal.ProductRegistrationChecker - result = [B@73d831e1 returnCode = 0 flag = 32
[main] DEBUG com.ibm.dbb.build.internal.ProductRegistrationChecker - Product Registration: Start registering the product id, 5755-A01
[main] DEBUG com.ibm.dbb.build.internal.ProductRegistrationChecker - Product registration was completed with NAME=IBM APP DLIV FND FEATURE=DBB VERSION=*.*.* ID=5755-A01
[main] DEBUG com.ibm.dbb.build.internal.ProductRegistrationChecker - Registered for product = {"productId":"5755-A01","featureName":"DBB","productName":"IBM APP DLIV FND"}
[main] DEBUG com.ibm.dbb.dependency.internal.DependencyUtilities - Getting logical file for file genapp/src/main/asm/hello.asm
[main] DEBUG com.ibm.dbb.dependency.internal.DependencyUtilities - Checking cache for file genapp/src/main/asm/hello.asm
[main] DEBUG com.ibm.dbb.dependency.internal.DependencyUtilities - Logical file not in cache
[main] DEBUG com.ibm.dbb.dependency.internal.DependencyUtilities - Creating logical file by scanning file in sourceDir /u/sungik/ga1
[main] DEBUG com.ibm.dbb.dependency.AbstractDependencyScanner - Retrieved HFS Encoding for /u/sungik/ga1/genapp/src/main/asm/hello.asm = IBM-1047
[main] DEBUG com.ibm.dbb.dependency.AbstractDependencyScanner - AbstractDependencyScanner.scan file = genapp/src/main/asm/hello.asm sourceDir = /u/sungik/ga1 encoding = IBM-1047
[main] DEBUG com.ibm.dbb.dependency.internal.DependencyUtilities - Caching file {
   "cics": false,
   "dli": false,
   "file": "genapp\/src\/main\/asm\/hello.asm",
   "language": "ASM",
   "lname": "HELLO",
   "mq": false,
   "sql": false
}
[main] DEBUG com.ibm.dbb.dependency.LogicalFile - Entering LogicalFile.resolveDependencies
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - Parsing initial search path search:[SYSLIB:MACRO]/u/sungik/ga1/?path=genapp/src/main/maclib/*.macsearch:[SYSLIB:COPY]/u/sungik/ga1/?path=genapp/src/main/maclib/*.mac
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - SearchPathString = search:[SYSLIB:MACRO]/u/sungik/ga1/?path=genapp/src/main/maclib/*.mac
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - SearchPathString = search:[SYSLIB:COPY]/u/sungik/ga1/?path=genapp/src/main/maclib/*.mac
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - Parsing search path segment search:[SYSLIB:MACRO]/u/sungik/ga1/?path=genapp/src/main/maclib/*.mac
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - Match Rules: [SYSLIB:MACRO]
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - Paths: genapp/src/main/maclib/*.mac
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - Search Root: /u/sungik/ga1/
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - Adding searchPath to searchPath List: {"categoryIncludes":["MACRO"],"paths":["genapp\/src\/main\/maclib\/*.mac"],"libraryIncludes":["SYSLIB"],"searchRoot":"\/u\/sungik\/ga1\/"}
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - Parsing search path segment search:[SYSLIB:COPY]/u/sungik/ga1/?path=genapp/src/main/maclib/*.mac
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - Match Rules: [SYSLIB:COPY]
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - Paths: genapp/src/main/maclib/*.mac
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - Search Root: /u/sungik/ga1/
[main] DEBUG com.ibm.dbb.dependency.internal.SearchPathParser - Adding searchPath to searchPath List: {"categoryIncludes":["COPY"],"paths":["genapp\/src\/main\/maclib\/*.mac"],"libraryIncludes":["SYSLIB"],"searchRoot":"\/u\/sungik\/ga1\/"}
[main] DEBUG com.ibm.dbb.dependency.LogicalFile - Leaving LogicalFile.resolveDependencies
*** Physical dependencies for genapp/src/main/asm/hello.asm:
[main] DEBUG com.ibm.dbb.dependency.internal.DependencyUtilities - Getting logical file for file genapp/src/main/asm/hello.asm
[main] DEBUG com.ibm.dbb.dependency.internal.DependencyUtilities - Checking cache for file genapp/src/main/asm/hello.asm
[main] DEBUG com.ibm.dbb.dependency.internal.DependencyUtilities - Logical file retrieved from cache
[main] DEBUG com.ibm.dbb.build.internal.DDProcessFactory - Enable SubProcess: false
[main] DEBUG com.ibm.dbb.build.internal.ProductRegistrationChecker - Checking product registration
[main] DEBUG com.ibm.dbb.build.internal.ProductRegistrationChecker - Registration product = {"productId":"5755-A01","featureName":"DBB","productName":"IBM APP DLIV FND"}
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(SYSIN) dsn(SUNGIK.TEST.ASM(HELLO)) shr
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(SYSPRINT) cyl space(5,5) unit(vio) blksize(80) lrecl(80) recfm(f,b) new
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(SYSUT1) cyl space(5,5) unit(vio) blksize(80) lrecl(80) recfm(f,b) new
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(SYSLIN) dsn(&&TEMPOBJ) cyl space(5,5) unit(vio) blksize(80) lrecl(80) recfm(f,b) new
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(SYSLIB) dsn(SUNGIK.TEST.MACRO) shr
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(TMP00001) dsn(SYS1.SCEEMAC) shr
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(TMP00002) dsn(SYS1.MACLIB) shr
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: concat ddlist(SYSLIB,TMP00001,TMP00002)
[main] DEBUG com.ibm.dbb.build.MVSExec - Executing 'ASMA90  ' with the following parameters: (LIST), DD names: (), tasklib: false
[main] DEBUG com.ibm.dbb.build.MVSExec - RC: 4
[main] DEBUG com.ibm.dbb.build.CopyToHFS - Copying SYSPRINT (encoding=IBM-1047) to '/u/sungik/ga1/work1/HELLO.log' (encoding=IBM-1047)
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(SYSIN)
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(SYSPRINT)
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(SYSUT1)
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(SYSLIB)
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(TMP00001)
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(TMP00002)
[main] DEBUG com.ibm.dbb.build.internal.ProductRegistrationChecker - Checking product registration
[main] DEBUG com.ibm.dbb.build.internal.ProductRegistrationChecker - Registration product = {"productId":"5755-A01","featureName":"DBB","productName":"IBM APP DLIV FND"}
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(SYSLMOD) dsn(SUNGIK.TEST.LOAD(HELLO)) shr
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(SYSPRINT) cyl space(5,5) unit(vio) blksize(80) lrecl(80) recfm(f,b) new
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(SYSUT1) cyl space(5,5) unit(vio) blksize(80) lrecl(80) recfm(f,b) new
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(SYSLIB) dsn(SUNGIK.TEST.OBJ) shr
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: alloc dd(TMP00003) dsn(SYS1.SCEELKED) shr
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: concat ddlist(SYSLIB,TMP00003)
[main] DEBUG com.ibm.dbb.build.MVSExec - Executing 'IEWBLINK' with the following parameters: (MAP,RENT,COMPAT(PM5)), DD names: (), tasklib: false
[main] DEBUG com.ibm.dbb.build.MVSExec - RC: 0
[main] DEBUG com.ibm.dbb.build.CopyToHFS - Copying SYSPRINT (encoding=IBM-1047) to '/u/sungik/ga1/work1/HELLO.log' (encoding=IBM-1047)
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(SYSLMOD)
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(SYSPRINT)
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(SYSUT1)
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(SYSLIB)
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(TMP00003)
[main] DEBUG com.ibm.dbb.build.MVSJob - Stopping MVS Job and free all DD allocated in this Job
[main] DEBUG com.ibm.dbb.build.internal.Utils - bpxwdyn: free dd(SYSLIN)
** Writing build report data to /u/sungik/ga1/work1/BuildReport.json
** Writing build report to /u/sungik/ga1/work1/BuildReport.html
** Build ended at Thu Mar 23 15:23:01 MDT 2023
** Build State : CLEAN
** Total files processed : 1
** Total build time  : 2.510 seconds


** Build finished
:/u/sungik:->

```
## Step 4: Verify Generated Datasets and members

Now your ASSEMBLER program hello.asm has been compiled, linked, and loaded.
You can view your Dataset using zoau API.
You also can see member generated for SUNGIK.TEST.ASM and SUNGIK.TEST.LOAD
```
:/u/sungik:->dls -lsub SUNGIK.TEST.*
SUNGIK.TEST.ASM                              2023/04/06 PO  FB      80 32720 TC791E           ??       849960
SUNGIK.TEST.DBRM                             2023/04/06 PO  FB      80 32720 TC791E           ??       849960
SUNGIK.TEST.LOAD                             2023/04/06 PO  U        0 32760 TC791E           ??       849960
SUNGIK.TEST.MACRO                            2023/04/06 PO  FB      80 32720 TC791E           ??       849960
SUNGIK.TEST.OBJ                              2023/04/06 PO  FB      80 32720 TC790D           ??       849960
:/u/sungik:->mls SUNGIK.TEST.ASM 
HELLO
:/u/sungik:->mls SUNGIK.TEST.LOAD 
HELLO
:/u/sungik:->

```
Let's see the contents of source ASSEMBLER code.
```
:/u/sungik:->dtail -n +1 "SUNGIK.TEST.ASM(HELLO)" 
HELLO    CSECT                                                                  
         USING *,15                                                             
         STM   14,12,12(13)                                                     
         LR    12,15                                                            
         USING HELLO,12                                                         
         WTO   'Hello ASM World!'                                               
         XR    15,15                                                            
         RETURN (14,12)                                                         
         END                                                                    
:/u/sungik:->

```

## Step 5: Submit JCL and see the WTO message from JES2::JESMSGLG

JCL can be viewed.
```
:/u/sungik:->pjdd JOB19944 JES2 JESJCL  
        1 //SUNGIK JOB 'WD-PKGBIND',MSGLEVEL=(1,1),MSGCLASS=R,NOTIFY=&SYSUID      JOB19944
          //*
          IEFC653I SUBSTITUTION JCL - 'WD-PKGBIND',MSGLEVEL=(1,1),MSGCLASS=R,NOTIFY=SUNGIK
        2 //RUN EXEC PGM=HELLO
        3 //STEPLIB  DD DISP=SHR,DSN=SUNGIK.DEMO1.LOAD
        4 //SYSIN    DD DUMMY
        5 //SYSPRINT DD SYSOUT=*
        6 //SYSUT1   DD DSN=SUNGIK.DATA.FILE.JCL,DISP=SHR
        7 //SYSUT2   DD SYSOUT=*
:/u/sungik:->
```
```
:/u/sungik:->ddls JOB19944
JES2     -        JESMSGLG UA         133        20   2
JES2     -        JESJCL   V          136         9   3
JES2     -        JESYSMSG VA         137        49   4
:/u/sungik:->pjdd JOB19944 JES2 JESMSGLG
1                   J E S 2  J O B  L O G  --  S Y S T E M  Z T C 3  --  N O D E  Z H 1 0 3 T C 1
0     
 17.06.08 JOB19944 ---- WEDNESDAY, 29 MAR 2023 ----
 17.06.08 JOB19944  IRR010I  USERID SUNGIK   IS ASSIGNED TO THIS JOB.
 17.06.09 JOB19944  ICH70001I SUNGIK   LAST ACCESS AT 17:04:09 ON WEDNESDAY, MARCH 29, 2023
 17.06.09 JOB19944  $HASP373 SUNGIK   STARTED - WLM INIT  - SRVCLASS BATCH    - SYS ZTC1
 17.06.09 JOB19944  IEF403I SUNGIK - STARTED - TIME=17.06.09
 17.06.09 JOB19944  +Hello ASSEMBLER World!
 17.06.09 JOB19944  GSDMV20I JOBNAME- --STEP-- -PSTEP-- CCODE ELAPSED-TIME -CPU-TIME-
 17.06.09 JOB19944  GSDMV21I SUNGIK   RUN                4048     00:00:00      0.01S
 17.06.09 JOB19944  IEF404I SUNGIK - ENDED - TIME=17.06.09
 17.06.09 JOB19944  GSDMV22I -SUNGIK   ENDED.  NAME-* NO NAME PROVIDED * TOTAL CPU TIME=   .00  TOTAL ELAPSED TIME=   .00
 17.06.09 JOB19944  $HASP395 SUNGIK   ENDED - RC=4048
0------ JES2 JOB STATISTICS ------
-  29 MAR 2023 JOB EXECUTION DATE
-            8 CARDS READ
-           82 SYSOUT PRINT RECORDS
-            0 SYSOUT PUNCH RECORDS
-           10 SYSOUT SPOOL KBYTES
-         0.00 MINUTES EXECUTION TIME
:/u/sungik:->pjdd JOB19944 JES2 JESMSGLG
1                   J E S 2  J O B  L O G  --  S Y S T E M  Z T C 3  --  N O D E  Z H 1 0 3 T C 1
0     
 17.06.08 JOB19944 ---- WEDNESDAY, 29 MAR 2023 ----
 17.06.08 JOB19944  IRR010I  USERID SUNGIK   IS ASSIGNED TO THIS JOB.
 17.06.09 JOB19944  ICH70001I SUNGIK   LAST ACCESS AT 17:04:09 ON WEDNESDAY, MARCH 29, 2023
 17.06.09 JOB19944  $HASP373 SUNGIK   STARTED - WLM INIT  - SRVCLASS BATCH    - SYS ZTC1
 17.06.09 JOB19944  IEF403I SUNGIK - STARTED - TIME=17.06.09
 17.06.09 JOB19944  +Hello ASSEMBLER World!
 17.06.09 JOB19944  GSDMV20I JOBNAME- --STEP-- -PSTEP-- CCODE ELAPSED-TIME -CPU-TIME-
 17.06.09 JOB19944  GSDMV21I SUNGIK   RUN                4048     00:00:00      0.01S
 17.06.09 JOB19944  IEF404I SUNGIK - ENDED - TIME=17.06.09
 17.06.09 JOB19944  GSDMV22I -SUNGIK   ENDED.  NAME-* NO NAME PROVIDED * TOTAL CPU TIME=   .00  TOTAL ELAPSED TIME=   .00
 17.06.09 JOB19944  $HASP395 SUNGIK   ENDED - RC=4048
0------ JES2 JOB STATISTICS ------
-  29 MAR 2023 JOB EXECUTION DATE
-            8 CARDS READ
-           82 SYSOUT PRINT RECORDS
-            0 SYSOUT PUNCH RECORDS
-           10 SYSOUT SPOOL KBYTES
-         0.00 MINUTES EXECUTION TIME
:/u/sungik:->pjdd JOB19944 JES2 JESYSMSG
 ICH70001I SUNGIK   LAST ACCESS AT 17:04:09 ON WEDNESDAY, MARCH 29, 2023
 IEFA111I SUNGIK IS USING THE FOLLOWING JOB RELATED SETTINGS:
          SWA=ABOVE,TIOT SIZE=64K,DSENQSHR=DISALLOW,GDGBIAS=JOB
 IEF236I ALLOC. FOR SUNGIK RUN
 IGD103I SMS ALLOCATED TO DDNAME STEPLIB
 IEF237I DMY  ALLOCATED TO SYSIN
 IEF237I JES2 ALLOCATED TO SYSPRINT
 IGD103I SMS ALLOCATED TO DDNAME SYSUT1
 IEF237I JES2 ALLOCATED TO SYSUT2
 Hello ASSEMBLER World!
 IEF142I SUNGIK RUN - STEP WAS EXECUTED - COND CODE 4048
 IGD104I SUNGIK.DEMO1.LOAD                            RETAINED,  DDNAME=STEPLIB
 IEF285I   SUNGIK.SUNGIK.JOB19944.D0000101.?            SYSOUT
 IGD104I SUNGIK.DATA.FILE.JCL                         RETAINED,  DDNAME=SYSUT1
 IEF285I   SUNGIK.SUNGIK.JOB19944.D0000102.?            SYSOUT
 ************************************************************************************************************************************
 * DDNAME   UNIT   EXCPS BLKSZ|DDNAME   UNIT   EXCPS BLKSZ|DDNAME   UNIT   EXCPS BLKSZ|DDNAME   UNIT   EXCPS BLKSZ                  *
 *----------------------------+---------------------------+---------------------------+---------------------------------------------*
 * STEPLIB  780E      10 32760|TOTAL:             10 *****|                           |                                             *
 ************************************************************************************************************************************
 IEF373I STEP/RUN     /START 2023088.1706
 IEF032I STEP/RUN     /STOP  2023088.1706
         CPU:     0 HR  00 MIN  00.00 SEC    SRB:     0 HR  00 MIN  00.00 SEC
         VIRT:     8K  SYS:   288K  EXT:        4K  SYS:    11212K
         ATB- REAL:                    28K  SLOTS:                     0K
              VIRT- ALLOC:      11M SHRD:       0M
 ************************************************************************************************************************************
 * z/OS    02.04.00 SYSTEM 3906       (ZTC1) :    SMF STATISTICS                                     DATE=2023-03-29  TIME=17:06:09 *
 ************************************************************************************************************************************
 *                                                                                                                                  *
 * //SUNGIK   JOB (WD-PKGBIND),                                                                                                     *
 *                                                                                                                                  *
 * STEP                               TOTAL     VIO      TCB      SRB    OTHER  ELAPSED   STORAGE     NO.OF  NO.OF  TIME ON  TIME ON*
 * NO. STEPNAME PROCSTEP PROGRAM     EXCP'S  EXCP'S CPU-TIME CPU-TIME CPU-TIME     TIME   <16M  >16M   PAGES SWAPS     ZIIP     ZAAP*
 *                                                                                                                                  *
 *   1 RUN               HELLO           10       0    0.00S    0.00S    0.01S    0.15S     8K    0M       0     0    0.00S    0.00S*
 *----------------------------------------------------------------------------------------------------------------------------------*
 * JOB SUM (WITH JOB OVERHEAD):          10       0    0.00S    0.00S    0.01S    0.15S     8K    0M       0     0    0.00S    0.00S*
 *==================================================================================================================================*
 * STORAGE SECTION | USED PRIVATE AREA BELOW 16MB:              8K | ALLOWED PRIVATE AREA BELOW 16MB:                         2048K *
 *                 | USED PRIVATE AREA ABOVE 16MB:              0M | ALLOWED PRIVATE AREA ABOVE 16MB:                           32M *
 *                 | USED VIRT. STORAGE ABOVE 2GB:             11M | ALLOWED VIRT. STORAGE ABOVE 2GB:                            3G *
 *----------------------------------------------------------------------------------------------------------------------------------*
 * WLM SECTION     | WORKLOAD NAME                        BATCH    | SERVICE CLASS NAME                                    BATCH    *
 *                 | RESOURCE GROUP NAME                           | REPORT CLASS NAME                                     BATCH    *
 ************************************************************************************************************************************
 IEF375I  JOB/SUNGIK  /START 2023088.1706
 IEF033I  JOB/SUNGIK  /STOP  2023088.1706
         CPU:     0 HR  00 MIN  00.00 SEC    SRB:     0 HR  

```
