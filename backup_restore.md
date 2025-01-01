# Backup Restoration Policy

## Introduction

This document outlines the steps to follow in case of data loss. 

**Note:** Only authorized personnel are allowed to perform the actions described below.

## Backup Locations and Procedures

The Ansible repository is used to configure virtual machines (VMs) specified in the `hosts` file. To configure the VMs, run:

```bash
ansible-playbook infra.yaml
```

### Backup Tasks
- **MySQL** and **InfluxDB** backups are defined in:
  - `roles > mysql > files > cron-mysql-backup`
  - `roles > influxdb > files > cron-influxdb-backup`

Backups include **full** and **incremental** types:
- **MySQL**: Exports the `agama` database.
- **InfluxDB**: Exports the `telegraf` database.

#### **Dump Schedules**
- **MySQL dump**: Every day at `20:00 UTC`.
  - Location: `/home/backup/mysql/`
- **InfluxDB dump**: Every day at `21:00 UTC`.
  - Location: `/home/backup/influxdb/`

#### **Backup Destinations**
Backups are saved locally and uploaded to a remote backup server.

### Full Backups
- **MySQL full backup**: Every Sunday at `20:15 UTC`.
- **InfluxDB full backup**: Every Sunday at `21:15 UTC`.
- **Remote Destination**:
  - MySQL: `/home/akhkusu/mysql`
  - InfluxDB: `/home/akhkusu/influxdb`

### Incremental Backups
- **MySQL incremental backup**: Daily except Sunday at `20:20 UTC`.
- **InfluxDB incremental backup**: Daily except Sunday at `21:20 UTC`.

Incremental backups are stored in the same remote destinations as full backups.

## Important Notes Before Starting Recovery

1. **Check Recovery File Directories**:
   - Ensure `/home/backup/restore/*` directories are empty before copying data from the backup server. Existing files will cause errors.

2. **SSH Key Propagation**:
   - If VMs were configured recently, the backup server might not recognize the new public SSH keys immediately. 
   - This issue resolves automatically within approximately **20 minutes** once the key is added to the `authorized_keys` file on the backup server.

## Recovery Procedures

### **MySQL Recovery Steps**

1. Copy the backup file to `/home/backup/restore/mysql` on the second VM:
    ```bash
    sudo -u backup duplicity --no-encryption restore rsync://akhkusu@backup.ak.io/mysql /home/backup/restore/mysql
    ```
2. Restore the `agama` database:
    ```bash
    sudo mysql agama < /home/backup/restore/mysql/agama.sql
    ```
3. **Fallback Option**:
   - If the backup server is inaccessible, check if `agama.sql` exists locally in `/home/backup/mysql/`.

4. Verify recovery:
   - Confirm successful restoration by checking the items on the Agama website.

---

### **InfluxDB Recovery Steps**

1. Copy the backup file to `/home/backup/restore/influxdb` on the second VM:
    ```bash
    sudo -u backup duplicity --no-encryption restore rsync://akhkusu@backup.ak.io/influxdb /home/backup/restore/influxdb
    ```
2. Stop the `telegraf` service:
    ```bash
    service telegraf stop
    ```
3. Drop the existing `telegraf` database:
    ```bash
    influx -execute 'DROP DATABASE telegraf'
    ```
4. Restore the database:
    ```bash
    sudo influxd restore -portable -database telegraf /home/backup/restore/influxdb
    ```
   - **Note:** Ignore errors like:
     ```
     error updating meta: DB metadata not changed. database may already exist
     restore: DB metadata not changed. database may already exist
     ```

5. Verify recovery:
   - Check the `Syslog` dashboard in Grafana to confirm the restoration.

---

## Final Steps

After completing the recovery, execute:
```bash
ansible-playbook infra.yaml
```
This ensures consistency across VMs and resolves any potential issues.
```