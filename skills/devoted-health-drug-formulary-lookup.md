---
name: Look up Devoted Health drug formulary coverage
description: Use the public FHIR R4 formulary API to look up covered drugs and formulary lists via MedicationKnowledge and List resources.
api: openapi/devoted-health-provider-directory-openapi-original.yml
base_url: https://fhir.devoted.com/fhir
operations:
- GET /MedicationKnowledge
- GET /List
- GET /InsurancePlan
---

# Look up Devoted Health drug formulary coverage

This skill queries Devoted Health's public drug formulary, exposed as FHIR
`MedicationKnowledge` (drug detail) organized by `List` (formulary lists).

## Steps
1. Find formulary lists: `GET /List` returns the formulary List resources; each
   List groups covered medications via `entry.item`.
2. Search a drug: `GET /MedicationKnowledge` (e.g. by code/name) returns drug
   coverage detail (tier, restrictions where published).
3. Relate to a plan: `GET /InsurancePlan` identifies which Devoted coverage
   product a formulary applies to.
4. Page with `_count` and follow `Bundle.link`; force JSON with `?_format=json`.

## Conventions & errors
- Responses are FHIR searchset Bundles; see conventions/devoted-health-conventions.yml.
- Errors are FHIR `OperationOutcome`; see errors/devoted-health-problem-types.yml.
