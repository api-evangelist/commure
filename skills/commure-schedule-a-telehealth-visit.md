---
name: Schedule and run a telehealth visit on Commure
description: End-to-end scheduling and documentation flow — find open slots, create and confirm an Appointment, mark the Slot busy, attach the video-call details, open an Encounter, capture the pre-visit questionnaire, write the note and order, and close the Encounter.
generated: '2026-07-31'
method: generated
api: openapi/commure-fhir-openapi.yml
source: postman/commure-telehealth-visit-collection.json
operations:
  - postBatchOrTransaction
  - searchResourcesType
  - createResource
  - updateResource
  - applyJSONPatchAsExtendedOperation
  - getResource
---

# Schedule and run a telehealth visit

This mirrors Commure's published `Clinical Scenario: Telehealth Visit` collection, step for step.
Authenticate first (`skills/commure-authenticate-smart-on-fhir.md`).

## Steps

1. **Seed or load the scenario.** `postBatchOrTransaction` — `POST /api/v1/{fhir_version}` with a
   `Bundle` of `type: transaction`. Commure's scenario uses this to create the Practitioner,
   Patient, Schedule and Slot prerequisites atomically. A transaction Bundle either fully applies
   or fully fails, so use it for any multi-resource setup instead of a sequence of creates.

2. **Find open slots.** `searchResourcesType` —
   `GET /api/v1/{fhir_version}/Schedule?actor={practitioner-id}&_revinclude=Slot:schedule`.
   The `_revinclude` pulls each Schedule's Slots into the same Bundle; check `Slot.status` for
   `free` before booking.

3. **Create the Appointment.** `createResource` —
   `POST /api/v1/{fhir_version}/Appointment` with participants referencing the Patient and the
   Practitioner and `slot` referencing the chosen Slot.
   *Make this retry-safe:* send `If-None-Exist` with a search query that uniquely identifies the
   appointment (FHIR conditional create). On a retry the server returns the existing Appointment
   with **200** instead of creating a duplicate. See `conventions/commure-conventions.yml`.

4. **Confirm participation.** `updateResource` — `PUT /api/v1/{fhir_version}/Appointment/{id}`,
   once for the patient's participation status and once for the practitioner's.
   Always send `If-Match` with the ETag from the previous read. Two coordinators confirming at the
   same time will otherwise silently clobber each other; with `If-Match` the loser gets **409/412**
   and can re-read and retry.

5. **Mark the Slot busy.** `applyJSONPatchAsExtendedOperation` —
   `POST /api/v1/{fhir_version}/Slot/{id}/$commure-json-patch` with an RFC 6902 patch setting
   `/status` to `busy`. (Commure's scenario issues a bare HTTP `PATCH` on the resource; the
   reference collection exposes the same semantics as this extended operation, and
   `$fhir-patch` for STU3.) Patch rather than PUT so you do not overwrite fields another client
   changed.

6. **Attach the video call.** Patch the Appointment with the telehealth link/telecom details.

7. **Open the Encounter.** `createResource` — `POST /api/v1/{fhir_version}/Encounter` referencing
   the Patient and the Appointment.

8. **Pre-visit survey.** `createResource` for the `QuestionnaireResponse`, then `getResource` —
   `GET /api/v1/{fhir_version}/QuestionnaireResponse/{id}` when the clinician reviews it.

9. **Review history before the visit.**
   - Reported medications: `GET /api/v1/{fhir_version}/MedicationStatement?subject=Patient/{patient-id}`
   - Whole history in one call:
     `GET /api/v1/{fhir_version}/Patient?_id={patient-id}&_revinclude=Condition:subject&_revinclude=FamilyMemberHistory:patient&_revinclude=Observation:subject&_revinclude=Procedure:subject`

10. **Document.** `createResource` for `ClinicalImpression` (the clinician's note), then
    `ServiceRequest` (follow-up), then `MedicationRequest` (the order).

11. **Close the Encounter.** Patch `Encounter.status` to `finished`.

## Rules

- Use a **transaction Bundle** whenever two or more resources must land together (setup in step 1;
  Commure's medication scenario uses the same pattern to create a MedicationAdministration and
  update the MedicationRequest atomically).
- Every write is guarded: `If-None-Exist` for creates, `If-Match` for updates and deletes.
  Never retry a bare `POST` without `If-None-Exist`.
- `409 Conflict` / `412 Precondition Failed` are expected outcomes, not bugs — re-read, merge,
  retry. `422` means profile/business validation failed; run `validateResource2`
  (`POST /{type}/$validate`) before submitting to find out why.
- Search results are Bundles. Follow `link[relation=next]`; never invent page parameters.
