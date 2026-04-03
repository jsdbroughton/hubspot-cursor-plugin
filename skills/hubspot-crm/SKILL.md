---
name: hubspot-crm
description: >-
  Use HubSpot MCP for CRM search and summaries (contacts, companies, deals,
  tickets). Use when the user asks about HubSpot records or pipeline context.
---

# HubSpot CRM (MCP)

## When to use this skill

Use the HubSpot MCP tools when the user wants to read or reason about HubSpot
CRM data (contacts, companies, deals, tickets, quotes, products, and related
objects). The remote HubSpot MCP server is read-only for those objects.

The plugin connects to **`https://mcp.hubspot.com/`** so Cursor’s OAuth check matches
HubSpot’s advertised protected resource. EU accounts may still use EU-specific
**authorize** links in the HubSpot UI; do not assume a different MCP base URL in
`mcp.json` unless HubSpot documents matching metadata for that host.

## Before you call tools

1. Confirm the HubSpot MCP server is connected and authenticated in Cursor
   (OAuth via the user’s MCP Auth App).
2. Read the tool schema from the MCP descriptor when parameters are unclear;
   prefer the smallest query that answers the question.

## Patterns that work well

- Start from a named company, contact, or deal when the user gives one; then
  fetch associations or summaries as needed.
- For broad questions (“deals over $X in stage Y”), use search or list tools if
  available rather than pulling full pipelines without filters.
- Summarize results in plain language; include record identifiers (IDs) when the
  user might need them in HubSpot.

## Limits

- The remote CRM MCP does not replace HubSpot UI configuration or write
  operations unless HubSpot exposes write tools for your account; treat the
  server as read-focused unless a tool explicitly performs a mutation.
- Custom sensitive-data properties may be blocked by HubSpot; if a field is
  missing, say so and avoid guessing.

## References

- HubSpot remote MCP overview: <https://developers.hubspot.com/mcp>
- Integration guide (OAuth, MCP Auth App):
  <https://developers.hubspot.com/docs/apps/developer-platform/build-apps/integrate-with-the-remote-hubspot-mcp-server>
