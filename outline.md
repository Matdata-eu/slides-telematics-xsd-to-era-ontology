# Outline: From XSD to ERA ontology

RNE Ontology Task Force, 27 October 2026, agenda item 3 "Ontology 4.0 – Telematics Extension".
About 15 minutes, followed by RNE's initial findings on v4.0.0.
Presented on behalf of Bane NOR; the work itself was done for ERA.

**One message:** a converter can only translate what the XSD says. It cannot add meaning the XSD never had.
**Running example:** the TAF TSI `LocationIdent`, which links to agenda items 4 (URIs) and 5 (Primary Location attachment).

| # | Slide | Point | Visual |
|---|---|---|---|
| 1 | Cover | | |
| 2 | What v4.0 adds | Telematics is ~4× the ERA core, NeTEx ~12×: too big to model by hand | `ScaleBlocks` |
| 3 | The choice at the start | Convert automatically (garbage in, garbage out) or model by hand and map (RDF and XML are maintained separately and can drift apart, though every message should still fit the model). ERA's industry consultation favoured converting | `DecisionFork` |
| 4 | The pipeline in one picture | xsd2rdf writes SHACL, derives OWL with 7 SPARQL steps; then ERA-specific post-processing | `PipelineFlow` |
| 5 | Section: xsd2rdf | | |
| 6 | A form becomes a dictionary | `LocationIdent` → class, properties, cardinality, definition, label | `XsdToRdf` |
| 7 | The assumptions | 12 mapping rules, 5 safe and 7 risky, each risky one with another plausible reading; enumerations always become a new SKOS list, even when an ERA list exists | table |
| 8 | Section: garbage in, garbage out | | |
| 9 | One concept, three names | `LocationPrimaryName`, `PrimaryLocationName`, `PrimaryLocationNameASCII` → 3 properties | `SynonymNotes` |
| 10 | Names for roles, not for things | 27 properties point to `era:PrimaryLocation`, 5 of them tracks; 8 subclasses, only `Station` is a real kind | `RoleHub` |
| 11 | Same name, different meaning | `author` (DP/OP), NeTEx `AccessModes` (definition changes between runs, issue #27) | cards |
| 12 | NeTEx | An ontology written in a schema language: 74 % of 3,451 classes are "…Structure", 14-level chain, `Quay` has no superclass. Kept separate | `NetexLadder` |
| 13 | Section: post-processing | | |
| 14 | Six steps after the converter | Convert per source, rename to v3.x terms, tidy code lists, add ERA cross-links, remove DP/OP suffixes, merge + parameter groups + checks | `PostProcessSteps` |
| 15 | How much is connected to v3.x? | 12 shared terms; alignments still "to discuss"; checks in the ERA repo are informational only | `OverlapVenn` |
| 16 | Section: since v4.0.0 | | |
| 17 | The converter, fixed at the source | xsd2rdf 2.1: nesting-as-subclass 105 → 0, wrong functional 13 → 0, simple-type super-properties → 0, DP/OP kind clashes 47 → 0; source provenance (3,682 designators on TAF 4.0.0); asn2rdf, jsonschema2rdf, openapi2rdf on PyPI | table |
| 18 | Post-processing: from scripts to small rules | Layers 10-raw … 60-docs; one rule per decision, with fixture and expected count; renames traced, docs generated | layer strip + cards |
| 19 | Meaning added by people | Four review sessions: LocationIdent as a reference (`era:refersToLocation`), ERA core reuse only where it fits (`era:coreInterface`), OWL-Time / units / WKT / `era:Message`, code lists; 30 policies awaiting acceptance | cards |
| 20 | Where it stands | Checks 157 → 3 errors, messages 22 → 98 of 146, NeTEx in `949/netex/` (35,423 IRIs); still open: case clashes, `subsidiaryLocationCode`, B.12/B.14/OSDM messages, acceptance, no release yet; merge target ERA core v3.3.4 | tiles |
| 21 | Three take-aways | Translator not analyst · meaning added by people · better XSDs give a better ontology | |
| 22 | Questions | | |

## Numbers (tag `v4.0.0`, named subjects, rdflib)

The "since v4.0.0" numbers come from the telematics-ontology pipeline on `main` (24 October 2026) and the RNE findings re-check on xsd2rdf 2.0.1 / TAF 4.0.0.

| | Classes | Datatype props | Object props |
|---|---|---|---|
| ERA core `_core.ttl` | 128 | 465 | 342 |
| Telematics bundle | 941 | 1,520 | 1,165 |
| NeTEx bundle | 3,451 | 4,027 | 3,722 |

- Shared `era:` terms between core and telematics: 12 (`PrimaryLocation`, `SubsidiaryLocation`, `Vehicle`, `primaryLocationName`, `freightFlag`, `passengerFlag`, `containerHandlingFlag`, `handoverPointFlag`, `direction`, `letterMarking`, `faxNumber`, `scope`).
- Properties with range `era:PrimaryLocation`: 27. Subclasses of `era:PrimaryLocation`: 8.
- NeTEx: 2,552 of 3,451 classes end in "Structure" (783 RefStructure, 749 RelStructure, 556 VersionStructure).
