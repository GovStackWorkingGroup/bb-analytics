# Technical Specifications

A GovStack-aligned specification for turning raw government data into governed, reconciled, decision-ready intelligence — covering scope, requirements, data structures, standards, and the API surface,

The Analytics Building Block turns harmonised government data into trustworthy, decision-ready intelligence: a governed data lakehouse, multi-source reconciliation with human oversight, dual-engine querying, and AI-assisted insight generation — all built to interoperate with the rest of the GovStack Building Block ecosystem.

> 🎯 **In one line.** Turn harmonised government data into decisions you can defend — reconciled transparently, governed by humans, delivered through open APIs.

This specification (v0.2) sets out what a conformant Analytics Building Block must do, across thirteen sections. Each has its own page, so you can jump straight to what you need. Throughout, <mark style="color:$danger;">**REQUIRED**</mark> and <mark style="color:$info;">**RECOMMENDED**</mark> tags distinguish baseline conformance from good practice.

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><h3><i class="fa-compass" style="color:$primary;">:compass:</i></h3></td><td><strong>Description</strong></td><td>What this Building Block is and where it sits in GovStack</td><td><a href="2.-description.md">2. Description</a></td></tr><tr><td><h3><i class="fa-puzzle-piece" style="color:$primary;">:puzzle-piece:</i></h3></td><td><strong>Key Digital Functionalities</strong></td><td>The twelve capabilities every deployment provides</td><td><a href="4.-key-digital-functionalities.md">4. Key Digital Functionalities</a></td></tr><tr><td><h3><i class="fa-code" style="color:$primary;">:code:</i></h3></td><td><strong>OpenAPI Specification</strong></td><td>Illustrative endpoint definitions</td><td><a href="13.-openapi-3.0-specification-appendix.md">13. OpenAPI 3.0 Specification (Appendix)</a></td></tr></tbody></table>

### 🧱 Foundations

1. [Version History](1.-version-history.md) — how this specification has evolved
2. [Description](2.-description.md) — scope, principles, and where it sits in GovStack
3. [Terminology](3.-terminology.md) — the vocabulary used throughout

### ⚙️ Capabilities and requirements

4. [Key Digital Functionalities](4.-key-digital-functionalities.md) — what every deployment must provide
5. [Cross-Cutting Requirements](5.-cross-cutting-requirements.md) — the non-functional baseline: privacy, governance, security, resilience
6. [Functional Requirements](6.-functional-requirements.md) — detailed, testable requirements, each tagged REQUIRED or RECOMMENDED

### 🗄️ Data, standards, and integration

7. [Data Structures](7.-data-structures.md) — the key data objects and their attributes
8. [Standards](8.-standards.md) — the open standards this Building Block complies with
9. [Building Block Interactions](9.-building-block-interactions.md) — how it fits with the rest of GovStack
10. [Other Resources](10.-other-resources.md) — key decisions, future directions, related specifications

### 📎 Appendices

11. [Internal Workflows and Data Lifecycle](11.-internal-workflows-and-data-lifecycle-appendix.md) — the ingestion-to-insight pipeline, step by step
12. [Reference Use Cases](12.-reference-use-cases-appendix.md) — proactive social support, SME risk, and climate NDC tracking
13. [OpenAPI 3.0 Specification](13.-openapi-3.0-specification-appendix.md) — illustrative endpoint definitions

***

Built to be domain-agnostic: the same capabilities that power a household-welfare use case apply, unchanged, to a climate NDC-tracking deployment. This specification is part of the wider [GovStack](https://specs.govstack.global/) Building Block catalogue.
