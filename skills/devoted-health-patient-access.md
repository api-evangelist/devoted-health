---
name: Retrieve a member's Devoted Health data via the Patient Access API
description: >-
  Authorize with OAuth 2.0 / OIDC, then read a Devoted Health member's claims,
  encounters, conditions, and medications from the FHIR R4 Patient Access API.
api: openapi/devoted-patient-access-openapi.json
base_url: https://api.prod.devoted.com/fhir
operations:
- GET /Patient
- GET /Patient/{id}
- GET /Condition
- GET /Encounter
- GET /ExplanationOfBenefit
- GET /Medication
---

# Retrieve a Devoted Health member's data (Patient Access API)

Devoted Health's Patient Access API is a HL7 FHIR R4 API that exposes a member's
own health data under the CMS Interoperability and Patient Access rule. Access is
member-authorized via OAuth 2.0 / OpenID Connect.

## Prerequisites

1. Register your application with Devoted's interoperability team via the
   [App Registration Form](https://forms.gle/UFYvckiAeEjWP49K9). You will receive a
   `client_id` and `client_secret` by Virtru-encrypted email (`interop@devoted.com`).
2. Discovery: `https://login.devoted.com/.well-known/openid-configuration`.

## Steps

1. **Authorize the member.** Send the member through the authorization code flow at
   `https://login.devoted.com/authorize` (scopes: `openid profile offline_access`,
   see `scopes/devoted-scopes.yml`). Exchange the code at
   `https://login.devoted.com/oauth/token` for an access token.
2. **Set headers.** On every request send `Authorization: Bearer <token>` and
   `Accept: application/fhir+json`.
3. **Find the patient.** Call `GET /Patient` (or `GET /Patient/{id}`) to resolve the
   member's FHIR Patient id.
4. **Read clinical + claims data**, always passing the required `patient` search
   parameter:
   - `GET /Condition?patient={id}` — problems/diagnoses.
   - `GET /Encounter?patient={id}&date=ge2024-01-01` — visits.
   - `GET /ExplanationOfBenefit?patient={id}&date=ge2024-01-01` — claims (CARIN BB).
   - `GET /Medication?patient={id}` — medications.
5. **Page results.** Responses are FHIR searchset Bundles; follow
   `Bundle.link[relation=next].url` until absent (see `conventions/devoted-conventions.yml`).

## Rules

- These operations are **read-only** (GET); there is no idempotency-key contract.
- Errors are FHIR `OperationOutcome` with a standard HTTP status — handle 401
  (token) and 403 (consent) explicitly (see `errors/devoted-problem-types.yml`).
- Only request the scopes you need; the member's consent governs access.
