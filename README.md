# HubSpot plugin for Cursor

This folder is a minimal [Cursor plugin](https://cursor.com) that wires
**HubSpot’s remote MCP server** into the editor and adds a **skill** so agents
know how to use CRM tools safely.

## What you get

- **CRM data (remote MCP)** — Connect to HubSpot’s hosted MCP (OAuth with
  PKCE). `mcp.json` uses **`https://mcp.hubspot.com/`** so OAuth metadata matches
  what Cursor expects (see below).
- **Skill** — `skills/hubspot-crm/SKILL.md` nudges the agent toward MCP tools
  and sane query patterns.

HubSpot also documents a **separate Developer MCP server** (local, CLI-driven)
for building on the HubSpot developer platform. That is not configured here; see
[Developer MCP server](https://developers.hubspot.com/docs/developer-tooling/local-development/mcp-server)
if you need it.

## Why `mcp.hubspot.com` in `mcp.json` (not `mcp-eu1`)

HubSpot’s OAuth **protected resource metadata** identifies the resource as
**`https://mcp.hubspot.com`**. Cursor verifies that the MCP URL you configure
**matches that origin**. If `mcp.json` uses e.g. **`https://mcp-eu1.hubspot.com/`**
while metadata still says **`mcp.hubspot.com`**, Cursor fails with:

`Protected resource https://mcp.hubspot.com does not match expected https://mcp-eu1.hubspot.com/`

So this plugin keeps **`https://mcp.hubspot.com/`** even for **EU** HubSpot
accounts, until HubSpot advertises a consistent EU origin in that metadata.

## EU vs global in the HubSpot UI

The **Installation URL Builder** may show a **region-specific** authorize URL,
for example:

`https://mcp-eu1.hubspot.com/oauth/authorize/user?client_id=...&redirect_uri=...`

That is for the **browser OAuth** install flow. It does **not** mean you should
set the **MCP stream** URL in Cursor to `mcp-eu1` if that triggers the mismatch
error above.

## Authorization URL vs MCP URL

The **authorize** link starts the **browser OAuth** flow. After the user
approves, HubSpot redirects to your **Redirect URL** (e.g.
`http://localhost:3000/api/hubspot/oauth/callback`) with an authorization
`code`. A real install must also use **PKCE** (`code_challenge` / verifier),
as HubSpot states in the UI—do not treat the bare authorize link as a complete
production flow unless your client adds PKCE per their docs.

The **MCP** URL in `mcp.json` is the **streamable HTTP** endpoint Cursor uses
after OAuth; it must stay aligned with HubSpot’s **protected resource** metadata
for Cursor’s client.

## Prerequisites

1. A HubSpot account on the current developer platform.
2. An **MCP Auth App** in HubSpot (**Development → MCP Auth Apps → Create MCP
   auth app**).
3. **Redirect URL(s)** in the app that **exactly** match your client. For a
   custom OAuth bridge, that may be e.g.
   `http://localhost:3000/api/hubspot/oauth/callback` (with a server on that
   route to exchange the `code`). For Cursor-native MCP OAuth, register the
   redirect URI Cursor uses (see Cursor docs or any OAuth error details).
4. For quick experiments with MCP Inspector, HubSpot’s docs mention callbacks
   such as `http://localhost:6274/oauth/callback/debug`.

## Install in Cursor

**From GitHub:** [github.com/jsdbroughton/hubspot-cursor-plugin](https://github.com/jsdbroughton/hubspot-cursor-plugin)

In Cursor, open **Settings → Plugins** and add the plugin using
`jsdbroughton/hubspot-cursor-plugin` (or the full repository URL if your Cursor
build asks for a URL—same pattern as other Cursor plugins such as
`makenotion/cursor-notion-plugin`).

**From a local clone:** copy or symlink the plugin folder into
`~/.cursor/plugins/local/` (see Cursor’s local plugin notes; symlinks may not
work on all versions), then **Developer: Reload Window**.

After install, connect **HubSpot MCP** under MCP / Tools settings and complete
OAuth when prompted, using your MCP Auth App’s **client ID** and **client
secret** as your Cursor version requires.

Exact OAuth field names in Cursor’s UI can change; if connection fails, confirm
PKCE is in use and redirect URLs match HubSpot’s app settings. HubSpot’s
guide:
[Integrate with the remote HubSpot MCP server](https://developers.hubspot.com/docs/apps/developer-platform/build-apps/integrate-with-the-remote-hubspot-mcp-server).

## Project layout

```text
hubspot-cursor-plugin/
├── .cursor-plugin/
│   └── plugin.json
├── mcp.json
├── README.md
└── skills/
    └── hubspot-crm/
        └── SKILL.md
```

## Security notes

- Never commit **client secret** or tokens. **Client ID** is not a secret in the
  OAuth sense but avoid pasting it into public places if you can help it.
- OAuth credentials belong in HubSpot’s app settings and in Cursor’s secure MCP
  auth flow — not in git.
- CRM access follows the installing user’s HubSpot permissions and the scopes
  granted at install time.

## Next steps you might add

- Extra skills (e.g. “summarize pipeline”, “prep for call with company X”).
- A second MCP entry for the **Developer** MCP if you live in `hs` CLI
  workflows.
- `assets/logo.png` and a `logo` field in `plugin.json` if you publish to a
  marketplace.
