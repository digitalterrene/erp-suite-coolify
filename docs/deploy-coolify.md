# Deploy on Coolify

Developed against Coolify v4 (beta). Menu names may differ slightly in other versions.

## Prerequisites

- A server managed by Coolify, with ports 80 and 443 open.
- A hostname (for example `erp.example.com`) with a DNS **A record** pointing at that server's IP address.
  Create the record first. Coolify requests the HTTPS certificate when you set the domain, and that fails
  while DNS points elsewhere.
- A built image tag (see the README, step 1) that is readable by the server (public package, or registry login).

## Steps

1. **New resource.** In your project choose *Docker Compose Empty* and paste in `compose.yaml`. Click **Save**.
2. **Variables.** Set `SITE_NAME`, `ERPNEXT_IMAGE` and `ERPNEXT_TAG` (see the README table).
   Leave **Connect To Predefined Network** unticked.
3. **Set the domain before the first deploy.** Open the `frontend` service and set Domains to
   `https://<SITE_NAME>:8080`, then save. Coolify has already generated `SERVICE_PASSWORD_DBROOT`,
   `SERVICE_PASSWORD_ADMIN` and a placeholder `*.sslip.io` address. The `:8080` only selects the container port.
4. **Deploy once, then wait.** Do **not** restart, redeploy, stop or change settings until `create-site` has
   finished. It installs every app one at a time and takes several minutes. Watch its logs for the final line
   `Done. Site ... is ready.` After that the container shows as *Exited*, which is normal for a run-once container.
   While apps are being installed, the site shows a maintenance message. That is intentional: it keeps visitors
   and background jobs away from the database. It switches off by itself when `create-site` finishes.
5. **Log in** at `https://<SITE_NAME>` as `Administrator` with the `SERVICE_PASSWORD_ADMIN` value from the
   Environment Variables tab.
6. **Verify.** Open `/desk` and confirm the app switcher lists ERPNext, HR, CRM, Helpdesk and Insights, then open
   `/crm`, `/helpdesk` and `/insights`. In the `backend` terminal run `bench version` and confirm every app is listed.
7. **Configure email** ([email.md](email.md)) and **backups** ([backups.md](backups.md)) before real use.

## Notes

- The site name must equal the hostname users browse to. It is fixed when the site is created.
- If you change `SITE_NAME` later, nothing renames the existing site. Start from fresh volumes instead.
- `create-site` is safe to run again. On every start it installs any requested app that is missing, so if a run
  was interrupted, redeploying completes it. If it reports the site is "not usable", the site was only half
  created. On a fresh install, delete the stack including its volumes and deploy again.
- If an app fails to install, `create-site` stops with that app's error. Open an issue with the log.