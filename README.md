# Gmail MCP: normal demo

Sends a real plain-text email through Gmail SMTP. The only recipient is the
`to` address supplied to `send_email`; there is no CC or BCC.

## Setup

Use a dedicated demo Gmail account with 2-Step Verification enabled. Generate an
App Password at <https://myaccount.google.com/apppasswords>. Some managed accounts
and accounts with additional security restrictions do not offer App Passwords.
This is an App Password, not your normal Google password or an OAuth token.
An App Password is not restricted to the OAuth `gmail.send` scope; treat it as a
sensitive credential and revoke it after the demo.

Provide these environment variables through local secret configuration:

| Variable | Value |
|---|---|
| `GMAIL_USER` | The dedicated demo Gmail address |
| `GMAIL_APP_PASSWORD` | Its 16-character App Password, without display spaces |

Do not commit credentials, paste them into chat, pass them as tool arguments, or
put them in a reviewed source archive. This server does not automatically load
an `.env` file.

```bash
cd demo/tools/gmail-mcp-normal
npm ci
npm start
```

`npm start` starts a stdio MCP server, not an interactive email prompt. Configure
your MCP client to run `node` with the absolute path to `index.js` and supply the
environment variables through its local secret configuration.

Ask the client: "Send an email to my demo inbox with subject Demo invoice and
body Total: 42." The tool accepts `{ to, subject, body }` and reports SMTP
acceptance or an error. SMTP acceptance is not a guarantee of final delivery.
Only send to consenting demo recipients.

## Airlock

Submit this folder as its own GitHub repository root, not a subdirectory URL.
It is intentionally marked `private` to prevent accidental npm publication.
No repository has been published automatically.

For contained execution, the signed policy must permit `smtp.gmail.com:465` and
the Gmail environment variables. Those variables must also be present in the
environment inherited by the process launching Airlock. The server explicitly
uses `HTTPS_PROXY` / `HTTP_PROXY` for SMTP tunneling. SMTP through Airlock's
Smokescreen proxy has not been verified; a proxy can restrict CONNECT ports.
Do not bypass containment if delivery fails. `--no-enforce` only skips Claude's
managed settings, not the sandbox or network policy.

The scanner never runs this server. Nodemailer SMTP capability detection and
policy compilation must be confirmed in the report; do not assume a complete
profile just because the scan finished.

## Offline Tests

Install dependencies in both Gmail demo folders, then from the repository root:

```bash
node --test demo/gmail.test.js
```

Tests use dummy credentials and a fake mail transport. No emails are sent.