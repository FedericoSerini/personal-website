---
title: Databases
weight: 3
prev: /docs/infrastructure/monitoring/
next: /docs/infrastructure/storage/
---

## MariaDB

This database is installed by using the [MariaDB operator](https://github.com/mariadb-operator/mariadb-operator/tree/main)

### Hosted applications

* [Bookstack](https://federicoserinidev.com/docs/apps/bookstack)

### Disaster Recovery

Every first day of the month, a backup job is triggered by using the [Backup resource](https://github.com/FedericoSerini/homelab/blob/main/databases/data/bookstack/backup.yaml), which creates a [CronJob](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/) on the cluster. The data are uploaded to AWS S3 through the operator. The AWS user and the attached policies are completely isolated and reflect the ['Least privilege'](https://en.wikipedia.org/wiki/Principle_of_least_privilege) principle. However, when needed, I perform a manual backup by issuing this command.

```shell
kubectl create job --from=cronjob/mariadb-backup backup-job-manual --namespace bookstack-mariadb
```

---

## PostgreSQL

This database is installed by using the using the [Cloudnative PG Operator](https://github.com/cloudnative-pg/cloudnative-pg)

### Hosted applications

* [Linkding](https://federicoserinidev.com/docs/apps/linkding)
* [Mealie](https://federicoserinidev.com/docs/apps/mealie)

### Disaster Recovery

Actually there is no automatic backup, the backup is performed by creating a [Backup](https://github.com/FedericoSerini/homelab/blob/main/databases/data/linkding/manual_backup.yaml) resource on the cluster. Just like the MariaDB database the backup are stored in AWS S3. However, it is already setted up for automatic recovery via [bootstrap spec](https://github.com/FedericoSerini/homelab/blob/main/databases/data/linkding/cluster.yaml#L17)
