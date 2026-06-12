MERMAID Architecture Ethos

This repository documents the architecture of the MERMAID software and data ecosystem.

Its purpose is to provide a durable, high-level view of how repositories, workflows, and data products relate to one another. The goal is not to document implementation details, but to explain the structure of the system and the reasoning behind it.

Core Principles

Repository names describe tools

Repositories are implementations.

Examples:

* mermaid-records
* mermaid-timelines
* mermaid-catalogs
* mermaid-buffer
* automaid
* cfneic
* mermaid-gcmt

Repositories may be replaced, merged, split, archived, or rewritten over time.

Directory names describe durable data families

Directories represent scientific or operational data products that persist independently of the software used to generate them.

Examples:

* raw/
* waveforms/
* metadata/
* associations/
* catalogs/
* records/
* timelines/

A single tool may produce multiple data families.

Multiple tools may contribute to the same data family.

Therefore data directories should not generally be named after software packages.

Prefer scientific concepts over implementation details

Names should describe what the data is, not how it was produced.

Prefer:

* waveforms/
* associations/
* catalogs/

over:

* automaid/
* processed/
* events/

unless the software identity itself is the important concept.

Preserve history

Historical layouts should not be rewritten simply for aesthetic reasons.

Legacy structures should be retained in a dedicated legacy/ area when practical, allowing modern workflows to evolve without losing provenance or breaking historical references.

Architecture before implementation

When making structural changes:

1. Define the conceptual model.
2. Define the data families.
3. Define the relationships between them.
4. Implement the software changes.

The architecture should drive the codebase, not the other way around.

Initial Data Families

The current working model is:

* raw/
* waveforms/
* metadata/
* associations/
* catalogs/
* records/
* timelines/

These are expected to evolve as the ecosystem grows.

Architecture Repository

This repository is intended to serve as the central map of the MERMAID ecosystem.

It should contain:

* High-level workflow diagrams
* Repository relationship diagrams
* Data-flow diagrams
* Architecture decision records
* Notes on naming, provenance, and system evolution

The architecture should be understandable to a new contributor without requiring
inspection of individual repositories.
