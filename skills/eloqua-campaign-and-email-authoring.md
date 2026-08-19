---
name: eloqua-campaign-and-email-authoring
description: >-
  Author and activate an Oracle Eloqua marketing campaign through the Application API —
  create the email asset, create the campaign, activate it, and read results back.
api: eloqua:eloqua-application-api
generated: '2026-08-13'
method: generated
source: openapi/eloqua-published-swagger.json + https://docs.oracle.com/en/cloud/saas/marketing/eloqua-rest-api/Getting_Started_Application.html
operations:
  - CreateIndividualPOSTRest20
  - ReadIndividualGETRest20
  - UpdateIndividualPUTRest20
  - SearchGETRest20
  - ActivateIndividualPOSTRest20
  - DeactivateIndividualPOSTRest20
---

# Author and activate an Eloqua campaign (Application API 2.0)

The Application API is synchronous and is meant for assets, not for data volume. Use it to build
and control campaigns; use the Bulk API for the contact data they act on.

> **operationId warning.** Oracle's published Swagger reuses operationIds across dozens of
> endpoints — `CreateIndividualPOSTRest20`, `ReadIndividualGETRest20`, `UpdateIndividualPUTRest20`
> and `SearchGETRest20` are each shared by many resources. Bind every call by **path + method**,
> never by operationId alone.

## Step 0 — Resolve the base URL

```
GET https://login.eloqua.com/id
Authorization: Bearer <access_token>
```

Use `urls.apis.rest.standard` with `{version}` = `2.0`.

## Step 1 — Create the email asset

- `CreateIndividualPOSTRest20` — `POST /api/rest/2.0/assets/email`

```
Content-Type: application/json
```

The `Email` entity is large (50 properties) and composes several sub-objects: `htmlContent`,
`contentSections`, `dynamicContents`, `images`, `hyperlinks`, `fieldMerges`, `attachments`,
`forms`. Build the minimum you need — `name`, `subject`, `htmlContent`, `emailGroupId`,
`senderName`, `senderEmail`, `replyToEmail` — and layer the rest.

Read it back with `ReadIndividualGETRest20` — `GET /api/rest/2.0/assets/email/{id}?depth=complete`
to see the whole composed object.

## Step 2 — Create the campaign

- `CreateIndividualPOSTRest20` — `POST /api/rest/2.0/assets/campaign`

A `Campaign` carries `elements[]` — the canvas steps — plus `startAt`/`endAt` timestamps and
`campaignType`. Each element is a `CampaignElement` with a `type`, a `position` and output
terminals wiring it to the next step. This is the hard part of the entity: the canvas graph is
expressed as elements plus terminal ids, so build it in one POST rather than trying to patch a
graph together.

## Step 3 — Activate

- `ActivateIndividualPOSTRest20` — `POST /api/rest/2.0/assets/campaign/active/{id}`

Deactivate back to draft with:

- `DeactivateIndividualPOSTRest20` — `POST /api/rest/2.0/assets/campaign/draft/{id}`

`ELQ-00132` ("Campaign not active") and `ELQ-00055` ("Campaign not found") are the two codes to
branch on when downstream calls reference the campaign.

## Step 4 — Send a one-off deployment (optional)

- `CreateIndividualPOSTRest20` — `POST /api/rest/2.0/assets/email/deployment`
- `ReadIndividualGETRest20` — `GET /api/rest/2.0/assets/email/deployment/{id}`

Use this for a direct send outside a campaign canvas.

## Step 5 — Find things

- `SearchGETRest20` — `GET /api/rest/2.0/assets/campaigns`
- `SearchGETRest20` — `GET /api/rest/2.0/assets/emails`

Query conventions on the Application API:

- `page` and `count` — `count` is capped at **1000**
- `search={term}{operator}{value}` with `=`, `!=`, `>`, `<`, `>=`, `<=`; `*` for partial match
- `orderBy=createdAt DESC`, or `sort` + `dir=asc|desc`
- `depth=minimal|partial|complete` — start at `minimal`, it scans the least data

You can search on a field even when the current `depth` does not return it.

## Rules

- `Content-Type: application/json` is mandatory on POST and PUT.
- **No idempotency key.** A retried `POST /assets/campaign` creates a second campaign. Search by
  `name` before re-creating.
- 409 Conflict means the name is already used by another asset.
- 412 means the asset you are deleting has dependencies — unwire it first.
- 403 "This service has not been enabled for your site." means the capability is not licensed on
  the tenancy, not that the call is malformed.
- Custom fields on contacts and accounts are addressed by numeric field id through `fieldValues[]`.
  Resolve ids from `GET /api/rest/2.0/assets/contact/fields` before writing them.
