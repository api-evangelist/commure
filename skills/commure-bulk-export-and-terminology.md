---
name: Run a FHIR bulk export and resolve codes with Commure terminology services
description: Kick off a FHIR Bulk Data $export, poll it with $async-status and cancel with $async-cancel, then normalise the exported codes using Commure's CodeSystem, ValueSet and ConceptMap operations.
generated: '2026-07-31'
method: generated
api: openapi/commure-fhir-openapi.yml
source: postman/commure-fhir-api-collection.json
operations:
  - exportDataFromFHIRServer
  - asyncStatus
  - asyncCancel
  - importDataFromFHIRServer
  - bulkDeleteDataFromFHIRServer
  - conceptLookUpDecomposition
  - codeSystemBasedValidation2
  - valueSetExpansion2
  - valueSetBasedValidation2
  - conceptTranslation
  - subsumptionTesting2
  - closureTableMaintenance
---

# Bulk export and terminology resolution

Two capabilities that make Commure's FHIR server useful for analytics and data migration, both
grounded in the published `Commure FHIR API` collection.

## Part 1 — Bulk Data export

1. **Kick off the export.** `exportDataFromFHIRServer` —
   `POST /api/v1/{fhir_version}/$export`. This is an asynchronous, kickoff-and-poll operation:
   expect a `202`/`303` rather than the data. Narrow the job with the FHIR Bulk Data parameters
   the contract declares — `_since`, `_type`, `_typeFilter`, `_lists`.

2. **Poll for completion.** `asyncStatus` —
   `GET /api/v1/{fhir_version}/$async-status?operation={operation}&operation_uri={url}`.
   Poll with backoff; do not busy-loop. Exports over a whole health system are long-running.

3. **Cancel when you must.** `asyncCancel` —
   `POST /api/v1/{fhir_version}/$async-cancel?operation={operation}&operation_uri={url}`.
   Cancel abandoned jobs rather than leaving them to run — you are sharing a tenant.

4. **Load data back** with `importDataFromFHIRServer` (`POST /$import`), and remove a bounded
   window with `bulkDeleteDataFromFHIRServer` (`POST /$bulk-delete`, parameters `_since`,
   `_until`, `_type`, `_typeFilter`, `_lists`).

> `$bulk-delete` is destructive and tenant-wide. Never call it without an explicit, scoped
> `_type`/`_since`/`_until`, and never as part of an automated retry.

## Part 2 — Terminology

- **Decompose a code.** `conceptLookUpDecomposition` —
  `POST /api/v1/{fhir_version}/CodeSystem/$lookup`. Supply *both* a system and a code (as
  `system`+`code` or as a `coding`). Returns definition, status, designations and properties.
- **Validate a code.** `codeSystemBasedValidation2` —
  `POST /api/v1/{fhir_version}/CodeSystem/$validate-code`. Supply exactly one of
  `code`+`system`, `coding`, or `codeableConcept`. When called at server level rather than on an
  instance, one of `url` or `codeSystem` is required.
- **Expand a value set.** `valueSetExpansion2` — `POST /api/v1/{fhir_version}/ValueSet/$expand`
  (or `valueSetExpansion` on an instance).
- **Validate against a value set.** `valueSetBasedValidation2` —
  `POST /api/v1/{fhir_version}/ValueSet/$validate-code`.
- **Translate between vocabularies.** `conceptTranslation` —
  `POST /api/v1/{fhir_version}/ConceptMap/$translate`. Supply exactly one of `code`, `coding` or
  `codeableConcept`.
- **Test hierarchy.** `subsumptionTesting2` —
  `POST /api/v1/{fhir_version}/CodeSystem/$subsumes`, honouring the code system's
  `hierarchyMeaning`.
- **Maintain a closure table.** `closureTableMaintenance` —
  `POST /api/v1/{fhir_version}/$closure`, for incremental subsumption caching.

## Rules

- Every terminology operation takes and returns a FHIR `Parameters` resource as
  `application/fhir+json`.
- Terminology calls are read-only and safe to retry; bulk operations are not — always resume by
  polling `$async-status` for an existing operation rather than re-kicking `$export`.
- Confirm the server actually advertises these operations for your tenant with
  `getFHIRServerMetadata` (`GET /api/v1/{fhir_version}/metadata`) before depending on them.
- Exported bulk data is PHI in the largest possible quantity. Handle under the tenant's BAA
  (https://www.commure.com/legal/business-associate-agreement).
