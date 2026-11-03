# 5 Cross-Cutting Requirements

The Analytics Building Block extends the GovStack [cross-functional requirements](https://specs.govstack.global/architecture) (CFRs). It inherits all of them – security, deployment, observability, data handling – and does not restate them. This section describes how the inherited requirements apply to this building block, the rules that apply across all of its functionalities, and the standards it uses.

## 5.1 Requirements

### 5.1.1 Inherited from the cross-functional requirements

The following topics are fully covered by the CFRs and are not specified again here:

* **Transport and security:** HTTP according to RFC 9110 over TLS, API versioning and access logging.
* **Authentication:** every request carries an OAuth 2.0 access token (bearer token according to RFC 6750), issued through the Identity BB; officials use user tokens, systems and other building blocks use client credentials (see [Section 8](8-service-apis.md)). The building block checks the roles in the token; it defines no authentication mechanism of its own.
* **Exchange across organisational boundaries:** deliveries from other organisations go through the Information Mediator.
* **Operation:** availability, deployment, scaling and infrastructure are governed by the CFRs on deployment and by the Cloud Infrastructure BB.

### 5.1.2 Rules across all functionalities

The following rules apply to every functionality of the building block. Where they are normative, they are stated as requirements in the respective functionality files in [Section 6](6-functional-requirements.md).

* **Authorisation.** Read permissions are governed by the access management of the platform. The building block returns no data and sends no events that the caller is not authorised for (see [Data Access](requirements/data-access.md) and [Publication Events](requirements/publication-events.md)).
* **Error model.** All operations defined by this specification report errors according to RFC 9457 (Problem Details for HTTP APIs).
* **Immutability.** Dataset versions, published definition versions and result versions are immutable. Corrections create new versions; withdrawal deletes content but keeps the metadata.
* **No data values in events.** Events carry only identifiers and version information (see [Publication Events](requirements/publication-events.md)).
* **Character encoding.** Every implementation accepts text formats in UTF-8 (see [Data Ingestion](requirements/data-ingestion.md)).

### 5.1.3 Personal data

Personal data does not appear in the core model. Datasets may contain pseudonyms, but the building block treats them like any other attribute and does not link them to persons. Pseudonymisation and re-identification are the task of the registry or of the receiving business application.

Indicator values are aggregated and contain no personal data, so they are not subject to the retention limits of the underlying data. Provenance records and events contain no personal data either. When and why underlying data is deleted or anonymised – for example because data protection law such as the GDPR requires it – is up to the implementation and its operation, not this specification. The specification only requires that withdrawals and content removals are visible.

This specification does not confer legal compliance; it is designed not to block it.

## 5.2 Exceptions to the Cross-Functional Requirements

None. The Analytics Building Block meets all inherited cross-functional requirements without exception.

## 5.3 Standards

The standards divide three questions between them: **DCAT** describes which data exists, **SDMX** describes how statistical values are structured and which codes apply, and **PROV-O** describes where a value comes from. The others govern files, events and errors.

### 5.3.1 Standards used by the core

| Standard | Publisher | What it covers | Role in this building block |
| -------- | --------- | -------------- | --------------------------- |
| [Data Package Standard 2.0](https://datapackage.org/) (Data Package, Data Resource, Table Schema, Table Dialect) | Open Knowledge Foundation | Package format: data files (e.g. CSV) plus a JSON description with fields, types, mandatory fields and keys | Delivery format for dataset versions; the Table Schema is also the schema against which deliveries are validated |
| [CSV (RFC 4180)](https://www.rfc-editor.org/rfc/rfc4180) | IETF | Simple text format for tables | Ingestion and export, also for suppliers with little digitalisation; output of indicator values in long format |
| [JSON Lines](https://jsonlines.org/) | Open specification | One JSON object per line, UTF-8 | Delivery format for dataset versions alongside CSV (recommended) |
| [DCAT 3](https://www.w3.org/TR/vocab-dcat-3/) | W3C | Vocabulary for data catalogues: datasets, distributions, publisher, licence, timeliness | Vocabulary for the dataset attributes, linked in the catalogue responses through a JSON-LD context |
| [JSON-LD 1.1](https://www.w3.org/TR/json-ld11/) | W3C | Links plain JSON to a vocabulary through a context document | Context of the catalogue responses (DCAT) and serialisation of the provenance record (PROV-O) |
| [SDMX 3.0](https://sdmx.org/) (ISO 17369) | SDMX sponsors (BIS, ECB, Eurostat, IMF, OECD, UN, World Bank) | Model for statistical data: dimensions, measures, attributes, code lists, observations; REST API for queries | Structure of indicators, code lists (`CL_OBS_STATUS`, `CL_UNIT_MULT`, `CL_FREQ`), time format and filter syntax of the values API |
| [JSON-stat 2.0](https://json-stat.org/) | Open specification of the JSON-stat project | Compact JSON format for statistical cubes: dimensions, codes, labels, values and status | Default output format for indicator values |
| [W3C PROV-O](https://www.w3.org/TR/prov-o/) | W3C | Ontology for provenance: which entity was produced by which activity through which agent | Format of the provenance record |
| [UCUM](https://ucum.org/) | Regenstrief Institute | Agency-independent, machine-readable codes for units of measure (e.g. `%`) | Unit of measure of indicators |
| [VTL 2.1](https://sdmx.org/about-sdmx/about-vtl/) | SDMX Technical Working Group | Technology-independent language for validation and calculation rules on statistical data | Calculation language for indicator definitions (recommended) |
| [Apache Ossie](https://ossie.apache.org/) | Apache Software Foundation (Incubator) | YAML format for metrics, dimensions and relationships with SQL expressions | Calculation language for indicator definitions (recommended) |
| [CloudEvents 1.0](https://cloudevents.io/) | CNCF | Common metadata for events: type, source, time, ID | Format of all events |
| [RFC 9457 Problem Details](https://www.rfc-editor.org/rfc/rfc9457) | IETF | Common format for error responses of HTTP APIs | Error model of all operations |
| [Idempotency-Key header](https://datatracker.ietf.org/doc/draft-ietf-httpapi-idempotency-key-header/) | IETF (draft) | HTTP header with which a client marks a request uniquely so that repetitions are recognised | Detection of duplicate deliveries |

Further standards used by the core interface: JSON according to RFC 8259, multipart requests according to RFC 7578, points in time according to RFC 3339, and checksums as SHA-256 (FIPS 180-4). The field names of the subscription resource follow the [CloudEvents Subscriptions API](https://github.com/cloudevents/spec/blob/main/subscriptions/spec.md); because it is a working draft (0.1-wip), the resource is defined in this specification until a version 1.0 appears.
