# Analytics Building Block API

| File | Purpose |
| ---- | ------- |
| [`openapi.yaml`](openapi.yaml) | Canonical OpenAPI 3.1 definition of the HTTP surface, including the CloudEvents webhooks |
| [`coverage.yaml`](coverage.yaml) | Traceability of active functional requirements to operations |
| [`common/`](common/) | Vendored, version-pinned GovStack shared schemas (`Problem`, `ValidationProblem`, `FieldError`, `PageInfo`) |

The definition follows the [GovStack Cross-BB API Design Guide](https://github.com/jeremi/govstack-api-design-guide) version `0.3.0-draft`, declared in `info.x-govstack-api-guide`. The registered BB code is `analytics`; it appears in problem types (`https://govstack.global/problems/analytics/...`), OAuth scopes (`bb:analytics:...`) and event types (`global.govstack.analytics...`).

Events are delivered by HTTP push to subscribed endpoints and are therefore documented as OpenAPI `webhooks`; there is no AsyncAPI surface.

## Validation

With a checkout of the guide at `api-design-guide/` in the repository root:

```bash
uv tool install openapi-spec-validator==0.9.0
cd api-design-guide/linter && npm ci && node cli.mjs --repo-root ../..
```

## Known deviations from recommendations

These deviate from `SHOULD` rules of the guide and produce linter warnings, not errors:

* **5.4 Shallow path nesting.** `POST /v1/datasets/{datasetId}/versions/{version}/withdraw` has three levels after `/v1`, because a dataset version is addressed by dataset and version number throughout the specification.
* **9.2 camelCase field names.** Documents defined by external standards keep the names of those standards: `$schema` in Data Package and Table Schema, the JSON-LD keywords `@context`, `@id` and `@type`, and the PROV-O and DCTERMS terms (`prov:used`, `dct:issued`, …) in provenance records. The guide's carve-out for external standards (§1.7) covers them; the linter cannot tell them apart from BB-owned fields.
* **15.4 Conventional operation path.** The asynchronous operation resources are domain resources — receipts (`/v1/receipts/{receiptId}`), calculations (`/v1/calculations/{calculationId}`) and downloads (`/v1/downloads/{downloadId}`) — instead of a generic `/v1/operations/{operationId}`, because their identifiers are used elsewhere in the specification (the calculation identifier is also the result version and the provenance record).
