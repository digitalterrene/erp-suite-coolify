# Backups and restore

> Status: the commands below are standard Frappe tooling, but the full procedure has **not yet been verified
> on this stack**. Run a restore test before relying on it.

## What needs protecting

| Data | Where it lives |
|---|---|
| Database (all business records) | `db-data` volume (MariaDB) |
| Uploaded files and attachments | `sites` volume (`sites/<site>/public` and `private`) |
| Encryption key and site settings | `sites/<site>/site_config.json` |

## 1. Database backups with Coolify

The `db` service has a **Backups** option in Coolify. Schedule a daily backup and send it to an S3-compatible
storage you configured under **S3 Storages**.

## 2. Site backups (database, files and site config)

Add a **Scheduled Task** in Coolify on the `backend` container, for example daily at 02:00:

```
bench --site <SITE_NAME> backup --with-files
```

Backups are written inside the `sites` volume, under `sites/<site>/private/backups`.

## 3. Get a copy off the server

Backups on the same disk are not disaster recovery. Copy them elsewhere, for example with server snapshots from
your hosting provider, or another tool that copies the volume to remote storage.

## 4. Test a restore

Do this on a throwaway stack with fresh volumes, using the backup files from step 2:

```
bench --site <SITE_NAME> restore <database.sql.gz> --with-public-files <files.tar> --with-private-files <private-files.tar>
```

You will need the database root password (`SERVICE_PASSWORD_DBROOT`). Then log in and open a few records and
attachments. Restore the site config backup as well, or stored passwords such as the email password will not
decrypt.
