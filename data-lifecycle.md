MERMAID Data Lifecycle

This document describes the canonical data families in the MERMAID ecosystem and their relationships.

The purpose of this diagram is architectural orientation. It is not intended to document every file, script, repository, or implementation detail.

Data Lifecycle

flowchart LR
    raw[raw]
    waveforms[waveforms]
    metadata[metadata]
    candidate[associations/candidate]
    identified[associations/reviewed/identified]
    unidentified[associations/reviewed/unidentified]
    catalogs[catalogs]
    records[records]
    timelines[timelines]
    raw --> waveforms
    raw --> metadata
    raw --> records
    records --> timelines
    waveforms --> candidate
    candidate --> identified
    candidate --> unidentified
    identified --> catalogs
    waveforms -. verification .-> catalogs

Canonical Data Families

raw

Mirrored source data retrieved from remote MERMAID data sources.

Examples:

* CLS mirrors
* RUDICS transfers
* recovered instrument data

waveforms

Event waveform products.

Examples:

* SAC
* miniSEED
* DET observations
* REQ observations

metadata

MERMAID instrument metadata.

Examples:

* GPS positions
* GeoCSV products
* dive information
* engineering telemetry
* battery information
* instrument configuration

Event metadata from ISC, NEIC, GCMT, PDE, etc. is not considered part of this data family.

associations

Waveform-to-source associations.

candidate

Automatically generated candidate source matches.

Candidate associations are durable products and are retained indefinitely.

reviewed

Manually reviewed associations.

identified

An authoritative waveform-to-source association exists.

The association itself is authoritative, although source metadata may still be preliminary.

unidentified

No acceptable source association exists.

These negative review results are preserved as durable products.

catalogs

Final waveform-associated catalog products.

Catalogs are derived from reviewed associations whose event metadata has been updated against reviewed catalogs such as ISC.

Catalogs may contain:

* finalized event metadata
* travel times
* residuals
* phase arrivals
* derived catalog quantities

Catalogs do not include complete external catalogs such as ISC, NEIC, or GCMT.

records

Normalized raw-data records.

Records are derived directly from raw MERMAID data and are independent of the event-association workflow.

timelines

Timeline products derived from records.

Examples:

* interval timelines
* activity timelines
* summary timelines

buffers

Continuous buffer observations.

Buffers are considered a distinct data family rather than a subtype of waveforms.

A likely future structure is:

buffers/
├── raw/
└── waveforms/

Open Questions

GCMT Products

Future mermaid-gcmt outputs have not yet been placed within the architecture.

Possible homes include:

* associations/
* catalogs/
* a future dedicated data family

CTD Profile Data

Some MERMAIDs collect CTD (conductivity-temperature-depth) profile data.

These observations are likely to become a future top-level data family rather than being treated as metadata.

Possible future structure:

profiles/

representing oceanographic observations in the same way that:

waveforms/

represents seismic observations.
