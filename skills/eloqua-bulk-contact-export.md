---
name: eloqua-bulk-contact-export
description: >-
  Export contacts out of Oracle Eloqua in volume using the Bulk API's three-step
  definition → sync → retrieve pattern, including field resolution, sync polling and
  log inspection.
api: eloqua:eloqua-bulk-api
generated: '2026-08-13'
method: generated
source: openapi/eloqua-published-swagger.json + https://docs.oracle.com/en/cloud/saas/marketing/eloqua-rest-api/API_Call_Format_Bulk.html
operations:
  - GetContactFieldSearch
  - PostContactExportIndividual
  - PostSyncIndividual
  - GetSyncIndividual
  - GetSyncDataQuery
  - GetSyncLogSearch
  - DeleteContactExportDataQuery
---

# Export contacts from Oracle Eloqua (Bulk API 2.0)

Use this when you need more than a page of contacts. The Application API is synchronous and
Oracle explicitly says not to use it for high volumes. The Bulk API stages data asynchronously.

## Step 0 — Resolve the base URL. Do not skip this.

There is no fixed Eloqua host. Call the discovery endpoint once and cache the result for the
session; Oracle throttles it.

```
GET https://login.eloqua.com/id
Authorization: Bearer <access_token>
```

Read `urls.apis.rest.bulk` from the response — e.g. `https://secure.p03.eloqua.com/API/Bulk/{version}/`.
Substitute `2.0` for `{version}`. Everything below is relative to that base.

If any later call returns 401, re-call `/id`. A success means the instance moved data centers —
retry at the new base. A failure means stop.

## Step 1 — Resolve the field names you want

Eloqua addresses contact fields by statement, not by label. Get the real ones first.

- `GetContactFieldSearch` — `GET /api/bulk/2.0/contacts/fields`

Each item carries a `statement` such as `{{Contact.Field(C_EmailAddress)}}`. Those statements
are what go into the export definition's `fields` map. Guessing them is the most common cause
of a rejected definition.

## Step 2 — Create the export definition

- `PostContactExportIndividual` — `POST /api/bulk/2.0/contacts/exports`

```
Content-Type: application/json
```

Body carries `name`, a `fields` object mapping your output column names to Eloqua statements,
and optionally `filter` (a bulk filter expression) and `maxRecords`. Keep the export narrow —
Oracle's own guidance is to break large exports into chunks with a filter rather than pulling
everything.

The response carries a `uri` like `/contacts/exports/123`. Hold onto it.

## Step 3 — Create a sync against the definition

- `PostSyncIndividual` — `POST /api/bulk/2.0/syncs`

Body: `{"syncedInstanceUri": "/contacts/exports/123"}`

The response carries the sync `uri` and a `status` of `pending`.

## Step 4 — Poll the sync

- `GetSyncIndividual` — `GET /api/bulk/2.0/syncs/{id}`

Poll until `status` leaves `pending`/`active`. Terminal states are `success`, `warning` and
`error`. Back off between polls — there are no rate-limit headers to pace against, and a 429
gives you no Retry-After.

## Step 5 — Retrieve the data

- `GetSyncDataQuery` — `GET /api/bulk/2.0/syncs/{id}/data`

Paginate with `limit` and `offset`. The response carries `count`, `hasMore`, `items` and
`totalResults` — keep requesting while `hasMore` is true, advancing `offset` by `limit`.
Set `Accept: text/csv` if you want CSV instead of JSON.

## Step 6 — Read the logs on anything but a clean success

- `GetSyncLogSearch` — `GET /api/bulk/2.0/syncs/{id}/logs`

Logs carry `ELQ-nnnnn` status codes. The ones that matter here:

- `ELQ-00101` — sync processed, with the resulting status
- `ELQ-00102` — successfully exported members to csv file
- `ELQ-00124` — there was no data available to process
- `ELQ-00136` — staging area storage limit exceeded
- `ELQ-00141` / `ELQ-00142` — error exporting to csv, retrying
- `ELQ-00146` — temporary failure; retry the sync after an hour

Full catalog: `errors/eloqua-problem-types.yml`.

## Step 7 — Clean up the staging area

- `DeleteContactExportDataQuery` — `DELETE /api/bulk/2.0/contacts/exports/{id}/data`

Staging storage is capped and exhaustion surfaces as HTTP 413 "Storage space exceeded." Delete
synced data once you have it.

## Rules

- `Content-Type` is mandatory on every PUT and POST. Omitting it errors.
- There is NO idempotency key. Re-POSTing a definition creates a second definition; re-POSTing
  a sync starts a second sync. Track the `uri` you got back and reuse it.
- 503 means temporary. Check Eloqua System Status, then retry with increasing delay.
- The export definition is reusable. Create it once, then create a new sync each run.
