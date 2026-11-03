---
title: Indicator Calculation
description: |
  Data stewards define indicators as versioned definitions that state how a key figure is calculated
  from which datasets. The building block calculates versioned indicator values from them and records
  for every result where it came from.
realises: [trustworthy-indicators]
conformance: CORE
requirements:
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block creates an indicator with the identifier and title given by a data steward.
    api_operation_id: createIndicator
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block leaves a published version of an indicator definition unchanged.
    api_operation_id: createDefinitionVersion
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block stores with every version of an indicator definition its calculation together with the calculation language.
    documentation: |
      Constraint: the language is stated as an identifier such as `vtl-2.1` or `ossie-<version>`;
      languages defined by an implementation carry the prefix `x-`.
    api_operation_id: createDefinitionVersion
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block lists the calculation languages it can execute in the catalogue.
    api_operation_id: getCatalogue
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block rejects an indicator definition whose calculation language it cannot execute with an error message.
    api_operation_id: createDefinitionVersion
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block determines the input datasets and the dimensions of an indicator definition from its calculation and shows them in the catalogue.
    api_operation_id: createDefinitionVersion
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block accepts a calculation request on POST /v1/indicators/{indicatorId}/calculations asynchronously and answers with 202 and the identifier of the calculation.
    documentation: |
      Constraint: the identifier of the calculation is also the identifier of the result version and
      of the provenance record.
    api_operation_id: startCalculation
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block returns on GET /v1/calculations/{calculationId} the status of a calculation (PENDING, RUNNING, SUCCEEDED, FAILED) and, on failure, the cause according to RFC 9457.
    api_operation_id: getCalculation
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block accepts an explicit statement of the dimensions in an indicator definition and requires it when the calculation language does not define the breakdown itself.
    api_operation_id: createDefinitionVersion
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block accepts and outputs the unit of measure of an indicator as a UCUM code.
    api_operation_id: createDefinitionVersion
  - status: DRAFT
    level: RECOMMENDED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block executes calculations written in VTL 2.1.
    api_operation_id: startCalculation
  - status: DRAFT
    level: RECOMMENDED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block executes calculations written in Apache Ossie.
    api_operation_id: startCalculation
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block links every indicator value to the definition version and the dataset versions it was calculated from.
    api_operation_id: getLineage
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block keeps the provenance record of a published indicator value after the referenced dataset versions have been deleted.
    api_operation_id: getLineage
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block stores the result of every calculation as a new result version.
    api_operation_id: getValues
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block skips withdrawn dataset versions for latest and in new calculations.
    api_operation_id: startCalculation
  - status: DRAFT
    level: RECOMMENDED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block reports errors of the indicator operations according to RFC 9457.
    api_operation_id: createDefinitionVersion
---

# Indicator Calculation

The **indicator definition** is at the centre of this building block: a binding, versioned description of how a key figure is calculated from which data.

## Definitions and definition versions

A data steward creates an indicator with `POST /v1/indicators` and adds definition versions with `POST /v1/indicators/{id}/versions`. Creating a definition version publishes it at the same time; from then on it is immutable. If formula, unit, scope or a dimension changes (for example new district boundaries), a new definition version is created, and values calculated with the old one stay linked to it.

The structure of a definition follows SDMX (Data Structure Definition and Dataflow). The **calculation** states its language. There is no mandatory language; VTL 2.1 and Apache Ossie are recommended. Definitions are therefore only portable between products that share a language, while inputs and outputs remain interchangeable regardless.

## Dimensions

The dimensions of an indicator do not follow directly from the field roles of its input datasets but from its calculation: they are the identifiers the result keeps, or the fields the calculation groups by. The time is converted into the SDMX time format. If the calculation language does not define the breakdown itself, as with Apache Ossie, the indicator definition states its dimensions explicitly.

## Calculations and result versions

A calculation is started with `POST /v1/indicators/{id}/calculations` and runs asynchronously. Its status is retrieved with `GET /v1/calculations/{id}`. A successful calculation produces a new, immutable **result version**; a failed one produces none and is announced with `indicator.calculation.failed`. Calculation, result version and provenance record share the same identifier.

Calculations can also be started on a schedule by the Scheduler BB, which only needs the same operation.

## Provenance

Every indicator value belongs to a result version, and every result version has exactly one provenance record with the definition version, the identifiers and checksums of the dataset versions used, the supplying systems and the points in time. It is kept permanently, even if the underlying data is later deleted or anonymised, and contains no personal data.
