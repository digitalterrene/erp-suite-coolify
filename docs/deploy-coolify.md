# Deploy on Coolify

Developed against Coolify v4 (beta). Menu names may differ slightly in other versions.

## Prerequisites

- A server managed by Coolify, with ports 80 and 443 open.
- A hostname (for example `erp.example.com`) with a DNS **A record** pointing at that server's IP address.
  Create the record first. Coolify requests the HTTPS certificate when you set the domain, and that fails
  while DNS points elsewhere.
- A built image tag (see the README, step 1) that is readable by the server (public package, or registry login).

## Steps

1. **New resource.** In your project choose *Docker Compose Empty* and paste in `compose.yaml`.
2. **Variables.** Set `SITE_NAME`, `ERPNEXT_IMAGE` and `ERPNEXT_TAG` (see the README table).
   Leave **Connect To Predefined Network** unticked.
3. **Deploy.** Coolify generates `SERVICE_PASSWORD_DBROOT`, `SERVICE_PASSWORD_ADMIN` and a placeholder
   `*.sslip.io` address for the frontend.
4. **Watch `create-site`.** Its logs show each app being installed. The first run takes several minutes.
   It ends with `Site ... created` and then shows as *Exited*. That is normal for run-once containers.
5. **Set the domain.** Open the `frontend` service, set Domains to `https://<SITE_NAME>:8080`, save and restart.
   While testing you can keep the generated address as well, separated by a comma.
6. **Log in** at `https://<SITE_NAME>` as `Administrator` with the `SERVICE_PASSWORD_ADMIN` value from the
   Environment Variables tab.
7. **Verify.** Open `/desk`, `/crm`, `/helpdesk` and `/insights`. In the `backend` terminal run
   `bench version` and confirm every app is listed.
8. **Configure email** ([email.md](email.md)) and **backups** ([backups.md](backups.md)) before real use.

## Notes

- The site name must equal the hostname users browse to. It is fixed when the site is created.
- If you change `SITE_NAME` later, nothing renames the existing site. Start from fresh volumes instead.
- If an app fails to install, the site exists with the apps before it. Retry in the `backend` terminal with
  `bench --site <SITE_NAME> install-app <app>`, and open an issue with the error.
