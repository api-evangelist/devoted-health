---
name: Search the Devoted Health provider, pharmacy, and formulary directory
description: >-
  Query Devoted Health's public FHIR R4 Directory and Formulary APIs (no member
  auth required) for in-network providers, locations, organizations, and covered drugs.
api: conformance/devoted-directory-capabilitystatement.json
base_url: https://fhir.devoted.com/fhir
operations:
- GET /Practitioner
- GET /PractitionerRole
- GET /Organization
- GET /Location
- GET /HealthcareService
- GET /InsurancePlan
- GET /MedicationKnowledge
- GET /List
---

# Search the Devoted Health directory and formulary

The Provider & Pharmacy Directory API (PDEX Plan-Net) and Plan Coverage &
Formularies API (PDEX US Drug Formulary) are **public** FHIR R4 endpoints — no
OAuth token or member consent is required. Base URL: `https://fhir.devoted.com/fhir`.
The full capability surface is in `conformance/devoted-directory-capabilitystatement.json`.

## Steps

1. **Set the Accept header.** `Accept: application/fhir+json` (or add `?_format=json`).
2. **Find in-network practitioners.** `GET /PractitionerRole?...` returns the
   role links; expand `GET /Practitioner`, `GET /Organization`, and `GET /Location`
   to resolve names, affiliations, and addresses (see the reference graph in
   `data-model/devoted-data-model.yml`).
3. **Locate services and facilities.** `GET /HealthcareService` and `GET /Location`.
4. **Look up plan coverage.** `GET /InsurancePlan` for plan details.
5. **Check the drug formulary.** `GET /List` (formulary lists) and
   `GET /MedicationKnowledge` for covered-drug tiers and coverage rules.
6. **Page results.** Follow `Bundle.link[relation=next].url` (see
   `conventions/devoted-conventions.yml`).

## Rules

- Read-only, public data — no idempotency or write semantics.
- Verify the CapabilityStatement (`GET /metadata`) for supported search
  parameters before building queries.
- Errors are FHIR `OperationOutcome` (see `errors/devoted-problem-types.yml`).
