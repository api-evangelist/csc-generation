---
name: Buy from a CSC Generation brand store as an agent
description: >-
  Search, cart and check out on a CSC Generation portfolio storefront over the
  Universal Commerce Protocol (UCP) MCP endpoint the brand advertises at
  /.well-known/ucp — with the idempotency, agent-identity and human-approval rules
  those endpoints actually enforce.
api: mcp/csc-generation-mcp.yml
generated: '2026-08-01'
method: generated
source: >-
  https://www.seattlecoffeegear.com/llms.txt (the flow, verbatim) +
  https://ucp.dev/2026-04-08/services/shopping/mcp.openrpc.json (the tool names)
operations:
  - search_catalog
  - create_cart
  - create_checkout
  - update_checkout
  - complete_checkout
---

# Buy from a CSC Generation brand store

CSC Generation has no corporate API. Its agent surface is per-brand: a storefront
publishes a **UCP merchant profile** at `/.well-known/ucp`, and that profile names the
MCP endpoint and protocol version to use. Two brands were verified live on 2026-08-01:

| Brand | Discovery | MCP endpoint | UCP version |
|---|---|---|---|
| Seattle Coffee Gear | `https://www.seattlecoffeegear.com/.well-known/ucp` | `https://www.seattlecoffeegear.com/api/ucp/mcp` | `2026-04-08` |
| Backcountry | `https://www.backcountry.com/.well-known/ucp` | `https://api.firmly.online/api/2026-01-23/ucp/mcp/domain/backcountry.com` | `2026-01-23` |

## 0. Discover before you call

`GET /.well-known/ucp` on the brand host first. Read `ucp.version`, pick a version from
`ucp.supported_versions`, and take the endpoint from
`ucp.services["dev.ucp.shopping"][].endpoint` where `transport` is `mcp`. Do not
hardcode the endpoints above — the profile is the source of truth and the two brands
are already on different protocol generations.

## 1. Identify yourself

Both endpoints reject anonymous calls, and they reject them differently:

- **Seattle Coffee Gear** needs your own UCP profile URI in `meta.ucp-agent.profile`
  (HTTP `UCP-Agent`). Without it: `422`, `invalid_profile_url`,
  `"Unable to fetch agent profile: Missing profile uri"`.
- **Backcountry** needs a merchant/destination API key from the UCP host. Without it:
  `401`, `api_key_required`. There is no public self-service enrollment surface.

## 2. Set an idempotency key on anything that mutates

`meta["idempotency-key"]` is a UUID that maps to the HTTP `Idempotency-Key` header. The
UCP shopping service **requires** it on `complete_checkout`, `cancel_checkout` and
`cancel_cart`; set it on `create_checkout`, `update_checkout`, `create_cart` and
`update_cart` too so a retry after a timeout cannot double-charge or double-create.

## 3. The flow

1. `search_catalog` — find products matching the buyer's intent. Pass
   `context.address_country` and `context.currency` or pricing and availability will be
   wrong.
2. `create_cart` — add the desired items. (`get_cart` / `update_cart` / `cancel_cart` to
   adjust.)
3. `create_checkout` — start the purchase.
4. `update_checkout` — set shipping address and method.
5. `complete_checkout` — finalize. **The buyer must approve the payment.**

`lookup_catalog`, `get_product`, `get_checkout` and `get_order` are the read-side tools.

## 4. Never complete a payment without the human

This is the store's own published rule, not a suggestion:

> Checkout requires human approval. Agents must not complete payment without explicit
> buyer consent.

If you cannot get contemporaneous buyer approval at the moment of payment, do not call
`complete_checkout` — the store directs agents to route the purchase through Shop Pay
via the Shop skill (`https://shop.app/SKILL.md`) instead.

## 5. Errors and backoff

Errors come back as a UCP `messages[]` array (`type`, `code`, `content`, `severity`),
wrapped in a JSON-RPC 2.0 error over MCP. `severity: recoverable` means fix the input
and retry with the *same* idempotency key. The MCP endpoint is rate-limited per IP —
back off on `429`. See `errors/csc-generation-problem-types.yml`.

## 6. If you only need to read

Seattle Coffee Gear publishes an unauthenticated read-only surface for agents that do
not transact: `GET /products/{handle}.json`,
`GET /collections/{handle}/products.json`, `GET /search?q={query}&type=product`, and
`GET /sitemap.xml`. Prefer these over scraping the rendered storefront.
