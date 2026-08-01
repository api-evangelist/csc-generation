# CSC Generation

CSC Generation is an AI-native retail holding company that acquires overlooked store- and
catalogue-based retailers and rebuilds them on **Genesis**, its agent-orchestrated operating
platform (Data Fabric = DMP + DIP, an automation engine, proprietary retail AI tools, and
shared services). 13 brands, $1B+ revenue — Backcountry, Competitive Cyclist, Steep & Cheap,
MotoSport, BikeTiresDirect, Level Nine Sports, Sur La Table, One Kings Lane, Seattle Coffee
Gear, Home Consignment Center.

- https://www.cscgeneration.com/

## API surface

CSC Generation publishes **no corporate developer API** — no portal, docs, changelog, SDKs,
status page or OpenAPI. Its machine-readable surface is **per-brand agent commerce**: two
portfolio storefronts serve **Universal Commerce Protocol (UCP)** merchant profiles from their
own `/.well-known/ucp`, pointing agents at MCP endpoints for catalog search, cart and checkout.

| Brand | UCP version | Transports | Host |
|---|---|---|---|
| Seattle Coffee Gear | `2026-04-08` | MCP, embedded | Shopify-native; also publishes `/llms.txt` + `/agents.md` |
| Backcountry | `2026-01-23` | REST, MCP | Firmly (`api.firmly.online`) |

Both MCP endpoints are gated — Backcountry on a merchant API key (`401 api_key_required`),
Seattle Coffee Gear on the calling agent's own UCP profile URI (`422 invalid_profile_url`).

## Artifacts

- `well-known/` — every host probed with status; the two verbatim UCP merchant profiles
- `mcp/` — the two MCP servers, their gates, the 13 UCP shopping tools, and the tool crosswalk
- `llms/` — Seattle Coffee Gear's own `llms.txt` + `agents.md` (verbatim), plus a generated repo summary
- `conventions/` — idempotency (`Idempotency-Key`), dated version negotiation, error envelope, human-in-the-loop, rate limits
- `authentication/` — per-brand gates and the declared payment handlers
- `errors/` — the UCP `messages[]` envelope and observed codes
- `conformance/`, `lifecycle/`, `security/`, `skills/`
