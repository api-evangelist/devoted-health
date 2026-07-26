---
name: Retrieve a Devoted Health member's claims and clinical data
description: Use the member-authorized Patient Access FHIR R4 API to read a consented Devoted Health member's Patient record, conditions, encounters, medications, and claims (ExplanationOfBenefit).
api: openapi/devoted-health-patient-access-openapi-original.json
base_url: https://api.prod.devoted.com/fhir
operations:
- GET /Patient
- GET /Patient/{id}
- GET /Condition
- GET /Encounter
- GET /Medication
- GET /ExplanationOfBenefit
---

# Retrieve a Devoted Health member's claims and clinical data

This skill reads a Devoted Health member's health data through the CMS Patient
Access FHIR R4 API. It requires the member's explicit OAuth consent.

## Prerequisites
- Your app is registered with Devoted (App Registration Form:
  https://forms.gle/UFYvckiAeEjWP49K9) and holds a Client ID + Secret.
- You have completed the SMART-on-FHIR OAuth 2.0 authorization-code flow against
  `https://login.devoted.com/authorize` / `https://login.devoted.com/oauth/token`
  and hold a member-authorized (patient-scoped) access token.
- Send `Authorization: Bearer <token>` and `Accept: application/fhir+json` on
  every request.

## Steps
1. Identify the member: `GET /Patient` returns the authorized member's Patient
   resource(s); note the Patient `id`. Optionally `GET /Patient/{id}`.
2. Pull diagnoses: `GET /Condition` (scoped to the authorized patient) returns a
   searchset Bundle of Condition resources.
3. Pull visits: `GET /Encounter` returns the member's Encounter Bundle.
4. Pull medications: `GET /Medication` returns the member's Medication Bundle.
5. Pull claims: `GET /ExplanationOfBenefit` returns the member's EOB (claims)
   Bundle — the CARIN Blue Button claims use case.
6. Page through results with `_count` and follow `Bundle.link` (relation `next`).

## Conventions & errors
- Responses are FHIR searchset Bundles; see conventions/devoted-health-conventions.yml.
- Errors are FHIR `OperationOutcome` (401 security / 403 forbidden / 404 not-found);
  see errors/devoted-health-problem-types.yml. On 401 refresh the token; on 403
  confirm the member consented and the token carries the needed patient scope.
