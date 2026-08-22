# Oracle Eloqua (eloqua)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Oracle Eloqua is a marketing automation platform that provides tools for lead management, email marketing, and marketing campaign management through comprehensive REST APIs. It enables marketing teams to create, execute, and measure the effectiveness of marketing programs and campaigns.

**APIs.json:** [https://www.oracle.com/marketingcloud/products/marketing-automation/](https://www.oracle.com/marketingcloud/products/marketing-automation/)

## Scope

- **Type:** Index
- **Position:** Consumer
- **Access:** 3rd-Party

## Tags

- CRM
- Email Marketing
- Lead Management
- Marketing Automation

## Timestamps

- **Created:** 2025-01-01
- **Modified:** 2026-05-19

## APIs

### Eloqua REST API

The primary REST API for Oracle Eloqua, providing access to marketing automation, contact management, campaign operations, and analytics.

- **Human URL:** [https://docs.oracle.com/en/cloud/saas/marketing/eloqua-rest-api/](https://docs.oracle.com/en/cloud/saas/marketing/eloqua-rest-api/)
- **Base URL:** `https://secure.p01.eloqua.com/API/REST/2.0/`

#### Tags

- Campaigns
- Contacts
- Marketing

#### Properties

- [Documentation](https://docs.oracle.com/en/cloud/saas/marketing/eloqua-rest-api/)
- [OpenAPI](openapi/eloqua-rest-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/eloqua-rest.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/eloqua-rest.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Eloqua Bulk API

Bulk API for high-volume data operations including imports, exports, and synchronization of large datasets.

- **Human URL:** [https://docs.oracle.com/en/cloud/saas/marketing/eloqua-rest-api/BulkAPI.html](https://docs.oracle.com/en/cloud/saas/marketing/eloqua-rest-api/BulkAPI.html)
- **Base URL:** `https://secure.p01.eloqua.com/API/Bulk/2.0/`

#### Tags

- Bulk Operations
- Data Export
- Data Import

#### Properties

- [Documentation](https://docs.oracle.com/en/cloud/saas/marketing/eloqua-rest-api/BulkAPI.html)
- [OpenAPI](openapi/eloqua-bulk-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/eloqua-bulk.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/eloqua-bulk.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [LinkedIn](https://www.linkedin.com/company/eloqua)
- [Getting Started](https://docs.oracle.com/en/cloud/saas/marketing/eloqua-rest-api/GettingStarted.html)
- [Authentication](https://docs.oracle.com/en/cloud/saas/marketing/eloqua-rest-api/Authentication.html)
- [Support](https://support.oracle.com/)
- [Terms of Service](https://www.oracle.com/legal/terms.html)
- [Privacy Policy](https://www.oracle.com/legal/privacy/)
- [S D Ks](https://docs.oracle.com/en/cloud/saas/marketing/eloqua-rest-api/SDKs.html)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
