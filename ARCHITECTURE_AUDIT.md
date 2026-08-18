# MERMAID Architecture Consistency Audit

Audit date: 2026-08-18  
Scope: complete repository read-only audit, including the ignored `repo_docs/`
snapshot corpus, literal `$MERMAID` paths, Mermaid diagrams, reachable Git
history, reflog, and dangling intermediate document blobs.

## How to use this audit

Each actionable finding has a checkbox. Check it only after the associated
architecture decision or documentation correction has been verified. Historical
references and clearly marked TBDs are not defects by themselves.

Priority meanings:

- **CRITICAL** — contradicts the supplied intended architectural contract and
  changes byte ownership or the durable hierarchy.
- **HIGH** — presents a stale/current path or leaves a major durable family
  materially ambiguous.
- **MEDIUM** — risks migration, reconciliation, or reader misunderstanding.
- **LOW** — snapshot/documentation hygiene issue.
- **INFORMATIONAL** — verified observation; no correction implied.

## Executive assessment

The current top-level documents are mostly self-consistent with one another,
but they implement a different model from the supplied intended reference.
Commit `c638252` (2026-08-14, *docs: clarify canonical MERMAID trees and
curated views*) explicitly changed the earlier durable-data-family model into
byte-owning `server/`, `processed/`, and `events/` workflow trees, with
`waveforms/` and `associations/` as symlink views.

That newer repository decision must either be reaffirmed as an intentional
replacement for the supplied reference model, or reversed/documented as a
stale divergence. Do not make piecemeal terminology edits before resolving
that primary decision.

## Reconstructed current repository contract

This section reports the latest documented contract; it does not choose between
that contract and the supplied reference.

```text
servers/<source-or-user>/...    source-specific acquisition mirrors
              |
              v
server/                         byte-owning reconciled aggregate
              |- processed/     byte-owning heterogeneous automaid workflow
              |     |- events/  byte-owning candidate/review workflow
              |     `- waveforms/{sac,mseed}/  symlink lookup view
              |
              `- records/ -> timelines/

events/reviewed/identified/     non-empty authoritative .evt bytes
events/reviewed/unidentified/   empty authoritative .evt null assertions
       `-> associations/{identified,unidentified}/  symlink views
                 `-> catalogs/ final catalog products

buffers/ -> mermaid-buffer processing/QC
```

Documented relationships:

- `mermaid-records` consumes raw source data directly, not metadata,
  processed outputs, waveforms, associations, or catalogs
  (`data-lifecycle.md:23`).
- `mermaid-timelines` consumes normalized records and produces timelines
  (`repo_docs/mermaid-timeline/README.md:3-8`).
- `mermaid-catalogs` produces final catalog products and may use reviewed
  identified associations, GeoCSV, waveform verification, `tomocat1`, and
  `cfneic`; metadata correction must not change the reviewed association
  (`data-lifecycle.md:44`).
- `mermaid-buffer` converts raw circular-buffer samples to miniSEED and is a
  separate branch (`repo_docs/mermaid-buffer/README.md:3-6`,
  `data-lifecycle.md:23`).

## Priority checklist

### Durable hierarchy and ownership

- [ ] **CRITICAL — Resolve the primary architecture conflict.** The supplied
  reference makes `raw/`, `waveforms/`, `metadata/`, `associations/`,
  `catalogs/`, `records/`, `timelines/`, and `buffers/` durable top-level data
  families. The current documents instead make `server/`, `processed/`, and
  `events/` byte-owning workflow trees and make `waveforms/` and
  `associations/` non-owning views. Sources: `README.md:9`,
  `data-lifecycle.md:17-19,40,42`, `ethos.md:15-21`. Historical contrast:
  initial commit `434672f`; replacement commit `c638252`.

- [ ] **CRITICAL — Resolve waveform byte ownership.** The supplied reference
  requires `waveforms/` to own SAC and MiniSEED waveform-family products, with
  legacy `processed/` corresponding roughly to a future waveform subset. The
  current contract says `processed/` owns waveform bytes and
  `waveforms/{sac,mseed}/` is a writable symlink view. Sources:
  `data-lifecycle.md:19,29,40,66`; `ethos.md:21`.

- [ ] **CRITICAL — Resolve the durable association hierarchy.** The supplied
  reference requires `associations/candidate/` and
  `associations/reviewed/{identified,unidentified}/`. The current contract
  places candidate and reviewed bytes in `events/`, while exposing symlinks at
  `associations/{identified,unidentified}/`. Sources:
  `data-lifecycle.md:21,30-35,42`; `ethos.md:21`.

- [ ] **HIGH — Define `raw/` versus `server/`.** The supplied reference names
  `raw/` as a durable family. Current prose makes singular `server/` the
  byte-owning raw aggregate, with plural `servers/` as source mirrors.
  Sources: `data-lifecycle.md:9,17,23`; `ethos.md:25`.

- [ ] **HIGH — Settle and document the top-level `metadata/` location.** The
  supplied reference names it as a durable family, while current documentation
  calls the clean top-level metadata view unresolved. Source:
  `data-lifecycle.md:48-52`.

- [ ] **MEDIUM — Define the role and ownership of `buffers/`.** It is clearly
  a separate trunk, and `mermaid-buffer` writes miniSEED, but its canonical
  durable output location relative to `buffers/` and `waveforms/` is not
  stated. Sources: `data-lifecycle.md:14,23`; 
  `repo_docs/mermaid-buffer/README.md:3-6,122-126`.

### Associations

- [ ] **CRITICAL — Preserve the candidate state in the durable association
  contract.** Candidate data are said to remain durable after review, but
  current docs assign them only to the broader `events/` workflow; they do not
  support the required `associations/candidate/` family, including candidate
  catalog-like lists and `all.txt`-type products. Sources:
  `data-lifecycle.md:21,42`.

- [ ] **HIGH — Make byte ownership versus views unambiguous for reviewed
  associations.** Current prose is internally clear that
  `events/reviewed/{identified,unidentified}` owns bytes and associations
  contains symlinks, but this is contrary to the supplied association model.
  The final contract must state one canonical byte-owning location and any
  view path separately. Sources: `data-lifecycle.md:30-33,42`.

- [ ] **MEDIUM — Specify a complete flat curated association view if one is
  offered.** Current split views collectively include both outcomes, but no
  combined flat-view contract exists. Any combined view must include both
  non-empty identified `.evt` files and empty reviewed-unidentified `.evt`
  null assertions. Sources: `data-lifecycle.md:32-35,42`; `ethos.md:21`.

- [x] **INFORMATIONAL — Reviewed unidentified results are correctly described
  as canonical curated null assertions, not absent/failed work.** Sources:
  `data-lifecycle.md:21,31,35,42`.

- [x] **INFORMATIONAL — Current docs correctly retain candidate associations
  after review.** Source: `data-lifecycle.md:21`.

### Catalogs

- [ ] **HIGH — Label or migrate catalog snapshot paths.** The
  `mermaid-catalogs` snapshot says cfneic/tomocat products “currently live” at
  `$MERMAID/cfneic/outputs` and waveforms at
  `$MERMAID/processed_everyone`, without a historical or migration label.
  This conflicts with durable data-family naming and with the documented move
  away from `_everyone`. Sources: `repo_docs/mermaid-catalogs/README.md:32-53`;
  `data-lifecycle.md:17,44`.

- [x] **INFORMATIONAL — Catalog correction is correctly kept separate from
  association truth.** The catalog may update event origin/location/depth/
  magnitude/identifier metadata without changing the reviewed association.
  Source: `data-lifecycle.md:44`.

- [x] **INFORMATIONAL — Final catalogs are correctly separated from complete
  external ISC/NEIC/GCMT reference catalogs.** Source: `data-lifecycle.md:44`.

- [x] **INFORMATIONAL — Catalog waveform use is scientifically consistent.**
  mSEED supports SNCL/timing checks and SAC supports station metadata checks.
  Source: `repo_docs/mermaid-catalogs/catalog.md:49-56`.

### Raw, records, and metadata

- [x] **INFORMATIONAL — `mermaid-records` is correctly documented as consuming
  raw data directly, not metadata.** Source: `data-lifecycle.md:23`.

- [x] **INFORMATIONAL — Normalized records remain distinct from metadata and
  timelines remain downstream records products.** Sources:
  `data-lifecycle.md:23`; `repo_docs/mermaid-timeline/README.md:3,51-54`.

### Server data

- [ ] **MEDIUM — Document plural `servers/` source copies as byte-owning
  acquisition mirrors, not merely possible views.** The operations snapshot
  says servercopy mirrors remote content to `$MERMAID/servers/<user>/`; the
  architecture only explicitly calls singular `server/` byte-owning. Sources:
  `repo_docs/mermaid-ops/README.md:13-16,29-33,90-94,210-213`;
  `data-lifecycle.md:17`.

- [ ] **MEDIUM — Define reconciliation provenance and basename-conflict
  handling.** Documentation recognizes overlaps and says `server/` can
  reconcile them, but never specifies how source identity, same-name files,
  or conflicts are retained/audited. Sources: `data-lifecycle.md:17`;
  `ethos.md:23-25`.

- [ ] **LOW — Classify `$MERMAID/servers/_runs/` as operational evidence versus
  durable raw data.** Source: `repo_docs/mermaid-ops/README.md:128-176`.

### Diagrams and snapshots

- [ ] **HIGH — Update both Mermaid diagrams after the primary architecture
  decision.** They currently encode the `servers/ -> server/ -> processed/ ->
  events/` model and `associations/{identified,unidentified}` paths, omitting
  `raw/`, `metadata/`, `associations/candidate/`, and
  `associations/reviewed/...` from the supplied model. Sources:
  `data-lifecycle.md:7-15,27-38`.

- [x] **INFORMATIONAL — Static syntax inspection found no malformed Mermaid
  syntax in the two fenced diagrams.** Actual rendering could not be performed:
  local Node fails before Mermaid starts because `libicui18n.74.dylib` is
  missing. This is an environment limitation, not a diagram defect.

- [ ] **LOW — Refresh, date, or visibly pin `repo_docs/` snapshots.** The
  directory is ignored by Git yet treated as evidence; the top-level README
  warns snapshots may lag, but individual snapshot content still says
  “currently.” Sources: `README.md:18-20`;
  `repo_docs/mermaid-catalogs/README.md:30-42`.

- [ ] **LOW — Repair or intentionally annotate flattened snapshot links.**
  Examples: `repo_docs/mermaid-catalogs/README.md:6` points to
  `docs/catalog.md`, and `repo_docs/mermaid-records/README.md:52,114-117`
  points to source-repository `docs/` paths that do not exist in this snapshot
  layout.

- [ ] **LOW — Resolve the `mermaid-timeline`/`mermaid-timelines` snapshot name
  mismatch.** Title/build use plural but package-install and plot examples use
  singular. Sources: `repo_docs/mermaid-timeline/README.md:1,48,73,83`;
  `repo_docs/mermaid-timeline/schema.md:1,11`.

## Literal `$MERMAID` path inventory

| Literal path | Classification | Source |
| --- | --- | --- |
| `$MERMAID/records` | Current and consistent | `repo_docs/mermaid-records/README.md:47`; `repo_docs/mermaid-records/cli.md:50-55` |
| `$MERMAID/timelines` | Current and consistent | `repo_docs/mermaid-timeline/README.md:51-54,77-78,109` |
| `$MERMAID/servers/<user>/` | Current operations path; architecturally ambiguous against singular `server/` | `repo_docs/mermaid-ops/README.md:29-33,90-94,210-211` |
| `$MERMAID/servers/_runs/` | Operational evidence; classification not specified | `repo_docs/mermaid-ops/README.md:128-139,160-172` |
| `$MERMAID/passwords/rudics.csv` | Operational configuration; outside durable data-family hierarchy | `repo_docs/mermaid-ops/README.md:18-22,64-66,197-203` |
| `$MERMAID/cfneic/outputs` | Stale/ambiguous current claim | `repo_docs/mermaid-catalogs/README.md:32-35,49-50,67-68` |
| `$MERMAID/processed_everyone` | Stale/ambiguous current claim | `repo_docs/mermaid-catalogs/README.md:38-42,53,70` |

No `~/mermaid/...` literal paths occur.

## Explicit unresolved decisions (not defects)

- Source-specific `servers/<user-or-source>/` naming and migration from an
  `_everyone` suffix: `data-lifecycle.md:17`.
- Clean top-level instrument `metadata/` view: `data-lifecycle.md:52`.
- Possible future `profiles/` family for CTD observations:
  `data-lifecycle.md:56-58`.
- Future `mermaid-gcmt` product home: `data-lifecycle.md:60-62`.
- Future opt-in automaid waveform-root mode: `data-lifecycle.md:64-66`.
- Catalog validation thresholds and diagnostics-sidecar format:
  `repo_docs/mermaid-catalogs/catalog.md:17,448,450-458`.

## Minimal ordered remediation plan

1. Check off the primary hierarchy decision: retain the workflow-tree/view
   model, or make the supplied durable-data-family model authoritative.
2. Create one normative hierarchy/ownership matrix covering every top-level
   family, canonical bytes, producers, consumers, legacy predecessor, and
   views.
3. Resolve association ownership and paths, explicitly including candidate,
   reviewed identified, and reviewed unidentified empty `.evt` assertions.
4. Resolve raw/server/source-mirror nomenclature, byte ownership,
   reconciliation provenance, and same-basename conflicts.
5. State legacy mappings for `processed/`, `events/`, `cfneic/outputs`, and
   `_everyone` paths.
6. Update prose and Mermaid diagrams together from the normative matrix.
7. Refresh/date snapshots, label historical paths, and repair or annotate
   flattened snapshot links.
