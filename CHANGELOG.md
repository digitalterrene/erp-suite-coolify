# Changelog

All notable changes to this project are documented here.
The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and image tags are date-based (for example `2026-10-24`).

## [Unreleased]

### Fixed
- Image build: pass `apps.json` as a BuildKit secret, as current `frappe_docker` requires. The old
  `APPS_JSON_BASE64` build argument is ignored by the new Containerfile and produced a frappe-only image.
- Image build: the image is now built and checked **before** it is pushed. The run fails, and nothing is
  published, if any app from `apps.json` is missing.
- `create-site` now installs any missing app on every start instead of skipping when the site folder exists.
  An interrupted first run is completed by redeploying, and a half-created site fails with a clear message.
- Deployment docs: set the domain before the first deploy and do not restart while `create-site` runs.

### Added
- Custom image build workflow (ERPNext, HR, CRM, Helpdesk, Insights, Payments, Telephony).
- Coolify-ready `compose.yaml` with health checks and generated passwords.
- Documentation for deployment, email (SMTP), backups and troubleshooting.