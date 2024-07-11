# Running REXX program using Dependency Based Build (DBB)

Dependency Based Build (DBB) is an intelligent build technology that is used in a DevOps pipeline for building Mainframe applications. IBM Developer for z Systems® (IDz) with DBB is used in a DevOps pipeline along with an open source tool stack such as Git, Jenkins and Artifactory. While Git, Jenkins and Artifactory are used across the enterprise, DBB fills the gap to provide a build framework for mainframe objects.

A developer who is working on REXX applications can run a DBB user build to compile and link programs before the code is ready to be exposed to the repository for others to use. With user build, you can compile your program without having to perform commits or pushes. 

## Prerequsite

- zAppBuild

After installation, you must make sure your REXX properties is included by checking genapp/application-conf/application.properties.
Pay attention to the following statements.
```
# check REXX.properties is included for applicationPropFiles 
applicationPropFiles=file.properties,bind.properties,Assembler.properties,BMS.properties,Cobol.properties,LinkEdit.properties,bind.properties,PLI.properties,MFS.properties,PSBgen.properties,DBDgen.properties,ACBgen.properties,REXX.properties,ZunitConfig.properties,Transfer.properties,reports.properties,datasets.properties
  :

```
Make sure datasets.properties file includes REXX Compiler Data set
```
# REXX Compiler Data Sets. Example: REXX.V1R4.SFANLMD
SFANLMD=SYS1.REXX.SFANPRC
```
You can configure REXX specific parameters from genapp/application-conf/REXX.properties.
Make sure the following value is specified.
```
:
# default REXX compiler parameters
# can be overridden by file properties
rexx_compileParms=OBJECT PRINT XREF CEXEC
```
Make sure file.properties include REXX.groovy for scriptMapping.
```
dbb.scriptMapping = REXX.groovy :: **/*.rexx


```
- zoau - Z Open Automation Utilities



## Step 1: Generate a REXX source 

Write a simple REXX program and save with mycobol.cbl.

```
/* REXX sample program       */
/*    written by Sung-Ik Son */
ADDRESS TSO
say "Hi REXX today is" date()
Say 'Enter your Dataset to show the member ex:{HLQ}.REXX.REXX'
Pull ds .
Say "LISTDS '"ds"' MEMBERS"
"LISTDS '"ds"' MEMBERS"
Say "LISTC"
call outtrap out.
   address tso 'listc'
   do i=1 to out.0
      say out.i
   end
/* Call USS */
Say "Access z/OS UNIX ....display all files under /u/sungik"
Call syscalls 'ON'
address syscall 'readdir /u/sungik  sungik.'
DO i=1 to sungik.0
   say sungik.i
END
EXIT 0 
```
The simple REXX program will handle TSO and USS address to display member(s) from a given PDS Dataset and LISTC to display the MVS datasets. After switching to USS, it will display a certain directory.
The following output is expected after we complete DBB Build and the successful program run.

```
Hi REXX today is 20 Mar 2023
Enter your Dataset to show the member ex:{HLQ}.REXX.REXX
SUNGIK.REXX.REXX
LISTDS 'SUNGIK.REXX.REXX' MEMBERS
SUNGIK.REXX.REXX
--RECFM-LRECL-BLKSIZE-DSORG
  FB    80    32720   PO                                                                                          
--VOLUMES--
  TC791E
--MEMBERS--
  HELLO
  ISPF
  SYSCALL
LISTC
IN CATALOG:SYS1.UCAT.TCCLAS
SUNGIK.BUILD.COBOL
SUNGIK.DB2ADM.TABLES
SUNGIK.D079.T1210370.P4010062
SUNGIK.D079.T1423066.P40100AA
SUNGIK.EI.CUSTOM.LOG
SUNGIK.IMS.ACBLIB
SUNGIK.IMS.DBD
SUNGIK.IMS.DBDLIB
SUNGIK.IMS.OBJ
SUNGIK.IMS.PSB
SUNGIK.IMS.PSBLIB
SUNGIK.PLI.DBRM
SUNGIK.PLI.LOAD
SUNGIK.PLI.OBJ
SUNGIK.PLI.PLI
SUNGIK.PLI.PLI.INCLUDE
SUNGIK.PLIDB2.DBRM
SUNGIK.PLIDB2.LOAD
SUNGIK.PLIDB2.OBJ
SUNGIK.PLIDB2.PLI
SUNGIK.PLIDB2.PLI.INCLUDE
SUNGIK.REXX.CEXEC
SUNGIK.REXX.LOAD
SUNGIK.REXX.OBJ
SUNGIK.REXX.REXX
SUNGIK.ZH103TC1.PROFILE
Access z/OS UNIX ....display all files under /u/sungik
.
..
.sh_history
ga
.ssh
.bash_history
:
groovy_search.py
datasets
item_search.py
python_search.py
junk.properties
junk.log
cobol_item_search.py
property_item_search.py
migration-repo
test.rexx

```


## Step 2: Execute DDB User Build

```
HLQ=SUNGIK.REXX
fn="genapp/src/main/rexx/hello.rexx"
$DBB_HOME/bin/groovyz /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build.groovy --sourceDir /u/sungik/ga1 --workDir /u/sungik/ga1/work1 --hlq $HLQ -a  genapp  --userBuild  $fn

```

## Step 3: Verify the console log if the build is clean

```
** Build start at 20230313.012805.028
** Input args = --sourceDir /u/sungik/ga1 --workDir /u/sungik/ga1/work1 --hlq SUNGIK.REXX -a genapp --userBuild /u/sungik/ga1/genapp/src/main/rexx/hello.rexx
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/datasets.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/dependencyReport.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/Assembler.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/BMS.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/MFS.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/PSBgen.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/DBDgen.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/ACBgen.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/Cobol.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/LinkEdit.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/PLI.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/REXX.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/ZunitConfig.properties
** Loading property file /u/sungik/pipeline_tools/dbb-zappbuild/2.0.0/build-conf/Transfer.properties
** appConf = /u/sungik/ga1/genapp/application-conf
** Loading property file /u/sungik/ga1/genapp/application-conf/file.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/bind.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/Assembler.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/BMS.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/Cobol.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/LinkEdit.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/bind.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/PLI.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/MFS.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/PSBgen.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/DBDgen.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/ACBgen.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/REXX.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/ZunitConfig.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/Transfer.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/reports.properties
** Loading property file /u/sungik/ga1/genapp/application-conf/datasets.properties
java.version=8.0.7.20 - pmz6480sr7fp20-20221020_01(SR7 FP20)
java.home=/MTCS01/usr/lpp/java/J8.0_64
user.dir=/u/sungik
** Build properties at start up:
IBMZPLI_V51=
IBMZPLI_V52=SYS1.VAPLI.SIBMZCMP
MACLIB=SYS1.MACLIB
REFERAL=IMS.IMD1.REFERAL
SASMMOD1=SYS1.SASMMOD1
SBZUSAMP=
SCEELKED=SYS1.SCEELKED
SCEEMAC=SYS1.SCEEMAC
SCSQCOBC=SYS2.MQMM002.SCSQCOBC
SCSQLOAD=SYS2.MQMM002.SCSQLOAD
SDFHCOB=SYS2.CTS550C1.SDFHCOB
SDFHLOAD=SYS2.CTS550C1.SDFHLOAD
SDFHMAC=SYS2.CTS550C1.SDFHMAC
SDFSMAC=IMS.IMD1.SDFSMAC
SDFSRESL=IMS.IMD1.SDFSRESL
SDSNLOAD=SYS2.DSNDSA1.SDSNLOAD
SEQAMOD=SYS1.WSEDO.SEQAMOD
SFANLMD=SYS1.REXX.SFANPRC
SFELLOAD=SYS1.WSEDO.SFELLOAD
SIGYCOMP_V4=
SIGYCOMP_V6=SYS1.ADCOB.SIGYCOMP
:

rexxCopySearch=search:[SYSLIB:COPY]/u/sungik/ga1/?path=genapp/src/main/rexx/*.rexx
rexx_cexecDatasets=SUNGIK.REXX.CEXEC
rexx_cexecOptions=cyl space(1,1) lrecl(80) recfm(F,B) blksize(27920) DSORG(PO) dsntype(pds) dir(10)
rexx_cexecPDS=SUNGIK.REXX.CEXEC
rexx_cexec_deployType=CEXEC
rexx_compileMaxRC=4
rexx_compileParms=OBJECT PRINT XREF CEXEC
rexx_compileSyslibConcatenation=
rexx_compiler=REXXCOMP
rexx_dependenciesDatasetMapping=rexx_srcPDS::**/*
rexx_dependencySearch=search:[SYSLIB:COPY]/u/sungik/ga1/?path=genapp/src/main/rexx/*.rexx
rexx_deployType=CLIST
rexx_fileBuildRank=
rexx_impactPropertyList=rexx_compileParms
rexx_linkDebugExit=INCLUDE OBJECT(@{member})  \n    INCLUDE SYSLIB(EQAD3CXT)
rexx_linkEdit=true
rexx_linkEditMaxRC=4
rexx_linkEditParms=MAP,RENT,COMPAT(PM5)
rexx_linkEditStream=
rexx_linkEditSyslibConcatenation=
rexx_linkEditor=IEWBLINK
rexx_loadDatasets=SUNGIK.REXX.LOAD
rexx_loadOptions=cyl space(1,1) dsorg(PO) recfm(U) blksize(32760) dsntype(library)
rexx_loadPDS=SUNGIK.REXX.LOAD
rexx_objPDS=SUNGIK.REXX.OBJ
rexx_outputDatasets=SUNGIK.REXX.CEXEC,SUNGIK.REXX.LOAD
rexx_printTempOptions=cyl space(5,5) unit(vio) blksize(133) lrecl(133) recfm(f,b) new
rexx_requiredBuildProperties=rexx_srcPDS,rexx_objPDS,rexx_loadPDS,rexx_cexecPDS, rexx_compiler,rexx_linkEditor,rexx_tempOptions,   SFANLMD
rexx_resolutionRules=[${rexxRule}]
rexx_rexxPrintTempOptions=cyl space(5,5) unit(vio) blksize(121) lrecl(121) recfm(f,a) new
rexx_scanLoadModule=true
rexx_srcDatasets=SUNGIK.REXX.REXX,SUNGIK.REXX.OBJ
rexx_srcOptions=cyl space(1,1) lrecl(80) dsorg(PO) recfm(F,B) dsntype(library)
rexx_srcPDS=SUNGIK.REXX.REXX
rexx_tempOptions=cyl space(5,5) unit(vio) blksize(80) lrecl(80) recfm(f,b) new
:

** zAppBuild running on DBB Toolkit Version 2.0.0 28-Oct-2022 14:56:55 
required props = buildOrder,buildListFileExt
** Build output located at /u/sungik/ga1/work1
** Unable to verify collections. No metadata store.
** Adding /u/sungik/ga1/genapp/src/main/rexx/hello.rexx to Building build list
** Writing build list file to /u/sungik/ga1/work1/buildList.txt
genapp/src/main/rexx/hello.rexx
** Populating file level properties from individual property files.
** Invoking build scripts according to build order: BMS.groovy,MFS.groovy,Cobol.groovy,Assembler.groovy,PLI.groovy,LinkEdit.groovy,DBDgen.groovy,PSBgen.groovy,Transfer.groovy,REXX.groovy
** Building files mapped to REXX.groovy script
** REXX groovy **
required props = rexx_srcPDS,rexx_objPDS,rexx_loadPDS,rexx_cexecPDS, rexx_compiler,rexx_linkEditor,rexx_tempOptions,   SFANLMD
** Creating / verifying build dataset SUNGIK.REXX.REXX
** Creating / verifying build dataset SUNGIK.REXX.OBJ
** Creating / verifying build dataset SUNGIK.REXX.LOAD
** Creating / verifying build dataset SUNGIK.REXX.CEXEC
*** Building file genapp/src/main/rexx/hello.rexx
*** Resolution rules for genapp/src/main/rexx/hello.rexx:
search:[SYSLIB:COPY]/u/sungik/ga1/?path=genapp/src/main/rexx/*.rexx
*** Physical dependencies for genapp/src/main/rexx/hello.rexx:
** Writing build report data to /u/sungik/ga1/work1/BuildReport.json
** Writing build report to /u/sungik/ga1/work1/BuildReport.html
** Build ended at Mon Mar 13 13:28:08 MDT 2023
** Build State : CLEAN
** Total files processed : 1
** Total build time  : 2.722 seconds

** Build finished

```

You now can view your first REXX source from SUNGIK.REXX.REXX(HELLO).

```
:/u/sungik:->dtail -n +1 "SUNGIK.REXX.REXX(HELLO)" 
/* REXX sample program       */                                                 
/*    written by Sung-Ik Son */                                                 
ADDRESS TSO                                                                     
say "Hi REXX today is" date()                                                   
Say 'Enter your Dataset to show the member ex:{HLQ}.REXX.REXX'                  
Pull ds .                                                                       
Say "LISTDS '"ds"' MEMBERS"                                                     
"LISTDS '"ds"' MEMBERS"                                                         
Say "LISTC"                                                                     
call outtrap out.                                                               
   address tso 'listc'                                                          
   do i=1 to out.0                                                              
      say out.i                                                                 
   end                                                                          
/* Call USS */                                                                  
Say "Access z/OS UNIX ....display all files under /u/sungik"                    
Call syscalls 'ON'                                                              
address syscall 'readdir /u/sungik  sungik.'                                    
DO i=1 to sungik.0                                                              
   say sungik.i                                                                 
END                                                                             
EXIT 0                                                                          
:/u/sungik:->

```

## Step 5: Execute REXX interpreter program

TSO Execution
```
EXEC 'SUNGIK.REXX.REXX(HELLO)' EXEC
```

ISPF Option 6 

```
TSO EXEC 'SUNGIK.REXX.REXX(HELLO)' EXEC
```

## Step 5: Execute Compiled REXX program

CEXEC contains compiled exec code.
TSO Execution
```
EXEC 'SUNGIK.REXX.CEXEC(HELLO)' EXEC
```

ISPF Option 6 

```
TSO EXEC 'SUNGIK.REXX.CEXEC(HELLO)' EXEC
```

USS using tsocmd

```
:/u/sungik:->tsocmd exec "'SUNGIK.REXX.CEXEC(HELLO)'"
exec 'SUNGIK.REXX.CEXEC(HELLO)'
Hi REXX today is 22 Mar 2023
Enter your Dataset to show the member ex:{HLQ}.REXX.REXX
SYS1.REXX.SFANPRC
LISTDS 'SYS1.REXX.SFANPRC' MEMBERS
SYS1.REXX.SFANPRC
--RECFM-LRECL-BLKSIZE-DSORG
  FB    80    27920   PO                                                                                          
--VOLUMES--
  MTCS01
--MEMBERS--
  FANCMC  ALIAS(REXXC)
  FANCMCG  ALIAS(REXXCG)
  FANCMCL  ALIAS(REXXCL)
  FANCMCLG  ALIAS(REXXCLG)
  FANCMOEC  ALIAS(REXXOEC)
LISTC
IN CATALOG:SYS1.UCAT.TCCLAS
SUNGIK.ASSEM.ASM
SUNGIK.ASSEM.DBRM
SUNGIK.ASSEM.LOAD
SUNGIK.ASSEM.MACRO
SUNGIK.ASSEM.OBJ
SUNGIK.BUILD.COBOL
SUNGIK.DB2ADM.TABLES
SUNGIK.IMS.ACBLIB
SUNGIK.IMS.DBD
SUNGIK.IMS.DBDLIB
SUNGIK.IMS.OBJ
SUNGIK.IMS.PSB
SUNGIK.IMS.PSBLIB
SUNGIK.ISPFGWY.EXEC
SUNGIK.PLI.DBRM
SUNGIK.PLI.LOAD
SUNGIK.PLI.OBJ
SUNGIK.PLI.PLI
SUNGIK.PLI.PLI.INCLUDE
SUNGIK.PLIDB2.DBRM
SUNGIK.PLIDB2.LOAD
SUNGIK.PLIDB2.OBJ
SUNGIK.PLIDB2.PLI
SUNGIK.PLIDB2.PLI.INCLUDE
SUNGIK.PLITEST.DBRM
SUNGIK.PLITEST.LOAD
SUNGIK.PLITEST.OBJ
SUNGIK.PLITEST.PLI
SUNGIK.PLITEST.PLI.INCLUDE
SUNGIK.REXX.CEXEC
SUNGIK.REXX.LOAD
SUNGIK.REXX.OBJ
SUNGIK.REXX.REXX
SUNGIK.ZH103TC1.PROFILE
Access z/OS UNIX ....display all files under /u/sungik
.
..
.sh_history
ga
.ssh
.bash_history
dead.letter
tools-setup
.profile
curl
.gitconfig
deploy.yml
read_yaml.py
README.md
install-tools.sh
groovy_src
certs
local
.cache
true
temp_txt
.bashrc
work
testdrive.sh
vicmdfile
.exrc
mig
term.txt
.ascii
pipeline_tools
?
install-python.sh
test.sh
tar_create.sh
groovy_build.sh
.python_history
read_json.py
test.json
environment_zos
deployment_method.yml
test.groovy
read_ds.py
jinja2_example.py
EXAMPLE.JCL
search_ds.py
EXAMPLE.JCL.j2
plum.pax
newdir
olddir
pax_handle.sh
sungik-cio-mbss-sub
ga1
BLD.log
deploy_tar.sh
deployment_plan1.yml
list_ds.py
genapp
deploy_pre_tar.sh
na.artifactory.swg-devops.com.pem
deployment_plan.yml
pjdd
dbb-main.zip
jsub
evidences
print_format.py
createDS.py
deploy_after_tar.sh
list_ds_members.py
comp_members.py
cmp_members.sh
deployedDS.txt
environment_zos.yml
regex.py
:
main_dir
work1
groovy_search.py
datasets
item_search.py
python_search.py
junk.properties
junk.log
cobol_item_search.py
property_item_search.py
migration-repo
test.rexx
groovy_search_item.py
:/u/sungik:->
```