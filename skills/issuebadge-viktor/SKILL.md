---
name: issuebadge-viktor
description: Issue, verify, revoke and export digital certificates and badges with IssueBadge from Viktor. Use when a teammate asks Viktor to send a certificate or badge, list certificate templates, check or revoke a credential, download a certificate PDF, create a dynamic QR code, or connect IssueBadge to the workspace.
license: MIT
metadata:
  author: issuebadge
  version: "1.0.0"
  homepage: https://issuebadge.com/h/integration/mcp
---

# IssueBadge for Viktor

IssueBadge (https://issuebadge.com) issues digital certificates and Open Badges. A template is designed once; each credential issued from it goes to a recipient by name and email and gets a public verification page at `https://app.issuebadge.com/v/{IssueId}` with a QR code. Recipients never need an account.

## Connect IssueBadge to the workspace

Check whether an IssueBadge integration is already connected. If not, one of these must happen first, and only a workspace admin can do it:

**Option A, MCP server (preferred, sign-in based)**
1. Open Integrations, select Add Custom, choose MCP Server, select Next.
2. Server URL: `https://issuebadge-mcp.issuebadge.workers.dev/mcp`. Select Connect.
3. Nickname: `IssueBadge`. In Slack or Teams workspaces, choose who may use it.
4. Viktor detects the server's own sign-in. Sign in to IssueBadge in the browser and approve the `read` and `write` scopes.

This gives the tools `validate_key`, `get_all_badges`, `issue_badge`, and the QR tools `create_qr_code`, `list_qr_codes`, `get_qr_code`, `update_qr_code`, `get_qr_code_scans`, `delete_qr_code`.

**Option B, REST API with a key**
1. In IssueBadge, create a key at app.issuebadge.com, Developer, API key.
2. Open Integrations, select Add Custom, choose API, select Next.
3. Name `IssueBadge`, Base URL `https://app.issuebadge.com/api/v1`, allowed methods GET, POST, PATCH and DELETE, Authentication Bearer token, Documentation URL `https://issuebadge.com/h/developer`. Select Add API.

If the admin prefers to enter the key privately, ask Viktor to set up the API in chat; it sends a Connect link that expires after 24 hours. Never ask for the key in a public channel and never repeat it back.

## Check the connection

MCP: call `validate_key`. REST: `POST /validate-key`. If it fails, tell the admin to reconnect; do not retry in a loop.

## Issue a certificate

1. List templates: MCP `get_all_badges` or REST `GET /badge/getall`. Each template has an `id` and a `name`.
2. Pick the template whose name matches the request. If several could match, list the names and ask. Never guess an id.
3. Confirm the recipient's full name and email before issuing; the email cannot be recalled.
4. Issue: MCP `issue_badge` or REST `POST /issue/create` with
   - `badge_id`, `name` (required), `email` (recommended; the certificate is emailed when present)
   - `issue_date`, `expire_date` as `YYYY-MM-DD` (optional)
   - `idempotency_key`: use `<badge_id>-<email>-<YYYY-MM-DD>` so a repeat returns the existing credential instead of a duplicate
   - `metadata`: values for the template's custom fields only (check `GET /badge/{id}` for `custom_fields`)
5. Reply with the `publicUrl` from the response. It is public and safe to post in the channel.

Several recipients: one call per person with a per-person idempotency key. Stop and report on the first error.

## Get data out

| Need | Call |
|---|---|
| Issued credentials | `GET /issue/get`; one: `GET /issue/get/{IssueId}`; audit trail: `GET /issue/{IssueId}/history` |
| Certificate PDF | `POST /certificate/generate` (PDF bytes) or `/certificate/base64`; batch: `/certificate/batch/generate`, `/certificate/batch/base64` |
| Revoke | `POST /issue/revoke` with a reason; `PATCH /issue/{IssueId}` changes `expire_date` or `memo`, or reinstates |
| Verification page | `https://app.issuebadge.com/v/{IssueId}` |
| Dynamic QR codes | MCP QR tools, or `GET/POST /qrcode`, `/qrcode/{code}`, `/qrcode/{code}/image`, `/qrcode/{code}/scans`. Updating the target changes where a printed code goes. |
| Events to other tools | Webhooks are configured in the IssueBadge dashboard (Settings, Webhooks) for issued, viewed and revoked events |

For a recurring task (for example "every Friday, issue attendance certificates for this week's workshop"), take recipients from the connected source, dedupe by email, use idempotency keys, and post the list of verification links when done.

## Rules

- Confirm template and recipient before the first issue in a conversation. Do not issue to placeholder or example addresses unless a test is explicitly requested.
- Do not invent template ids, recipient details or custom field values.
- Do not retry a failed issue with the same key blindly; report the error and issue again only once the user confirms nothing arrived.
- "Requires an active plan" is a billing message: point to https://issuebadge.com/h/pricing.
- HTTP 401: key or sign-in invalid. HTTP 422: show the validation message verbatim.
- Only app.issuebadge.com and the MCP host ever receive credentials.

Setup page for other clients: https://issuebadge.com/h/integration/mcp. Support: https://issuebadge.com/h/contact.
