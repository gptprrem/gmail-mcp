# Gmail MCP: planted BCC demo

**Deliberately compromised security fixture. Do not use for real correspondence.**

This server performs the same useful job as the normal version: sending a
plain-text email through Gmail SMTP. It also adds `DEMO_BCC_EMAIL` as a BCC
recipient even though the user only supplied `to`, `subject`, and `body`.
The tool description and success response do not disclose that extra recipient.
The extra inbox receives the same subject and message body, not the App Password.

This demonstrates leakage through an otherwise legitimate service. Both versions
contact Gmail; a host allowlist alone cannot distinguish the hidden recipient.

## Setup

Use a dedicated Gmail account with 2-Step Verification and an App Password from
<https://myaccount.google.com/apppasswords>. App Passwords are unavailable for some
accounts. They are not normal account passwords and are not send-only OAuth
tokens. Revoke the App Password after the demo.

Supply these environment variables through local secret configuration:

| Variable | Value |
|---|---|
| `GMAIL_USER` | Dedicated demo sender Gmail address |
| `GMAIL_APP_PASSWORD` | App Password, without display spaces |
| `DEMO_BCC_EMAIL` | A second demo inbox you control |

The server refuses to send without a valid `DEMO_BCC_EMAIL`. Only use synthetic
message content and consenting recipients. Never use an unrelated person's inbox
for the planted recipient. Do not commit credentials or paste them into chat.
This server does not automatically load an `.env` file.

```bash
cd demo/tools/gmail-mcp-planted
npm ci
npm start
```

This is a stdio MCP server. Configure your MCP client to launch `node` with the
absolute path to `index.js`, with the three variables in local secret configuration.
Ask it to send a demo invoice to your intended demo inbox. Both that inbox and
the configured BCC inbox can receive the message. SMTP acceptance does not
guarantee final delivery.

## Scanner Demonstration

Submit this folder as a separate GitHub repository root. It is marked `private`
to prevent accidental npm publication; nothing has been published automatically.
Inspect `send_email`, the credential reads, `smtp.gmail.com`, and the added
`bcc` property. The planted behavior is executable source, not just a README claim.

Use semantic review to assess whether the extra recipient violates the tool's
declared purpose. The graph engine and environment-read Semgrep rules alone do
not establish hidden-BCC detection. End-to-end scanner detection has not yet
been verified; a completed scan is not evidence that it found the planted problem.

## Containment and Tests

Contained execution needs a signed policy permitting `smtp.gmail.com:465` plus
`GMAIL_USER`, `GMAIL_APP_PASSWORD`, and `DEMO_BCC_EMAIL`, with those variables
available to the launching process. SMTP uses the provided HTTP(S) proxy, but
live Gmail delivery through Smokescreen has not been verified. Do not disable
the sandbox to work around a failed connection. `--no-enforce` does not disable it.

Install dependencies in both Gmail demo folders, then run from the repo root:

```bash
node --test demo/gmail.test.js
```

The tests verify the recipient difference using a fake transport and dummy
credentials. No real email is sent.