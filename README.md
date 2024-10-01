
                                      QUERIES
                                    ----------
 
 Microsoft Windows [Version 10.0.19045.4894]
(c) Microsoft Corporation. All rights reserved.

C:\Users\hp i5>sqlplus sys as SYSDBA <!-- I opened SQL*Plus to connect to the Oracle Database as the SYS user with DBA privileges-->

SQL*Plus: Release 21.0.0.0.0 - Production on Tue Oct 1 08:23:50 2024
Version 21.3.0.0.0

Copyright (c) 1982, 2021, Oracle.  All rights reserved.

Enter password:

Connected to:
Oracle Database 21c Express Edition Release 21.0.0.0.0 - Production
Version 21.3.0.0.0

SQL> SHOW USER;  <!--I therefore wanted to check which user I was logged in as (for just assurance) -->
USER is "SYS" <!--The result indicated that i was connected as the "SYS" user-->
SQL> SHOW PDBS; <!--I listed all available pluggable databases and the output displayed two existing pluggable databases -->

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 XEPDB1                         READ WRITE NO

<!-- I wanted to determine the correct file paths and file naming conventions for my new PDB as well 
as ensures that the new PDB's data files are properly stored without conflicting with existing PDBs or files.-->
SQL> SELECT CON_ID, TABLESPACE_NAME, FILE_NAME
  2  FROM CDB_DATA_FILES
  3  WHERE CON_ID = 3;

    CON_ID TABLESPACE_NAME
---------- ------------------------------
FILE_NAME
--------------------------------------------------------------------------------
         3 SYSTEM
C:\ORACLE\PRODUCT\21C\DBHOME_1\ORADATA\XE\XEPDB1\SYSTEM01.DBF

         3 SYSAUX
C:\ORACLE\PRODUCT\21C\DBHOME_1\ORADATA\XE\XEPDB1\SYSAUX01.DBF

         3 UNDOTBS1
C:\ORACLE\PRODUCT\21C\DBHOME_1\ORADATA\XE\XEPDB1\UNDOTBS01.DBF


    CON_ID TABLESPACE_NAME
---------- ------------------------------
FILE_NAME
--------------------------------------------------------------------------------
         3 USERS
C:\ORACLE\PRODUCT\21C\DBHOME_1\ORADATA\XE\XEPDB1\USERS01.DBF

<!--I therefore went a head and created a new pluggable database named plsql_class2024db and also specified:
An admin user OL_PLSQLAUCA with the password 123. with a file path conversion for storing the new pluggable database's 
files from the seed database.-->
SQL> CREATE PLUGGABLE DATABASE plsql_class2024db
  2  ADMIN USER OL_PLSQLAUCA IDENTIFIED BY 123
  3  FILE_NAME_CONVERT = ('C:\oracle\product\21c\dbhome_1\oradata\XE\pdbseed\','C:\oracle\product\21c\dbhome_1\oradata\XE\PLSQL_CLASS2024DB\');

Pluggable database created.
<!--After creating the plsql_class2024db PDB, I opened it to allow it to be used.-->
SQL>  ALTER PLUGGABLE DATABASE PLSQL_CLASS2024DB OPEN; 

Pluggable database altered.
<!--I ran the SHOW PDBS command again to confirm that the new pluggable database plsql_class2024db was successfully created and is in "READ WRITE" mode. The output now showed three databases:-->
SQL> SHOW PDBS;

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 XEPDB1                         READ WRITE NO
         4 PLSQL_CLASS2024DB              READ WRITE NO

 <!--I created another pluggable database named OL_to_delete_pdb with an admin user OL_DELETER and password 123-->        
SQL> CREATE PLUGGABLE DATABASE OL_to_delete_pdb
  2  ADMIN USER OL_DELETER IDENTIFIED BY 123
  3  FILE_NAME_CONVERT = ('C:\oracle\product\21c\dbhome_1\oradata\XE\pdbseed\','C:\oracle\product\21c\dbhome_1\oradata\XE\OL_TO_DELETE_PDB\');

Pluggable database created.
<!--I therefore opened it to verify that the PDB was successfully created and can be accessed -->
SQL> ALTER PLUGGABLE DATABASE OL_TO_DELETE_PDB OPEN;

Pluggable database altered.
<!--I run the CLOSE IMMEDIATE command to shut down the pdb immediately because 
Oracle requires that a PDB must be in a closed state before it can be dropped..-->
SQL> ALTER PLUGGABLE DATABASE OL_TO_DELETE_PDB CLOSE IMMEDIATE;

Pluggable database altered.
<!--lastly, I dropped the OL_to_delete_pdb pluggable database. I included the INCLUDING DATAFILES 
option to remove the associated files from the filesystem as well.-->
SQL> DROP PLUGGABLE DATABASE OL_TO_DELETE_PDB INCLUDING DATAFILES;

Pluggable database dropped.

SQL>
 
