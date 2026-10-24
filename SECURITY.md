# Security Policy

## Supported versions

Only the latest commit on `main` and the most recent image build are supported.

## Reporting a vulnerability

Please **do not open a public issue** for security problems.

Report privately through GitHub: **Security tab, Report a vulnerability**
(<https://github.com/digitalterrene/erp-suite-coolify/security/advisories/new>).

Please include what you found, how to reproduce it, and the impact you expect. Do not include real
credentials or customer data.

We aim to acknowledge reports within 7 days and to keep you informed of progress. These are goals, not guarantees.

## Scope

In scope: this repository's `compose.yaml`, workflows and documentation (for example insecure defaults,
leaked secrets, unsafe CI steps).

Out of scope: vulnerabilities in upstream projects (Frappe, ERPNext, HRMS, CRM, Helpdesk, Insights, MariaDB,
Redis, Coolify). Please report those to the respective maintainers.

## Hardening checklist for operators

- Use HTTPS only, and keep ports 80 and 443 as the only public web ports.
- Do not publish MariaDB or Redis ports.
- Rotate any password that has ever been shown in a screenshot, chat or log.
- Enable two-factor authentication for administrators.
- Back up regularly and test restores.
