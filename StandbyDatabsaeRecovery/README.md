# Standby RMAN Archive-Log Refresh Refactor

This package splits the original single Python script into portable components:

```text
main.py                         # CLI entrypoint only
config.ini                      # tunable parameters
helpers/standbyengine.py         # refresh orchestration/business logic
helpers/dbManager.py             # Oracle SQL*Plus and RMAN execution
helpers/s3archivelog.py          # copy/extract/stage archive-log bundles
helpers/mailer.py                # SMTP error notification
helpers/messager.py              # webhook text notification
queries/standbydb.sql            # SQL statements loaded by name at runtime
```

## Run

```bash
cd standby_refresh_refactor
python3 main.py 2026_05_25 0 --config config.ini
```

## Notes

- `helpers/dbManager.py` is the only class that executes SQL*Plus and RMAN.
- `helpers/standbyengine.py` calls `DbManager`; it does not directly run subprocess Oracle commands.
- `queries/standbydb.sql` is loaded only when `DbManager` is initialized, then each named query is called by the engine.
- `helpers/s3archivelog.py` supports a mounted S3 path such as `/mnt/s3bucket/RMAN/`. If `s3_base` starts with `s3://`, it calls `aws s3 cp`, so the AWS CLI must be configured on the host.
- `helpers/messager.py` works with Slack/Teams/Discord-style webhook endpoints. It uses `requests` if installed, otherwise falls back to Python standard-library `urllib`.

## Recommended architecture

This is a good production direction: keep orchestration, database execution, external file staging, notifications, SQL text, and configuration separated. For Oracle/RMAN automation, the main additional best practice is to keep `DbManager` as the single execution boundary so audit logging, error handling, timeouts, and security checks are centralized.





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




