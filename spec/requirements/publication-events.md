---
title: Publication Events
description: |
  Subscribing systems are notified when new versions of datasets and indicators are published, when
  a calculation fails or when a delivery has been checked, so that they do not have to poll.
realises: [reusable-statistics]
conformance: CORE
requirements:
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block registers a subscription for an event type and a callback endpoint.
    api_operation_id: createSubscription
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block assigns the identifier of a new subscription and returns the created subscription with id, types, sink and protocol in the response.
    api_operation_id: createSubscription
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block creates a subscription only for an authenticated caller and assigns it to that caller.
    api_operation_id: createSubscription
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block removes a subscription at the request of the system it is assigned to.
    api_operation_id: deleteSubscription
  - status: DRAFT
    level: RECOMMENDED
    observability: OBSERVABLE
    interaction: EVENT
    statement: The Analytics Building Block confirms a callback endpoint with a validation request before it sends events to it.
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: EVENT
    statement: The Analytics Building Block sends the mandatory events of the event catalogue with the types, triggers and contents defined there.
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: EVENT
    statement: The Analytics Building Block sends indicator.calculation.failed when a calculation fails.
  - status: DRAFT
    level: RECOMMENDED
    observability: OBSERVABLE
    interaction: EVENT
    statement: The Analytics Building Block sends the recommended events delivery.checked and dataset.version.contentRemoved of the event catalogue.
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: EVENT
    statement: The Analytics Building Block sends delivery.checked to the system that made the delivery, regardless of whether that system may read the dataset.
    documentation: |
      Condition: the implementation offers the event delivery.checked.
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: EVENT
    statement: The Analytics Building Block formats every event as a CloudEvents 1.0 message.
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: EVENT
    statement: The Analytics Building Block signs every event so that the recipient can verify sender and integrity.
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: EVENT
    statement: The Analytics Building Block sends an event about a dataset or indicator only to subscribers that may read it.
  - status: DRAFT
    level: REQUIRED
    observability: OBSERVABLE
    interaction: EVENT
    statement: The Analytics Building Block includes only identifiers and version information in an event, no data values.
  - status: DRAFT
    level: RECOMMENDED
    observability: OBSERVABLE
    interaction: SERVICE_API
    statement: The Analytics Building Block reports errors of the subscription operations according to RFC 9457.
    api_operation_id: createSubscription
---

# Publication Events

## Subscriptions

A system subscribes with `POST /v1/subscriptions`, stating the event types, its callback endpoint (`sink`) and the protocol (in the core always `HTTP`). The subscription is bound to the authenticated caller. The building block assigns the identifier, which the subscriber needs to unsubscribe with `DELETE /v1/subscriptions/{subscriptionId}`; `GET /v1/subscriptions` lists its subscriptions. A validation request to the callback endpoint before events are sent there is recommended.

The field names of the subscription follow the CNCF CloudEvents Subscriptions API. Because that API is a working draft (0.1-wip), the subscription resource is defined in this specification itself until a version 1.0 appears.

## Event catalogue

The building block only distributes the events of this catalogue. Every event is a CloudEvents 1.0 message in structured JSON format and always carries `specversion`, `id`, `source` (base address of the installation), `type`, `time` and `subject` (path of the affected resource). Following the CloudEvents recommendation and the GovStack API Design Guide, the type starts with a reversed domain name that contains the BB code, here `global.govstack.analytics`. The field `data` contains only identifiers and version information, never data values. An event only goes to subscribers that may read the affected resource; the only exception is `delivery.checked` to the supplying system. Dataset versions are identified in events by the separate values `datasetId` and `version`.

| Type (without prefix) | Trigger | `subject` | Content of `data` | Level |
| --------------------- | ------- | --------- | ----------------- | ----- |
| `dataset.version.published` | A delivery was validated and accepted as a new dataset version | `datasets/{datasetId}` | `datasetId`, `version`, `schemaVersion`, `checksum` | Required |
| `dataset.version.withdrawn` | A data steward withdrew a dataset version | `datasets/{datasetId}` | `datasetId`, `version`, `reason` | Required |
| `indicator.definition.published` | A new version of an indicator definition was published | `indicators/{indicatorId}` | `indicatorId`, `definitionVersion` | Required |
| `indicator.values.published` | A calculation produced a new result version | `indicators/{indicatorId}` | `indicatorId`, `definitionVersion`, `resultVersion` | Required |
| `indicator.calculation.failed` | A calculation failed; no new result version was produced | `indicators/{indicatorId}` | `indicatorId`, `definitionVersion`, `calculationId` | Required |
| `delivery.checked` | Validation of a delivery is complete, accepted or rejected | `receipts/{receiptId}` | `receiptId`, `datasetId`, `status`; if accepted also `version`, `checksum` and `receivedAt` | Recommended |
| `dataset.version.contentRemoved` | The content of a dataset version was deleted or anonymised under an operational rule; the metadata remain | `datasets/{datasetId}` | `datasetId`, `version` | Recommended |

`delivery.checked` is meant for supplying systems: they can subscribe to the receipt instead of polling it. It always goes to the system that made the delivery, regardless of whether that system may read the dataset, because it is that system's own receipt.
