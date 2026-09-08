# FoxForm MCP server

Connect [FoxForm](https://foxform.app) to Claude, ChatGPT and any other MCP
client. Describe the form you need, publish it, and read the answers back
without leaving the conversation.

The server is **hosted by us** — there is nothing to install and no code in this
repository. Point your client at the URL below and sign in with your FoxForm
account.

```
https://mcp.foxform.app/mcp
```

## What you can do with it

| Tool | What it does |
| --- | --- |
| `foxform_list_forms` | List the forms in your account, newest first |
| `foxform_get_form` | Read one form: every screen, its settings and its conditional logic |
| `foxform_create_form` | Create a form from a description |
| `foxform_update_form` | Rewrite an existing form (questions, style, logic) |
| `foxform_publish_form` | Publish it and get the public link |
| `foxform_unpublish_form` | Take a published form off the air |
| `foxform_list_responses` | List the answers a form received |
| `foxform_get_response` | Read one answer in full |
| `foxform_get_form_analytics` | Views, completion rate and per-question stats |
| `foxform_export_responses` | Export the answers as CSV |

Six of the ten only read. The four that write are marked as such to the client,
so your assistant asks before changing or unpublishing anything.

## Connecting

### Claude

Settings → Connectors → Add custom connector, and paste:

```
https://mcp.foxform.app/mcp
```

Claude opens the FoxForm sign-in page, you approve the access, and the tools
appear in the conversation. No API key to copy.

### ChatGPT

Settings → Connectors → Add, same URL. Same sign-in flow.

### Anything else

The server speaks **Streamable HTTP** at `/mcp`. Two ways to authenticate:

**OAuth 2.1** — the recommended one, and what the clients above use. Discovery
follows the specs, so a compliant client needs nothing but the URL:

| Document | URL |
| --- | --- |
| Protected resource metadata (RFC 9728) | `https://mcp.foxform.app/.well-known/oauth-protected-resource` |
| Authorization server metadata (RFC 8414) | `https://mcp.foxform.app/.well-known/oauth-authorization-server` |

Dynamic client registration (RFC 7591) and PKCE with `S256` are supported.
Scopes are `forms:read` and `forms:write`.

**API key** — for scripts and self-hosted clients. Generate one in the FoxForm
dashboard under **API & MCP**, then send it as a bearer token:

```bash
curl -X POST https://mcp.foxform.app/mcp \
  -H "Authorization: Bearer ff_live_your_key" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

`tools/list` answers without credentials, so a directory can read the catalogue
before anyone signs in. Every tool that touches your data requires
authentication.

## Accounts and pricing

You need a FoxForm account. The free plan collects up to 25 responses, with no
card and no deadline; paid plans lift that and remove the FoxForm mark from
published forms. See [foxform.app](https://foxform.app) for the current plans.

## Support

- Product and account: [foxform.app](https://foxform.app)
- Something wrong with this server: open an issue here

## Why this repository has no code

Directories ask for a repository URL, and this is it. The server runs on our
infrastructure, so listing it does not require shipping the implementation —
what a client needs is the URL above and the discovery documents, both public.
This repository holds the machine-readable descriptor (`mcp.json`) and these
instructions.

FoxForm is built by [Mamute Tech](https://mamutetech.com).
