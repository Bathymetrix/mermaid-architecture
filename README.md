# MERMAID Architecture

`mermaid-architecture` is the central documentation and planning repository for the long-term architecture of the MERMAID software and data ecosystem. It provides durable orientation across repositories, workflows, and data products; it does not replace the detailed documentation maintained by each implementation repository.

## Purpose

Use this repository to understand how MERMAID data move from acquisition through processing, review, derived products, and external archival, and to locate the architectural decisions that shape those workflows.

The architecture distinguishes canonical and workflow trees that own bytes from curated views that may use symlinks, independent derived-product branches, and external archival or reference systems. Established workflow trees such as `server/`, `processed/`, and `events/` remain canonical; `waveforms/` and `associations/` are curated views that improve access without taking ownership of those bytes.

## Architecture

The guiding principle is that repository names describe tools and directory names describe durable data families. It is a principle rather than an absolute rule: long-established workflow paths retain compatibility and operational meaning.

- [Architecture ethos](ethos.md) explains the naming, ownership, reconciliation, compatibility, and architecture-before-implementation principles.
- [Data lifecycle](data-lifecycle.md) describes the current lifecycle, curated views, independent branches, external publication, and explicit open questions.

## Repository Documentation

`repo_docs/` contains collected documentation snapshots from related repositories, including `mermaid-buffer`, `mermaid-catalogs`, `mermaid-gcmt`, `mermaid-ops`, `mermaid-records`, and `mermaid-timeline`. These snapshots support cross-repository architectural reasoning, but the originating repositories remain authoritative for their own implementation details and snapshots may lag live development. The top-level documents in this repository define the cross-repository architectural view.

## Status

The architecture is actively evolving. Unresolved questions are documented explicitly rather than decided prematurely; current examples include the future representation of an instrument `metadata/` view, the treatment of CTD/profile observations, and the final home of future GCMT products.
