PROJECT 2: AUTOMATED DATABASE BACKUP & RECOVERY SYSTEM (POSTGRESQL)

====================================================
1. PROJECT OVERVIEW
====================================================

Tujuan:
- Melakukan backup database PostgreSQL secara otomatis.
- Menyimpan file backup dengan timestamp.
- Menghapus backup lama secara otomatis.
- Mendukung proses restore database.
- Menyimpan log hasil backup.

====================================================
2. SYSTEM ARCHITECTURE
====================================================

PostgreSQL Database
        |
        V
Backup Script (Shell/Python)
        |
        V
Backup Folder
        |
        +-- Daily Backup
        +-- Weekly Backup
        +-- Monthly Backup
        |
        V
Recovery Process

====================================================
3. DATABASE INFORMATION
====================================================

Database Name : sales_db
Username      : postgres
Host          : localhost
Port          : 5432

====================================================
4. BACKUP DIRECTORY STRUCTURE
====================================================

backup_system/

|-- backups/
|   |-- daily/
|   |-- weekly/
|   |-- monthly/
|
|-- logs/
|   |-- backup.log
|
|-- backup.sh

====================================================
5. BACKUP SCRIPT (backup.sh)
====================================================

#!/bin/bash

DB_NAME="sales_db"
DB_USER="postgres"

BACKUP_DIR="./backups/daily"
LOG_FILE="./logs/backup.log"

DATE=$(date +"%Y%m%d_%H%M%S")

mkdir -p $BACKUP_DIR
mkdir -p ./logs

pg_dump -U $DB_USER $DB_NAME > $BACKUP_DIR/${DB_NAME}_${DATE}.sql

echo "$(date): Backup completed" >> $LOG_FILE

====================================================
6. GIVE EXECUTE PERMISSION
====================================================

chmod +x backup.sh

Run Script:

./backup.sh

====================================================
7. COMPRESSED BACKUP VERSION
====================================================

#!/bin/bash

DB_NAME="sales_db"
DB_USER="postgres"

DATE=$(date +"%Y%m%d_%H%M%S")

pg_dump -U $DB_USER $DB_NAME | gzip > backups/daily/${DB_NAME}_${DATE}.sql.gz

====================================================
8. AUTOMATED SCHEDULING USING CRON
====================================================

Open Cron:

crontab -e

Backup every day at 01:00 AM:

0 1 * * * /home/user/backup_system/backup.sh

====================================================
9. REMOVE OLD BACKUPS
====================================================

find backups/daily -name "*.sql.gz" -mtime +30 -delete

====================================================
10. RECOVERY PROCESS
====================================================

Restore SQL File:

psql -U postgres sales_db < sales_db_20260623_010000.sql

Restore Compressed Backup:

gunzip -c sales_db_20260623_010000.sql.gz | psql -U postgres sales_db

====================================================
11. BACKUP VERIFICATION
====================================================

Check SQL File:

head sales_db_20260623_010000.sql

Check Dump File:

pg_restore --list backup_file.dump

====================================================
12. LOGGING SYSTEM
====================================================

Example Log:

2026-06-23 01:00:02 Backup completed
2026-06-24 01:00:01 Backup completed
2026-06-25 01:00:03 Backup completed

Logging Command:

echo "$(date): Backup completed successfully" >> logs/backup.log

====================================================
13. PYTHON VERSION
====================================================

import os
from datetime import datetime

db_name = "sales_db"
user = "postgres"

timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")

backup_file = f"backups/daily/{db_name}_{timestamp}.sql"

os.system(
    f'pg_dump -U {user} {db_name} > {backup_file}'
)

print("Backup completed")

====================================================
14. DISASTER RECOVERY PROCEDURE
====================================================

Scenario 1: Table Deleted

psql -U postgres sales_db < backup.sql

Scenario 2: Database Corrupted

DROP DATABASE sales_db;
CREATE DATABASE sales_db;

Restore:

psql -U postgres sales_db < backup.sql

Scenario 3: Server Failure

1. Install PostgreSQL
2. Create Database
3. Copy Backup Files
4. Restore Database

====================================================
15. SECURITY CONSIDERATIONS
====================================================

- Store backups on another server.
- Encrypt backup files.
- Restrict access to backup folders.
- Use a dedicated PostgreSQL backup account.
- Upload backups to cloud storage.

Example:

chmod 700 backups

====================================================
16. EXPECTED OUTCOME
====================================================

[OK] Automatic daily backup
[OK] Timestamped backup files
[OK] Automatic deletion of old backups
[OK] Database recovery capability
[OK] Backup activity logs
[OK] Disaster Recovery Plan support

TOOLS USED

- PostgreSQL
- pg_dump
- psql
- Bash Script
- Cron Scheduler
- Python (Optional)

END OF DOCUMENT
