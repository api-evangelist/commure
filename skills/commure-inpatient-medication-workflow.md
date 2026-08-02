---
name: Track an inpatient medication order from prescription to administration
description: Place a MedicationRequest, poll the resource history for changes with _since and the next link, find the MedicationDispense for a prescription, and record the MedicationAdministration atomically with the order update.
generated: '2026-07-31'
method: generated
api: openapi/commure-fhir-openapi.yml
source: postman/commure-inpatient-medication-workflow-collection.json
operations:
  - createResource
  - searchResourcesType
  - getVersionHistoryAllResourcesType
  - applyJSONPatchAsExtendedOperation
  - postBatchOrTransaction
---

# Inpatient medication workflow

Mirrors Commure's published `Clinical Scenario: Inpatient Medication Workflow` collection. This
is the reference pattern for **polling a FHIR server for change** without missing or double-reading
records.

## Steps

1. **Seed the scenario.** `postBatchOrTransaction` — `POST /api/v1/{fhir_version}` with a
   transaction Bundle.

2. **Physician places the order.** `createResource` —
   `POST /api/v1/{fhir_version}/MedicationRequest`, with `If-None-Exist` so a retried submission
   cannot create a duplicate prescription.

3. **Take a snapshot of active orders.** `searchResourcesType` —
   `GET /api/v1/{fhir_version}/MedicationRequest`. Record the server timestamp; it becomes your
   watermark.

4. **Poll for change with the type history, not repeated searches.**
   `getVersionHistoryAllResourcesType` —
   `GET /api/v1/{fhir_version}/MedicationRequest/_history?_since={watermark}`.
   The history Bundle returns every version created since the watermark, including updates and
   deletes that a plain search would hide.

5. **Walk the history pages by link.** When more history exists, the Bundle carries
   `link[relation=next]` with an **opaque URL**. `GET` that URL verbatim. Commure's scenario stores
   it in a `link-to-next-url` variable precisely because it must not be reconstructed. Updates that
   happen mid-walk are picked up correctly by following the link chain rather than re-issuing
   `_since`.

6. **Find the dispense for a prescription.** `searchResourcesType` —
   `GET /api/v1/{fhir_version}/MedicationDispense?prescription={medication-request-id}`.

7. **Record administration atomically.** `postBatchOrTransaction` —
   `POST /api/v1/{fhir_version}` with a transaction Bundle that both creates the completed
   `MedicationAdministration` and updates the `MedicationRequest` status. Do **not** do this as two
   calls: a partial failure would leave an administered dose against an order that still reads as
   active.

## Rules

- Medication ordering is the highest-consequence write on this API. Every create carries
  `If-None-Exist`; every update carries `If-Match`. A retry without them can duplicate a
  prescription.
- Use `_history?_since=` for change detection, not polling search. Search shows current state only —
  you will miss intermediate versions and deletes.
- Follow `next` links verbatim; never synthesise paging parameters.
- Cross-resource state changes go in one transaction Bundle.
- On `409`/`412`, re-read the current version and re-apply; on `422`, validate the resource with
  `validateResource2` before resubmitting. See `errors/commure-problem-types.yml`.
