# 8 Service APIs

This section provides a reference for the APIs that are implemented by the Analytics Building Block. The APIs defined here establish a blueprint for how the building block interacts with other building blocks and applications. Additional APIs may be implemented by a solution, but the listed APIs define the minimal set of functionality that every implementation provides.

The [GovStack architecture](https://specs.govstack.global/architecture) describes how 'adaptors' may be used to translate an existing API to the patterns described here. An existing product, for example an SDMX platform or a data warehouse, can be made conformant by an adaptor that provides these interfaces.

## 8.1 OpenAPI definition

The core interface is defined in [`api/swagger.yaml`](../api/swagger.yaml) (OpenAPI 3.1, version `0.1.0-draft`). It covers the five core functionalities.

{% hint style="info" %}
Once the OpenAPI file is published at its final location, it will be embedded here with the GitBook OpenAPI widget, linking to the raw URL of the file.
{% endhint %}

**Basics that apply to all operations:**

* HTTP according to RFC 9110 over TLS; every request carries a bearer token according to RFC 6750, issued through the Identity BB (inherited from the cross-functional requirements).
* Requests and responses use JSON according to RFC 8259 unless stated otherwise; points in time follow RFC 3339.
* Errors are returned as Problem Details according to RFC 9457 (`application/problem+json`). Validation errors of a delivery list the affected row and field.
* Read permissions are governed by the access management of the platform. The building block returns no data and sends no events that the caller is not authorised for.
* Every operation carries the property `x-govstack-requirement` with `MUST` (required in the core) or `SHOULD` (recommended). It corresponds to the level `REQUIRED` or `RECOMMENDED` of the requirements in [Section 6](6-functional-requirements.md), each of which names its operation in `api_operation_id`.

## 8.2 Operations

The operations are grouped by the tags of the OpenAPI definition.

### 8.2.1 Catalogue

Datasets, schemas, versions and code lists. Functionality: [Data Catalogue](requirements/data-catalogue.md).

| Operation | Method and path | Level | Purpose |
| --------- | --------------- | ----- | ------- |
| `getCatalogue` | `GET /catalogue` | MUST | Datasets and indicators with their versions, executable calculation languages, size limit for asynchronous downloads; JSON-LD context (DCAT 3) |
| `createDataset` | `POST /datasets` | MUST | Create a dataset; the building block sets the origin |
| `getDataset` | `GET /datasets/{dataset_id}` | MUST | Catalogue entry of a dataset |
| `putSchema` | `PUT /datasets/{dataset_id}/schema` | MUST | Register a Table Schema; creates a new schema version |
| `getCurrentSchema` | `GET /datasets/{dataset_id}/schema` | MUST | Current Table Schema |
| `getSchemaVersion` | `GET /datasets/{dataset_id}/schema/{schema_version}` | MUST | Earlier Table Schema |
| `listDatasetVersions` | `GET /datasets/{dataset_id}/versions` | MUST | All versions, including withdrawn ones |
| `getDatasetVersion` | `GET /datasets/{dataset_id}/versions/{version}` | MUST | Metadata of a dataset version; `latest` instead of a number is recommended |
| `getCodelist` | `GET /codelists/{codelist_id}/{schema_version}` | MUST | Code list `<dataset_id>.<field>` as a JSON-stat dimension response |

### 8.2.2 Ingestion

Delivery and validation of dataset versions. Functionality: [Data Ingestion](requirements/data-ingestion.md).

| Operation | Method and path | Level | Purpose |
| --------- | --------------- | ----- | ------- |
| `deliverDatasetVersion` | `POST /datasets/{dataset_id}/versions` | MUST | Deliver a Data Package (multipart request according to RFC 7578) with header `Idempotency-Key`; answers `202` with a receipt |
| `getReceipt` | `GET /receipts/{receipt_id}` | MUST | Validation result of a delivery |
| `withdrawDatasetVersion` | `DELETE /datasets/{dataset_id}/versions/{version}` | MUST | Withdraw a dataset version with a reason; only data stewards |

### 8.2.3 Indicators

Indicator definitions and calculations. Functionality: [Indicator Calculation](requirements/indicator-calculation.md).

| Operation | Method and path | Level | Purpose |
| --------- | --------------- | ----- | ------- |
| `createIndicator` | `POST /indicators` | MUST | Create an indicator with identifier and title |
| `createDefinitionVersion` | `POST /indicators/{indicator_id}/versions` | MUST | Create and publish a definition version; immutable afterwards |
| `startCalculation` | `POST /indicators/{indicator_id}/calculations` | MUST | Start a calculation; answers `202` with its identifier |
| `getCalculation` | `GET /calculations/{calculation_id}` | MUST | Status of a calculation |

### 8.2.4 Values, Provenance and Export

Retrieval of indicator values, provenance records and dataset contents. Functionality: [Data Access](requirements/data-access.md).

| Operation | Method and path | Level | Purpose |
| --------- | --------------- | ----- | ------- |
| `getValues` | `GET /indicators/{indicator_id}/values` | MUST | Indicator values as JSON-stat 2.0 (default) or CSV; parameters `format` and `result_version` (MUST), `c` and `updatedAfter` (SHOULD) |
| `getLineage` | `GET /lineage/{calculation_id}` | MUST | Provenance record as W3C PROV-O in JSON-LD |
| `exportDatasetVersion` | `GET /datasets/{dataset_id}/versions/{version}/export` | MUST | Content of a dataset version as CSV; `410` if the content was deleted |
| `getDownload` | `GET /downloads/{download_id}` | SHOULD | Asynchronous result above the configured size limit, with SHA-256 checksum |

### 8.2.5 Events

Subscriptions and the events of the [event catalogue](requirements/publication-events.md). Functionality: [Publication Events](requirements/publication-events.md).

| Operation | Method and path | Level | Purpose |
| --------- | --------------- | ----- | ------- |
| `createSubscription` | `POST /subscriptions` | MUST | Subscribe to event types with a callback endpoint (`sink`) |
| `deleteSubscription` | `DELETE /subscriptions/{subscription_id}` | MUST | End a subscription |

The events themselves are defined as `webhooks` in the OpenAPI definition. Each is a CloudEvents 1.0 message (`application/cloudevents+json`) with a type prefixed by `org.govstack.analytics`.

| Event type | Level |
| ---------- | ----- |
| `dataset.version.published` | MUST |
| `dataset.version.withdrawn` | MUST |
| `indicator.definition.published` | MUST |
| `indicator.values.published` | MUST |
| `indicator.calculation.failed` | MUST |
| `delivery.checked` | SHOULD |
| `dataset.version.content_removed` | SHOULD |

## 8.3 Open points

{% hint style="warning" %}
* **Transfer of the Data Package.** The OpenAPI file specifies a multipart request; whether a ZIP archive or references to files should also be allowed is not yet decided.
* **Plain CSV delivery.** A delivery of a CSV file without a Data Package has been proposed but not decided.
* **Access classes.** Whether the building block maintains its own access classes for datasets and indicators is an open decision (see [Data Access](requirements/data-access.md)).
* **Address of the JSON-LD context document.** To be defined by the working group.
{% endhint %}
