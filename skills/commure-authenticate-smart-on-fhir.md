---
name: Authenticate against the Commure FHIR API (SMART on FHIR / OIDC)
description: Obtain and use an access token for the Commure Developer Platform FHIR API via the OpenID Connect / SMART App Launch authorization code flow, or the client credentials grant for system-to-system access.
generated: '2026-07-31'
method: generated
api: openapi/commure-fhir-openapi.yml
source: postman/commure-fhir-api-collection.json
operations:
  - sMARTAppLaunchConfiguration
  - openIDConnectProviderMetadata
  - authorizationEndpoint
  - tokenEndpoint
  - publicKeys
  - userInfo
  - logout
---

# Authenticate against the Commure FHIR API

**Availability warning.** The tenant hosts this skill targets
(`https://api-{tenant-id}.developer.commure.com`) no longer resolve publicly, and
`developer.commure.com` returns HTTP 404. Commure's Developer Services are gated behind the
[Developer User Agreement](https://www.commure.com/legal/developer-user-agreement) and issued
per partner. Treat this as the operating contract to follow **once a tenant is provisioned**,
not as a callable public endpoint. See `lifecycle/commure-lifecycle.yml`.

## Prerequisites

- A tenant id. It is the suffix on the dashboard host when you sign in to the Commure Developer
  Platform — in `https://dashboard-99750511.developer.commure.com/smart/dashboard/` the tenant id
  is `99750511`. Every request goes to `https://api-{tenant-id}.developer.commure.com`.
- A registered client id (Commure's published example client is `smart_hello_world`).
- For the client credentials grant, a client secret enabled for that grant in the specific
  Commure Platform environment.

## Steps

1. **Discover the auth surface.** Call `openIDConnectProviderMetadata`
   (`GET /auth/.well-known/openid-configuration`) for the OIDC provider configuration, and
   `sMARTAppLaunchConfiguration` (`GET /api/v1/r4/.well-known/smart-configuration`) for the SMART
   App Launch capabilities. Never hard-code endpoint URLs you can discover.

2. **Pick the grant.**
   - *Authorization code (default).* Use it whenever the request can be attributed to a human
     user. Supports the SMART **EHR launch** sequence (with a `launch` parameter handed to you by
     the EHR) and the SMART **standalone launch** sequence.
   - *Client credentials.* Use it **only** for requests that cannot reasonably be associated with
     an individual user. Requires a client secret and per-environment enablement.
   - *Refresh token.* Exchange a refresh token for a new access token; request `offline_access` to
     get one.

3. **Authorization code + PKCE.** Redirect the user to `authorizationEndpoint`
   (`GET /auth/authorize`) with `response_type`, `client_id`, `redirect_uri`, `scope`, `state`,
   `nonce`, `code_challenge`, `code_challenge_method`, and — for an EHR launch — `launch` plus
   `aud` (the FHIR base URL). Commure authenticates the user by single sign-on, typically through
   an SSO provider configured by the hospital. Always send and verify `state`; always use PKCE.

4. **Exchange the code.** POST to `tokenEndpoint` (`POST /auth/token`). Commure's own documented
   starter scope set is `openid email`; see `scopes/commure-scopes.yml` for the scopes the
   contract declares. SMART resource scopes (`patient/*.read`, `user/*.read`, `system/*.read`) are
   defined by the SMART App Launch IG — confirm which ones your tenant grants from the
   `smart-configuration` document rather than assuming.

5. **Call the API.** Present the token as `Authorization: Bearer Sec-...` (Commure access tokens
   carry a `Sec-` prefix). A missing or expired token returns **401** with a FHIR
   `OperationOutcome` — see `errors/commure-problem-types.yml`.

6. **Verify ID tokens.** Fetch the JWKS from `publicKeys` (`GET /auth/jwks`, RFC 7517) and verify
   the ID token signature, issuer, audience and nonce. Cache the JWKS and honour key rotation.

7. **Read user context** with `userInfo` (`GET /auth/userinfo`). Expect any claim except `aud` and
   `sub` to be absent — the returned claims depend on the token scopes *and* on what the SSO
   provider and EHR supply. Never assume `email` or `name` is present.

8. **End the session** with `logout` (`GET /auth/logout`) when the user signs out.

## Rules

- Treat every token as PHI-adjacent: this is a HIPAA/HITECH platform (see
  `security/commure-trust-center.yml`). Never log tokens, never persist them in client-side
  storage beyond the session, and never share a token across tenants.
- Do not fall back to the implicit flow. Commure's authorize endpoint supports it, but the
  authorization code flow with PKCE is the only grant to use for new clients.
- One tenant per token. The tenant id is part of the host, so a token is scoped to a host.
