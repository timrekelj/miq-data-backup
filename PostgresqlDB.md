# Dump and restore Postgresql database

ManageIQ uses PostgreSQL Database. So while dealing with ManageIQ database we’ll need to follow PostgreSQL commands and procedure to take a database dump as a backup and to restore it in another instance.

> **Note: We will backup manageIQ “vmdb_development” database**
---

> **This is list of steps followed to obtain a dump from a db then restore it**

## Dump the Postgres DB:

1. Stop the appliance(s):

`systemctl stop evmserverd`

> **This will stop the evmserverd.service from using database in background but no need to stop them, for creating backup it does not affect anything**
> 
1. Dump the database into file:

`pg_dump -Fc vmdb_development > development.dump`

> *If you get output similar to this*
> ```bash
> pg_dump: [archiver (db)] connection to database "vmdb_development" failed: connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: FATAL: role "<your_username>" does not exist
> ```
> *you can add `-U root` to every postgresql command (in postgresql installation for ManageIQ you define root as the user of ManageIQ databases).* 

## Import the Postgresql dump

1. Take `development.dump` to machine where you want database to be restored.
2. Stop the backend processes

`systemctl stop evmserverd`

3. Drop existing database `vmdb_development`

`dropdb vmdb_development`

4. Create new database named `vmdb_development`

`createdb vmdb_development`

5. restore the database from dump file

`pg_restore -d vmdb_development "path/to/development.dump"`

6. Run `fix_auth.rb`

`bundle exec tools/fix_auth.rb --v2 vmdb_development`

7. Start evm service

`systemctl start evmserverd`

This will do the task of restoring database automatically