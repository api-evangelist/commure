---
name: Build a patient chart from the Commure FHIR API
description: Assemble a clinical summary for one patient — demographics, medications, allergies, conditions, vitals and labs — using FHIR search against the Commure Developer Platform, or the $everything compartment read.
generated: '2026-07-31'
method: generated
api: openapi/commure-fhir-openapi.yml
source: postman/commure-patient-chart-collection.json
operations:
  - searchResourcesType
  - getResource
  - fetchPatientRecordById
  - lastNObservationsQuery
  - searchAllResources
---

# Build a patient chart

Grounded in Commure's own published "Patient Chart" Postman collection and the
`Clinical Scenario: Telehealth Visit` collection. All paths below are
`/api/v1/{fhir_version}/...` on `https://api-{tenant-id}.developer.commure.com` — authenticate
first with `skills/commure-authenticate-smart-on-fhir.md`.

## Steps

1. **Find the patient.** `searchResourcesType` with `type=Patient`.
   - Browse: `GET /api/v1/{fhir_version}/Patient`
   - By name: `GET /api/v1/{fhir_version}/Patient?name=john`
   - Partial match on both name parts (Commure's own example):
     `GET /api/v1/{fhir_version}/Patient?family=val&given=g`
   The response is a FHIR **searchset Bundle** — read `Bundle.total` and follow
   `Bundle.link[relation=next]` for more pages. Never construct offsets by hand.

2. **Read the patient.** `getResource` with `type=Patient`, `id={patient-id}`.

3. **Pull the clinical panels.** Each is a `searchResourcesType` call:
   - Medications: `GET /api/v1/{fhir_version}/MedicationStatement?patient={patient-id}`
   - Allergies: `GET /api/v1/{fhir_version}/AllergyIntolerance?patient={patient-id}`
   - Conditions: `GET /api/v1/{fhir_version}/Condition?subject={patient-id}`
   - Vitals: `GET /api/v1/{fhir_version}/Observation?subject={patient-id}&category=vital-signs&code=85354-9,55284-4,8867-4,8331-1,9279-1,59408-5&_sort=-date`
   - Labs (BMP): `GET /api/v1/{fhir_version}/Observation?code=2951-2,2823-3,2075-0,1963-8,3097-3,2160-0,15074-8&subject={patient-id}`
   - Labs (CBC): `GET /api/v1/{fhir_version}/Observation?code=6690-2,718-7,4544-3,777-3&subject={patient-id}`
   Those LOINC code lists are Commure's published examples — reuse them verbatim rather than
   guessing codes.

4. **Prefer one round trip when you can.** Two cheaper alternatives to six searches:
   - `fetchPatientRecordById` — `GET /api/v1/{fhir_version}/Patient/{id}/$everything` returns the
     whole patient compartment as one Bundle. Narrow it with `_type`, `_since`, `start`, `end`
     and `_count`.
   - Reverse includes on a single search, as Commure's telehealth scenario does:
     `GET /api/v1/{fhir_version}/Patient?_id={patient-id}&_revinclude=Condition:subject&_revinclude=FamilyMemberHistory:patient&_revinclude=Observation:subject&_revinclude=Procedure:subject`

5. **Latest-value panels.** For "most recent N results per code", use `lastNObservationsQuery`
   (`GET /api/v1/{fhir_version}/Observation/$lastn?max={n}`) instead of sorting a full search
   client-side.

## Rules

- Ask for `application/fhir+json`; every response — success or error — is a FHIR resource.
- Errors are `OperationOutcome`, not RFC 9457 problem details. 404 means the resource type or id
  is unknown; 410 means it was deleted (use `getVersionHistoryResource` to read prior versions).
  See `errors/commure-problem-types.yml`.
- Read operations are safe to retry. Send `If-None-Match` with the cached ETag to get a cheap
  **304**.
- `{fhir_version}` is a required path segment. Confirm which releases the tenant serves with
  `discoverWhatVersionsServerSupports` or `getFHIRServerMetadata` before hard-coding `r4`.
- This is PHI. Request the narrowest scope that satisfies the chart, and never cache resources
  outside the tenant's boundary.
