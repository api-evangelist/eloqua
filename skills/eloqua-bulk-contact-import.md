---
name: eloqua-bulk-contact-import
description: >-
  Load or update contacts in Oracle Eloqua in volume through the Bulk API — define the
  import with an identifier field, stage the payload, sync it, and reconcile rejects.
api: eloqua:eloqua-bulk-api
generated: '2026-08-13'
method: generated
source: openapi/eloqua-published-swagger.json + https://docs.oracle.com/en/cloud/saas/marketing/eloqua-rest-api/API_Call_Format_Bulk.html
operations:
  - GetContactFieldSearch
  - PostContactImportIndividual
  - PostContactImportData
  - PostSyncIndividual
  - GetSyncIndividual
  - GetSyncLogSearch
  - GetSyncRejectSearch
  - DeleteContactImportData
---

# Import contacts into Oracle Eloqua (Bulk API 2.0)

Writing is where Eloqua's lack of idempotency bites. Read the rules at the bottom before you
send anything.

## Step 0 — Resolve the base URL

```
GET https://login.eloqua.com/id
Authorization: Bearer <access_token>
```

Use `urls.apis.rest.bulk` with `{version}` = `2.0`. Cache it for the session.

## Step 1 — Resolve field statements

- `GetContactFieldSearch` — `GET /api/bulk/2.0/contacts/fields`

You need the `statement` for every field you intend to write, plus the one you will use as the
match key (usually `{{Contact.Field(C_EmailAddress)}}`).

## Step 2 — Create the import definition

- `PostContactImportIndividual` — `POST /api/bulk/2.0/contacts/imports`

```
Content-Type: application/json
```

The definition names the `fields` map and, critically, `identifierFieldName` — the field Eloqua
matches on to decide update-vs-create. Two documented constraints:

- `ELQ-00112` — `identifierFieldName` must be contained within `fields` if specified. Include it.
- `ELQ-00111` — a field present in your file but absent from the definition is silently ignored.
- `ELQ-00121` — a field in the definition but missing from the file is reported.

The response carries a `uri` like `/contacts/imports/456`.

## Step 3 — Stage the data

- `PostContactImportData` — `POST /api/bulk/2.0/contacts/imports/{id}/data`

Send `Content-Type: application/json` (an array of records keyed by your definition's field
names) or `Content-Type: text/csv`. XML is not supported. Nothing has entered the Eloqua
database yet — this only writes the staging area.

## Step 4 — Sync it

- `PostSyncIndividual` — `POST /api/bulk/2.0/syncs`

Body: `{"syncedInstanceUri": "/contacts/imports/456"}`

This is the step that merges staged data into Eloqua. It is the irreversible one.

## Step 5 — Poll to a terminal state

- `GetSyncIndividual` — `GET /api/bulk/2.0/syncs/{id}`

Wait for `success`, `warning` or `error`. A `warning` means it landed but records were rejected —
go to step 6 regardless.

## Step 6 — Reconcile

- `GetSyncLogSearch` — `GET /api/bulk/2.0/syncs/{id}/logs`
- `GetSyncRejectSearch` — `GET /api/bulk/2.0/syncs/{id}/rejects`

Counters you should record every run: `ELQ-00001` total records processed, `ELQ-00004` contacts
created, `ELQ-00022` contacts updated, `ELQ-00049` total new records, `ELQ-00060` total rejected
records, `ELQ-00144` total records with rejected fields.

Failure codes worth branching on:

- `ELQ-00002` invalid email addresses / `ELQ-00011` duplicate email addresses
- `ELQ-00026` duplicate identifier — your match key is not unique in the payload
- `ELQ-00032` malformed records / `ELQ-00061` rejected records (missing fields)
- `ELQ-00122` validation errors on the definition; the sync will NOT proceed
- `ELQ-00105` / `ELQ-00107` error processing the staging file — Oracle's remediation is to
  delete the staging data, re-upload and sync again, and raise an SR if it repeats
- `ELQ-00135` multiple matched record limit exceeded
- `ELQ-00136` staging area storage limit exceeded
- `ELQ-00146` temporary failure — retry the sync after an hour

`/rejects` gives you the per-record detail; the logs give you the aggregate.

## Step 7 — Delete the staged data

- `DeleteContactImportData` — `DELETE /api/bulk/2.0/contacts/imports/{id}/data`

Free the staging area. Exhaustion surfaces as HTTP 413.

## Rules

- **No idempotency.** Eloqua documents no idempotency key or replay token. If a sync POST times
  out you do not know whether it started. Poll `GET /api/bulk/2.0/syncs` filtered by
  `syncedInstanceUri` before creating a second one, or you will merge the same batch twice.
- Reuse the import definition. Create it once; create a new sync per batch.
- `Content-Type` is mandatory on PUT and POST.
- No rate-limit headers exist. On 429, back off blind — there is no Retry-After.
- Contact-level security can restrict what the authenticating user may write. The token's
  `full` scope does not override it.
