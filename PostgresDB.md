# Dump and restore Postgresql database

ManageIQ uses PostgreSQL Database. So while dealing with ManageIQ database we’ll need to follow PostgreSQL commands and procedure to take a database dump as a backup and to restore it in another instance.

> **Note: ManageIQ uses “vmdb_production” database**

---

> **This is list of steps followed to obtain a dump from a db then restore it**

## Dump the Postgres DB:

1. Stop the appliance(s):

`systemctl stop evmserverd`

> **This will stop the evmserverd.service from using database in background but no need to stop them, for creating backup it does not affect anything**

1. Dump the database into file:

`pg_dump -Fc vmdb_production > production.dump`

1. Copy the dump file to the other ManageIQ instance where we want to restore the database. We can use `scp` for that as follows:

 `scp production.dump root@ip_of_other_instance:/root/`

## Import the Postgres dump

1. Take `production.dump` to the local machine.
2. Stop the backend processes

`systemctl stop evmserverd`

1. Drop existing database `vmdb_production`

`dropdb vmdb_production`

1. Create new database named `vmdb_production`

`createdb vmdb_production`

1. restore the database from dump file

`pg_restore -d vmdb_production "path/to/production.dump"`

1. Change directory to `/var/www/miq/vmdb/tools`

```yaml
vmdb  #executing "vmdb" will take you to "/var/www/miq/vmdb/"

cd tools

bundle exec fix_auth.rb --v2 --invalid bogus
```

1. Start evm service

`systemctl start evmserverd`

This will do the task of restoring database automatically