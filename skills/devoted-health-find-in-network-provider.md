---
name: Find an in-network Devoted Health provider or location
description: Use the public Provider Directory FHIR R4 API to search Devoted Health's network of practitioners, organizations, healthcare services, and locations.
api: openapi/devoted-health-provider-directory-openapi-original.yml
base_url: https://fhir.devoted.com/fhir
operations:
- GET /Practitioner
- GET /PractitionerRole
- GET /Organization
- GET /Location
- GET /HealthcareService
- GET /InsurancePlan
---

# Find an in-network Devoted Health provider or location

This skill searches Devoted Health's public (non-member) provider and pharmacy
directory. No member consent is required, though requests are still authorized
via SMART-on-FHIR per the CapabilityStatement.

## Steps
1. Search practitioners: `GET /Practitioner` with FHIR search params (e.g.
   `name`, `address-state`, `specialty` via PractitionerRole) returns a Bundle.
2. Resolve a practitioner's roles/affiliations: `GET /PractitionerRole?practitioner={id}`
   links the Practitioner to its Organization, Location, and HealthcareService.
3. Search organizations: `GET /Organization` (e.g. by `name`, `address`).
4. Search locations: `GET /Location` (e.g. by `address-city`, `organization`).
5. Search services: `GET /HealthcareService` to find offered services at a site.
6. Look up plans: `GET /InsurancePlan` for Devoted's coverage products.

## Conventions & errors
- Responses are FHIR searchset Bundles; page with `_count` + `Bundle.link`.
- Force JSON with `?_format=json` (the server defaults to an HTML narrative UI).
- Errors are FHIR `OperationOutcome`; see errors/devoted-health-problem-types.yml.
