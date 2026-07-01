# Source Attribution Model

Date: 2026-07-01

## Direction

BC-NPPD should separate ecological values from evidence confidence and source
attribution. A strong score without strong evidence should remain visibly
uncertain.

## Minimum Future Record Concepts

* Reference ID
* Source name
* Source tier
* URL or citation
* Field or claim supported
* Species ID
* Evidence confidence
* Notes and limitations
* External source IDs where available
* Materialization manifest path or URI where a public-safe manifest exists
* Acquisition or resolution status for derived source artifacts

## Source Tiers

* Tier 1: peer-reviewed literature.
* Tier 2: government, academic, herbarium, and technical sources.
* Tier 3: regional practitioner sources such as Satinflower Nurseries.

## Excluded Source

The City of Vancouver Green Rainwater Infrastructure Planting Guidelines PDF is
excluded. The exclusion applies to citations, source-attribution fields, scoring
rationale, species notes, and documentation.

## External Source Manifests

P2 should make room for public-data materialization records without requiring
live downloads in normal validation. Future source records may reference:

* BC Data Catalogue package IDs, dataset page URLs, resource IDs, formats,
  licence titles, and resource classifications.
* WFS, direct-download, indirect custom-download, or DWDS acquisition state.
* FreshForge workflow IDs, run namespaces, run summaries, provider diagnostics,
  and artifact manifest paths.
* HectaresBC dataset IDs, catalog family, title candidates, source ZIP paths,
  content status, and retrieval diagnostics.

These records support provenance and review. They do not, by themselves, make an
ecological claim true or score-ready.

## Deferred To P2

P2 should define durable source/reference dataclasses, source completeness
checks, reference ID conventions, source-attribution validation behavior, and
optional source materialization manifest contracts.
