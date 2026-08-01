---
name: Sync organizations and contacts
description: Keep customer organizations and their contacts in sync with remberg from an ERP or CRM.
api: openapi/remberg-organizations-openapi.json
operations:
  - OrganizationsCfaController_findMany
  - OrganizationsCfaController_createOne
  - OrganizationsCfaController_findOneByOrganizationNumber
  - OrganizationsCfaController_findManyContacts
  - ContactsCfaController_createOne
---

# Sync organizations and contacts

Use the remberg Organizations and Contacts APIs to mirror your CRM/ERP customer records.

## Auth & conventions
- API key in the `authorization` header; base URL `https://api.remberg.de`.
- Organizations are keyed by a unique `organizationNumber` (URL-encode it). Creating a duplicate number returns `409`.
- Offset pagination via `page`/`limit`; full-text `search` and sorting supported.

## Steps
1. Look up an organization with `OrganizationsCfaController_findOneByOrganizationNumber` before creating, to avoid duplicates.
2. Create it with `OrganizationsCfaController_createOne` (returns the created organization; `409` on duplicate number).
3. List its contacts with `OrganizationsCfaController_findManyContacts`.
4. Add a contact with `ContactsCfaController_createOne` — requires first name, last name, and `organizationNumber`; optionally an email to link a remberg user account. A `209` on contact update means the record saved but the activation/verification email failed.

## Errors
- `403` — key lacks organization/contact rights.
- `404` — organization/contact not found.
- `409` — duplicate or ambiguous organization number.
