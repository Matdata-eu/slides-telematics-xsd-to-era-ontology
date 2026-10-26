# From XSD to ERA ontology

Slidev deck for the RNE Ontology Task Force of 27 October 2026, agenda item 3 "Ontology 4.0 – Telematics Extension".
It explains how the TAF / TAP TSI XSDs became the telematics part of ERA ontology v4.0: the choice to convert
automatically, the [xsd2rdf](https://pypi.org/project/xsd2rdf/) converter and the assumptions it makes, where those
assumptions break on real TSI schemas (with examples from v4.0.0), why NeTEx is kept separate, and the post-processing
that repairs the result and connects it to ERA ontology v3.x. A last slide shows what has changed in the pipeline since v4.0.0. The work was done for ERA; it is presented on behalf of Bane NOR.

**Live slides:** https://matdata-eu.github.io/slides-telematics-xsd-to-era-ontology/

## Getting started

```bash
npm ci
npm run dev
```

Open http://localhost:3030 to see the slides, and http://localhost:3030/presenter for presenter mode with speaker notes.

| Script | What it does |
|---|---|
| `npm run dev` | Live-reloading dev server |
| `npm run build` | Static site in `dist/` |
| `npm run export` | PDF export (`slides-export.pdf`, needs Playwright Chromium) |

## Publishing

- **GitHub Pages** (`.github/workflows/deploy.yml`): every push to `main` builds the deck with base path `/slides-telematics-xsd-to-era-ontology/` and deploys it. Change the base path if the repository gets another name.
- **PDF release** (`.github/workflows/release.yml`): pushing a tag such as `v1.0.0` exports the slides (with click steps) to PDF and attaches it to a GitHub release.

## Interactive components

Every component takes a `step` prop driven by `$clicks`.

| Component | Slide | What it shows |
|---|---|---|
| `ScaleBlocks` | What v4.0 adds | ERA core, telematics and NeTEx to scale (classes + properties) |
| `DecisionFork` | The choice at the start | Convert automatically vs model by hand, and the industry's choice |
| `PipelineFlow` | The pipeline in one picture | XSD → SHACL → OWL/SKOS → post-processing → bundles, generic vs ERA-specific |
| `XsdToRdf` | A form becomes a dictionary | The TAF `LocationIdent` type next to its SHACL and OWL, line by line |
| `SynonymNotes` | One concept, three names | Three XSD names for one concept become three properties |
| `RoleHub` | Names for roles, not for things | 27 properties pointing to `era:PrimaryLocation`, tracks among them, and 8 subclasses |
| `NetexLadder` | NeTEx | How much of NeTEx is schema structure, and its 14-level "is a" chain |
| `PostProcessSteps` | Six steps after the converter | The post-processing steps with a real before → after per step |
| `OverlapVenn` | How much of it is connected to v3.x? | The 12 terms shared with the ERA core, and what is still to discuss |

## Sources of the numbers

All counts come from tag `v4.0.0` of the [ERA ontology](https://gitlab.com/era-europa-eu/public/interoperable-data-programme/era-ontology/era-ontology)
(`era-ontology/_core.ttl`, `domains/telematics/artefacts/dist/{telematics,netex}/ontology.ttl`), counted with rdflib
on named `owl:Class`, `owl:DatatypeProperty` and `owl:ObjectProperty` subjects. The XSD examples come from
`taf_cat_complete.xsd` (TAF TSI) and the converter behaviour from xsd2rdf `main`. The post-processing steps are those
of the ERA telematics-ontology pipeline.

## License

Code under the MIT License, slide content under CC BY 4.0. The Bane NOR logos are trademarks of Bane NOR SF and are not covered. See [LICENSE](LICENSE).
