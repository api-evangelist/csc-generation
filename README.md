# CSC Generation

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
