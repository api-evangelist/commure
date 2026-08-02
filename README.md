# Commure (commure)

Commure is a San Francisco-based AI-native healthcare technology company serving United States health systems, formed by the 2023 combination of Commure and Athelas. Its integrated platform spans Ambient AI clinical documentation (Scribe/Dictation), end-to-end Revenue Cycle Management (RCM), Call Center Agents, referral Orchestrator, patient Engage coordination, Commure Pro clinical intelligence, Strongline staff-safety alerting, and Athelas Home point-of-care diagnostics, integrating with 60+ EHRs across 130+ health systems processing over $25B in annual claims.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/commure/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/commure/refs/heads/main/apis.yml)

## API surface

Commure's API is a **gated, partner-only** Developer Services offering governed by the [Developer User Agreement](https://www.commure.com/legal/developer-user-agreement). The FHIR-native open developer platform launched in 2020 has been withdrawn without a published sunset notice: `developer.commure.com` returns HTTP 404 on every path, and the tenant API hosts `api-{tenant-id}.developer.commure.com` no longer resolve.

The one surviving first-party machine-readable contract is Commure's **public Postman workspace** ([postman.com/commure/commure](https://www.postman.com/commure/commure/)), which publishes a 59-request `Commure FHIR API` collection plus five clinical-scenario collections. All six are saved verbatim in `postman/`, and `openapi/commure-fhir-openapi.yml` is an OpenAPI 3.1 document derived from the reference collection — 55 paths, 59 operations covering the HL7 FHIR RESTful interactions, terminology services, conformance operations, FHIR Bulk Data, and the OpenID Connect / SMART App Launch authentication surface.

## Tags

- Healthcare
- United States
- Clinical AI
- Ambient AI
- Revenue Cycle Management
- FHIR
- SMART on FHIR
- Interoperability
- EHR
- Remote Monitoring
- Health System
- Terminology Services

## Timestamps

- **Created:** 2026-07-24
- **Modified:** 2026-07-31

## APIs

- **Commure FHIR API** — [Postman documentation](https://www.postman.com/commure/commure/documentation/vp76tv7/commure-fhir-api) · base URL `https://api-{tenant-id}.developer.commure.com` (published; currently non-resolving) · OAuth 2.0 / OpenID Connect / SMART App Launch

## Repo contents

| Path | What |
|---|---|
| `apis.yml` | APIs.json 0.20 profile |
| `openapi/` | OpenAPI 3.1 derived from the published Postman collection |
| `postman/` | All six public Commure collections, verbatim |
| `authentication/`, `scopes/` | OAuth 2.0 / OIDC / SMART App Launch profile and scopes |
| `conventions/` | Idempotency, pagination, versioning, media types, async semantics |
| `errors/` | FHIR OperationOutcome catalog |
| `data-model/` | FHIR entity/relationship graph |
| `conformance/` | Standards conformance assertions |
| `lifecycle/` | Versioning, deprecation, status page |
| `well-known/` | `/.well-known/` probe results (none served) |
| `security/` | Domain security probe, trust center |
| `skills/` | 5 packaged agent skills grounded in real operationIds |
| `overlays/` | OpenAPI Overlay of API Evangelist annotations |
| `llms/` | llms.txt |
| `review.yml` | Original reviewer finding |

## Common Properties

- [Website](https://www.commure.com/)
- [Company](https://www.commure.com/company)
- [API Reference (Postman)](https://www.postman.com/commure/commure/documentation/vp76tv7/commure-fhir-api)
- [Postman workspace](https://www.postman.com/commure/commure/)
- [Sign up](https://accounts.commure.com/signin/register) · [Sign in](https://accounts.commure.com/signin)
- [Blog](https://www.commure.com/blog)
- [News](https://www.commure.com/news)
- [Partners](https://www.commure.com/partners)
- [Customers](https://www.commure.com/customers)
- [Support / Contact](https://www.commure.com/contact)
- [Status Page](https://status.commure.com)
- [Trust Center](https://www.commure.com/trust-center)
- [Terms of Use](https://www.commure.com/legal/general-terms-of-use)
- [Privacy Policy](https://www.commure.com/legal/privacy-policy)
- [Business Associate Agreement](https://www.commure.com/legal/business-associate-agreement)
- [Developer User Agreement](https://www.commure.com/legal/developer-user-agreement)
- [GitHub Organization](https://github.com/commure)
- [LinkedIn](https://www.linkedin.com/company/commure)

## Not present (probed, honest negatives)

- No `/.well-known/security.txt`, `openid-configuration`, `api-catalog`, `ai-plugin.json` or agent card on any reachable host.
- No A2A Agent Card — `/.well-known/agent-card.json` and `/.well-known/agent.json` both 404 everywhere probed.
- No MCP server, no GraphQL endpoint, no gRPC/protobuf, no AsyncAPI and no documented webhook catalog.
- No first-party SDK, CLI or client library on npm, PyPI, crates.io or the GitHub org.
- No dated API changelog, no published SLA, no vulnerability disclosure program or bug bounty.

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
