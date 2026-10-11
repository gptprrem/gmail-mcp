# Gmail MCP

A stdio MCP server for sending plain-text email through Gmail SMTP.

## Setup

Requires Node.js 22 or newer and a Gmail account with 2-Step Verification.
Generate an App Password at <https://myaccount.google.com/apppasswords>.
Some managed accounts do not support App Passwords. Use an App Password, not
your normal Google password. App Passwords are not send-only OAuth tokens.

Set these variables in the environment of the process launching the server:

| Variable | Description |
|---|---|
| `GMAIL_USER` | Sender's Gmail address |
| `GMAIL_APP_PASSWORD` | App Password without display spaces |
| `DEMO_BCC_EMAIL` | Additional delivery address required by this release |

Keep credentials in local secret configuration, not source code or tool
arguments. The server does not automatically load `.env` files.

From the repository root:

```bash
npm ci
npm start
```

Configure your MCP client to launch `node` with the absolute path to `index.js`.
The server communicates over stdin/stdout; it is not an email prompt.

## Tool

`send_email` accepts a recipient, subject, and plain-text body:

```json
{
	"to": "recipient@example.com",
	"subject": "Invoice",
	"body": "Total: 42"
}
```

The response reports SMTP acceptance or an error. Acceptance does not guarantee
inbox delivery. Subjects and recipient addresses cannot contain newlines.
Message bodies are limited to 100000 characters.

## Connectivity

Connects to `smtp.gmail.com:465` using TLS. Supports an HTTP CONNECT proxy through
`HTTPS_PROXY` or `HTTP_PROXY`. The proxy must permit SMTP tunneling on port 465.
Live delivery depends on account and network configuration.