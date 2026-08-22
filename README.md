# Commure (commure)

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
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
