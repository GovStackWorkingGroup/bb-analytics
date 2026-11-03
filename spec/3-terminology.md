# 3 Terminology

This section lists terms that this specification uses in a specific sense. General GovStack terms such as _Building Block_, _capability_, _functionality_, _core_, _extension_ and _cross-functional requirement_ are defined in the [GovStack common terminology](https://specs.govstack.global/architecture/2-common-terminology) and are not redefined here.

## 3.1 Terms

| Term | Description |
| ---- | ----------- |
| **Calculation** | One run that produces indicator values from an indicator definition version and the dataset versions it uses. It runs asynchronously and has the status `PENDING`, `RUNNING`, `SUCCEEDED` or `FAILED`. A calculation, the result version it produces and its provenance record share the same identifier. |
| **Calculation language** | The language in which the calculation of an indicator definition is written, given with the definition (for example `vtl-2.1` or `ossie-<version>`). Languages defined by an implementation carry the prefix `x-`. |
| **Code list** | The permitted codes of a field and their labels. The building block derives code lists from the `categories` of the fields in a table schema. A code list is identified as `<dataset_id>.<field>`, and its version is the schema version it was taken from. |
| **Consuming system** | A system that reads indicator values or datasets from the building block, for example a dashboard, a BI tool, a business application or an open data portal. |
| **Data steward** | A person or organisational unit responsible for datasets and indicators: describes datasets and their schemas, defines and publishes indicators, starts calculations and withdraws faulty deliveries. |
| **Dataset** | A described collection of data of one kind in the catalogue of the building block, for example _"labour force and unemployed persons by region"_. A dataset has a schema and any number of dataset versions. |
| **Dataset version** | One delivered state of a dataset. Dataset versions are numbered consecutively from 1 per dataset and are immutable. Where a single identifier is needed, it is written `<dataset_id>:<version>`. |
| **Definition version** | One version of an indicator definition. A published definition version is never changed; any change to formula, unit, scope or dimensions creates a new definition version. |
| **Delivery** | The transfer of one dataset version by a supplying system or data steward to the building block, answered with a receipt. |
| **Dimension** | A breakdown of an indicator, for example region or age group. The time period is a dimension of every indicator. Dimensions are derived from the calculation or stated explicitly in the indicator definition. |
| **Field role** | The role of a field in a table schema: `identifier`, `time`, `measure` or `attribute`. It determines how the building block treats the field in calculations and outputs. If the schema does not state it, it is derived by convention. |
| **Indicator** | A precisely defined key figure with a unit and dimensions, for example _"unemployment rate by region and year"_. |
| **Indicator definition** | The binding, versioned description of an indicator: identifier, title, unit, dimensions, input datasets and the calculation (language and expression). |
| **Indicator value** | A single value of an indicator for one combination of dimension codes and one time period, belonging to exactly one result version. |
| **Observation status** | The status of an indicator value as defined by SDMX, for example provisional or estimated, taken from the SDMX code list `CL_OBS_STATUS`. |
| **Origin** | Whether a dataset was delivered to the building block (`ORIGINAL`) or produced by the building block itself from other dataset versions (`DERIVED`). The building block sets the origin itself. |
| **Processing stage** | A label for the degree of processing of a dataset, taken from a code list that each installation defines, for example _raw / harmonised_ or _bronze / silver / gold_. |
| **Provenance record** | The permanent record of how a result version or derived dataset version was produced: the definition version, the identifiers and checksums of the input dataset versions, the supplying systems and the points in time. It contains no personal data and is kept even after the underlying data has been deleted. |
| **Receipt** | The acknowledgement of a delivery, with its own identifier and the status `VALIDATING`, `ACCEPTED` or `REJECTED`. An accepted receipt names the new dataset version and its checksum. |
| **Result version** | The immutable set of indicator values produced by one calculation. Queries return the latest result version unless an earlier one is requested. |
| **Schema** | The description of the fields of a dataset as a Table Schema of the Data Package Standard 2.0, against which every delivery is validated. Schemas are versioned. |
| **Subscribing system** | A system that has registered a subscription and receives events. |
| **Subscription** | The registration of a subscribing system for one or more event types and a callback endpoint (`sink`). |
| **Supplying system** | A system that delivers dataset versions to the building block, for example a registry or a business application. |
| **Withdrawal** | Removing a dataset version that should never have been delivered (for example test data or personal data delivered by mistake). The content is deleted; the metadata remain with the status `WITHDRAWN` and the reason. Only data stewards can withdraw. |

## 3.2 Abbreviations and standards

| Abbreviation | Meaning |
| ------------ | ------- |
| **Apache Ossie** | Open standard for exchanging metric definitions between data platforms and BI tools (formerly Open Semantic Interchange) |
| **CSV** | Comma-separated values, a plain text format for tables (RFC 4180) |
| **CloudEvents** | CNCF specification for a common format of event messages |
| **Data Package** | Data Package Standard 2.0 of the Open Knowledge Foundation (formerly Frictionless Data Package): data files plus a JSON description; includes Table Schema and Table Dialect |
| **DCAT** | Data Catalog Vocabulary of the W3C |
| **JSON-LD** | JSON for Linked Data (W3C), links plain JSON to a vocabulary through a context document |
| **JSON Lines** | Text format with one JSON object per line, always in UTF-8 |
| **JSON-stat** | Compact JSON format for statistical data cubes |
| **PROV-O** | Provenance ontology of the W3C |
| **RFC 9457** | Problem Details for HTTP APIs, the common error format |
| **SDMX** | Statistical Data and Metadata eXchange (ISO 17369), the international standard for exchanging statistical data and metadata |
| **UCUM** | Unified Code for Units of Measure |
| **VTL** | Validation and Transformation Language of the SDMX initiative |

The role of each standard in this building block is described in [Section 5.3](5-cross-cutting-requirements.md).
