# PostgreSQL Lab Assignment: Backups, WAL Archiving, PITR, and Streaming Replication

This repository contains the configuration files, backup artifacts, and documentation for the PostgreSQL database management lab completed in a WSL Ubuntu environment.

---

## Lab Overview & Steps Completed

### 1. Logical Backups (`pg_dump`)
- **Database Name:** `bootcamp`
- **Backup File:** `backups/bootcamp.dump`
- **Description:** Created and verified a custom-format logical backup of the database containing student records and test tables.

### 2. WAL Archiving & Physical Base Backup (`pg_basebackup`)
- **Configuration:** 
  - `wal_level = replica`
  - `archive_mode = on`
  - `archive_command = 'cp %p /home/PACH/backups/wal/%f'`
- **Base Backup File:** `backups/base.tar.gz`
- **Description:** Enabled Write-Ahead Logging (WAL) archiving and captured a full physical base backup via Unix socket authentication.

### 3. Point-in-Time Recovery (PITR)
- **Disaster Simulation:** Dropped records using `DELETE FROM students;` at a recorded timestamp (`2026-10-07 12:05:39`).
- **Recovery Method:** Restored the physical base backup, placed a `recovery.signal` file, and configured `restore_command` and `recovery_target_time` in `postgresql.conf`.
- **Verification:** Successfully recovered the database to the target point in time and verified row counts.

### 4. Streaming Replication Standby
- **Primary Configuration:** 
  - Created a replication role (`replicator`) with `REPLICATION` privileges.
  - Updated `pg_hba.conf` to allow MD5-authenticated replication connections from `127.0.0.1/32`.
  - Configured `max_wal_senders = 10` and `wal_keep_size = 64MB`.
- **Standby Configuration:** 
  - Created a `standby.signal` file in the data directory.
  - Added `primary_conninfo` pointing to the primary instance with application name `standby1`.
- **Verification:** 
  - Confirmed the instance runs in recovery mode (`SELECT pg_is_in_recovery();` returns `t`).
  - Tested real-time synchronization by inserting a record on the primary and verifying its appearance on the standby replica.

---

## Repository Structure

```text
postgres-lab-submission/
├── backups/
│   ├── bootcamp.dump        # Logical backup file
│   └── base.tar.gz          # Physical base backup archive
├── configs/
│   ├── postgresql.conf      # Modified PostgreSQL server configuration
│   └── pg_hba.conf          # Client authentication configuration
└── README.md                # Lab documentation and submission record

