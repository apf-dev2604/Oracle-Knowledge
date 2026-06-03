Skip to content
apf-dev2604
Oracle-Knowledge
Repository navigation
Code
Issues
Pull requests
Actions
Projects
Wiki
Security and quality
Insights
Settings
Commit 3c784b5
apf-dev2604
apf-dev2604
authored
13 minutes ago
Verified
Create rmanDailyRefresh
main
1 parent 
00a0d32
 commit 
3c784b5
1 file changed

+21
Lines changed: 21 additions & 0 deletions
File tree
Filter files…
rmanDailyRefresh
Search within code
 
Customizable line height
The default line height has been increased for improved accessibility. You can choose to enable a more compact line height from the view settings menu.

‎rmanDailyRefresh‎
+21
Lines changed: 21 additions & 0 deletions
Original file line number	Diff line number	Diff line change
@@ -0,0 +1,21 @@
The script is maintaining Oracle Standby Database using python script. 
This contains the python scripts that reads oracle-archive log file 
in S3 bucket and perform and recovery based on the next System Change Number(SCN). 
This will execute the following tasks
a. Check current state of Oracle Standby database restrart the database to MOUNT mode
b. Check and Copy archived log tar.gz file in s3 bucket and copy to local storage
c. Decompress archivelog tar file
d. Register possible candidate archive log file and catalog the file using RMAN.
e. Check and Idenitfy and Display
  * CUrrent SCN 
  * Next or MAX SCN after recovery
  Note: Also checks what has been applied or good archive log file for recovery
f. Perform recovery 
g. Deregister archive log file in RMAN and delete it logically
h. Delete physical archive log files creaeted in step "b"
i. Start the database in OPEN READ ONLY mode
Note: Donot open Database in OPENRESETLOGS mode retain the original DB INCARNATE or the apply 
new changes/updaets from backup archivelogs
0 commit comments
Comments
0
 (0)
Comment
You're not receiving notifications from this thread.

