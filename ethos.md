# MERMAID Architecture Ethos

This repository records the long-term architecture of the MERMAID software and data ecosystem. It describes the conceptual model before implementation; it does not prescribe a data migration or replace existing operational workflows.

## Core principles

### Repository names describe tools; directory names describe durable data families

Repositories are implementations and may be replaced, merged, split, archived, or rewritten. Examples include `automaid`, `mermaid-records`, `mermaid-timelines`, `mermaid-catalogs`, `mermaid-buffer`, `cfneic`, and `mermaid-gcmt`.

Directory names should normally describe scientific or operational data families that persist independently of a particular producer. This is a guiding principle, not an absolute naming rule. Long-established workflow trees such as `server/`, `processed/`, and `events/` retain architectural value: they encode operational semantics and support a decade or more of code and practice.

### Preserve established paths when they carry compatibility and workflow meaning

Do not rename or migrate an established tree merely to obtain a cleaner clean-slate name. `server/`, `processed/`, and `events/` remain canonical workflow trees. A `legacy/` area may be useful when practical, but its layout and any migration are not yet settled.

### Canonical trees own bytes; curated views improve access

Canonical or workflow trees own the real bytes. Curated data-family views may expose those canonical files through symlinks when the need is lookup or organization, not a change in data ownership. Such views are reversible and avoid unnecessary physical migration or duplicated bytes.

For example, `waveforms/` is a flat view of waveform files owned by `processed/`, and `associations/` is a flat view of all reviewed `.evt` outcomes owned by `events/`: identified positive associations and unidentified authoritative null results. Neither view replaces its full canonical workflow tree.

### Reconcile aggregates when ownership requires it

When several acquisition sources can contain overlapping logical files, retain a real canonical aggregate that can compare and reconcile those sources. A symlink view is appropriate only when the underlying bytes are already canonical and the problem is solely layout or lookup. Thus `server/` is the byte-owning reconciled aggregate of raw MERMAID source data, not merely a view of source-specific mirrors.

### Prefer scientific concepts without erasing useful history

Names such as `waveforms/`, `associations/`, and `catalogs/` clarify durable scientific concepts. Introduce them where they solve concrete problems, while preserving the established trees that contain wider workflow context.

### Architecture before implementation

When making structural changes:

1. Define the conceptual model.
2. Define ownership and data-family boundaries.
3. Define relationships and compatibility expectations.
4. Implement software or migrations only after those decisions are made.

The architecture should guide implementation, while documenting unresolved questions honestly rather than turning them into premature commitments.

## Architecture repository

This repository is the central high-level map of the MERMAID ecosystem. It contains workflow and data-flow diagrams, architecture decisions, and notes on naming, provenance, and system evolution. It should help a new contributor understand the system without requiring inspection of every implementation repository.
