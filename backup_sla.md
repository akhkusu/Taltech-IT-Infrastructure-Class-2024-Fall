# Backup Policy and SLA

## Purpose
This SLA outlines the backup and recovery strategy for critical services managed within the Ansible repository. These services include:
- **Configurable services**: nginx, uwsgi, bind9, agama, prometheus, pinger, grafana.
- **Recoverable services**: MySQL, InfluxDB.

## Scope
- **Configurable services**: Configurations can be restored using the Ansible repository.
- **Recoverable services**: Full data backups are created and stored in multiple locations to ensure data integrity.

---

## Backup Objectives

### Recovery Point Objective (RPO)
- The RPO is **24 hours**, ensuring data generated within the past day is preserved in case of a disaster.

### Recovery Time Objective (RTO)
- The RTO is **2 hours**, targeting the complete restoration of services and data within this period.

---

## Backup Schedule and Retention

- **MySQL Full Backup**: Sundays at 20:15 UTC  
- **InfluxDB Full Backup**: Sundays at 21:15 UTC  
- **MySQL Incremental Backup**: Daily (excluding Sunday) at 20:20 UTC  
- **InfluxDB Incremental Backup**: Daily (excluding Sunday) at 21:20 UTC  

Backups are retained for **6 months** to ensure long-term data recovery capabilities. Each backup is timestamped and labeled for easy identification.

---

## Usability Checks
Backup integrity checks are conducted every **14 days**. These tests include:  
- Verifying backup files.  
- Restoring data to test environments.  
- Validating the integrity of recovered data.  

Issues identified during these checks are resolved immediately to maintain reliability.

---

## Restoration Process

1. **Configurable Services**:  
   Restored by re-running the Ansible repository.  

2. **Recoverable Services (MySQL, InfluxDB)**:  
   Follow detailed steps in the `backup_restore.md` document for data recovery.

---
