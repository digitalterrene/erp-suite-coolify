# Troubleshooting

## "no available server" in the browser

This is the Coolify proxy (Traefik) answering. The request reached a proxy, but no healthy container is
routed for that hostname. Check, in this order:

1. **Domain not set on the frontend.** In Coolify, the `frontend` service's Domains field must contain
   `https://<SITE_NAME>:8080`. A DNS record alone does not tell the proxy which app owns the hostname.
2. **Missing port.** Without `:8080` the proxy targets the wrong container port.
3. **DNS points to another server.** `nslookup <SITE_NAME> 1.1.1.1` must return your Coolify server's IP.
   A wildcard record can silently send new hostnames elsewhere. Add a specific A record.
4. **An old stack still claims the hostname.** Delete previous test stacks that used the same domain.
5. **Services not healthy.** A failing health check removes the container from routing. Check the service logs.

## `create-site` fails with "No module named 'erpnext'", or the site only shows "Framework"

The image does not contain the apps. Confirm in the `backend` terminal:

```
ls /home/frappe/frappe-bench/apps
```

You should see `crm`, `erpnext`, `frappe`, `helpdesk`, `hrms`, `insights`, `payments` and `telephony`. If only
`frappe` is listed, the image was built without `apps.json`. Current `frappe_docker` expects `apps.json` as a
**BuildKit secret** (`--secret id=apps_json,src=apps.json`). The older `APPS_JSON_BASE64` build argument is
silently ignored, and the build still "succeeds". The build workflow in this repo uses the secret and refuses to
push an image that is missing any app, so rebuild with a **new tag** and redeploy on fresh volumes.

## The site loads but only "Framework" shows, and `/crm` is "page not found"

The apps are in the image but were not installed on the site. This usually means `create-site` was interrupted
(for example by restarting or redeploying while it was still running). Check the `create-site` logs. With the
current `compose.yaml`, redeploying completes missing apps. If it reports the site is "not usable", delete the
stack including its volumes and deploy again, and this time leave it alone until you see `Done.`

## Internal Server Error right after deploying

Normal for the first minutes. The frontend starts before `create-site` has finished creating the site. Wait for the
`Done. Site ... is ready.` line, then reload.

## Browser shows "Not secure"

The certificate has not been issued or the domain was saved as `http://`. Use `https://`, make sure DNS already
points at the server and ports 80 and 443 are open, then save and restart.

## Site loads on the generated sslip.io address but not on your domain

The stack is healthy. DNS, or the Domains field, is wrong. See above.

## Site not found

`SITE_NAME` must equal the hostname you browse to, with no scheme and no port.

## create-site fails with "access denied"

An older database volume was initialised with a different root password, and the database ignores the new one.
If there is no data to keep, delete the stack **including its volumes** and deploy again.

## Image build fails on Helpdesk or frappe-ui

Upstream version mismatches happen. At the time of writing, Helpdesk `main` did not build against the newest
Frappe v16 release, and pinning an older Frappe tag was the reported workaround
(see [frappe/helpdesk#3878](https://github.com/frappe/helpdesk/issues/3878)). Re-run the build with the
`frappe_branch` input set to an older tag, and open an issue with the log.

## "Running (unknown)" in Coolify

The container has no health check, so Coolify cannot tell. Services in `compose.yaml` define health checks, so
you should see *healthy*.

## Emails do not arrive

See [email.md](email.md).