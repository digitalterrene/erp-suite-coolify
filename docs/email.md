# Email (SMTP)

ERPNext does not send mail by itself on a self-hosted install. Until an outgoing **Email Account** is set as
the default, password-reset and notification emails are not delivered. The "If an account exists..." message
on the login page is generic and appears whether or not mail was sent.

## Using your own domain's mail server (cPanel)

1. In cPanel, create a mailbox for system mail, for example `noreply@yourdomain.com`.
2. Open that mailbox's **Connect Devices** page and note the *outgoing server* and ports shown there.
   Prefer port **465 (SSL/TLS)** or **587 (STARTTLS)**.
3. In ERPNext, search for **Email Account** and click **New**.
4. Enter the address, then the SMTP server and port from step 2. The login is the **full mailbox address**,
   and the password is that mailbox's password.
5. Tick **Enable Outgoing** and **Default Outgoing**. Leave incoming disabled unless you need it.
6. Save, then try *Forgot password?* with an address you can read.

If ERPNext reports a certificate hostname mismatch, use the server name that your mail certificate actually
covers (cPanel's Connect Devices page usually lists it).

## Make the mail trusted

Mail from your domain should pass **SPF** and **DKIM**, with a **DMARC** record for the domain. In cPanel the
**Email Deliverability** tool checks these records and can repair them. Without them, large providers often
send your mail to spam or reject it.

## Know your limits

Shared hosting servers usually limit how many emails a domain can send per hour. That is plenty for password
resets and notifications. Bulk campaigns from CRM may hit it. If you administer the server, review the limit
in WHM; otherwise ask your host.

## Other SMTP providers

The same steps apply to any provider: server, port, TLS or SSL, username, password, then **Enable Outgoing** and
**Default Outgoing**.

## If mail still does not arrive

1. **Email Account list:** one account should show as the default for sending.
2. **Email Queue list:** open the reset email. *Not Sent* or *Error* entries include the failure reason.
   If nothing is queued, check the **Error Log** list.
3. **Scheduler:** in the `backend` terminal run `bench --site <SITE_NAME> doctor`. If the scheduler is
   disabled, run `bench --site <SITE_NAME> scheduler enable`.
4. **Connectivity:** `bash -c 'echo > /dev/tcp/YOUR_SMTP_HOST/465 && echo open'` should print `open`.
5. **Spam folder** and **sender mismatch:** the From address should belong to the authenticated domain.

## Security

The email password is stored encrypted in the database. The key is in the site's `site_config.json`, so a
restore needs both the database backup and that file. See [backups.md](backups.md). Never post SMTP passwords
in issues or chats.
