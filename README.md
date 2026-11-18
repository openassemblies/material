# Open Material standard

An open standard for the part record: one part type or part instance: how it is identified, its classes in published systems, its property values with their basis, its parametric base form, and the files it is made from. This repository is the standard's definition: a SAMM aspect model, and the JSON Schema (draft-07) and Asset Administration Shell submodel template generated from it.

- **Served:** each version of the schema at its `$id` under `https://openmaterial.org/`, its AAS template and aspect model beside it, and [`index.json`](https://openmaterial.org/index.json) listing them.
- **Licence:** Apache License 2.0 (`LICENSE`, `NOTICE`).
- **Governance:** `GOVERNANCE.md`. Contributions: `CONTRIBUTING.md`.
- **Status:** draft. No version 1.0.0 schema has been released yet.

## What is here

| Path | What it is |
|---|---|
| `samm/org.openmaterial.part-record/{version}/PartRecord.ttl` | The part record as an Eclipse Semantic Aspect Meta Model (SAMM 2.1.0) aspect model, aspect `urn:samm:org.openmaterial.part-record:{version}#PartRecord`. It is the source: the two files below are generated from it. |
| `part-record/{version}/part-record.schema.json` | The part record's JSON Schema (draft-07), served at its `$id`, `https://openmaterial.org/part-record/{version}/part-record.schema.json`. |
| `part-record/{version}/part-record.aas.json` | The part record's Asset Administration Shell submodel template (IDTA 01001 metamodel, JSON). |
| `references.json` | The shared definitions the record refers to outside its own namespace, each at an immutable versioned URL with its SHA-256. |
| `examples/` | Example records, listed in `examples/manifest.json` against the schema each validates with. |
| `scripts/` | The gate (`npm run check`), the served-site build (`npm run site`), the served check (`npm run served`) and the generator that writes the schema and AAS template from the aspect model. |

## How the record is built

A part record states one part type (`assetKind` Type) or one part instance. `identity` is its IEC 61406 identification link and the AAS globalAssetId it resolves to. `classification` names its classes in published systems: ETIM for the product, IFC and CCI for the element. `typeProperties` holds the values of its classes' properties, each keyed by its published property URI, and `manufacturerType` the IFC Pset_ManufacturerTypeInformation values. `baseForm` is the part's parametric base form: a form kind whose dimensions are read, by property URI, from the part's own values or, for a dimension that varies by occurrence, from an assembly record's occurrence. `composition` says how the part is supplied and counted in an assembly, `jobOrder` the ISA-95 job order material requirement it answers, `fabricationFiles` its geometry and machining files, and `provenance` who authored the record, when, from which sources, and whether it is illustrative. An assembly record names a part by its `materialDefinitionId`.

Every unit is a UN/CEFACT Recommendation 20 code. Classes are named in published systems (ETIM, IFC, CCI, read through the buildingSMART Data Dictionary) by code, version and URI. Every value carries its basis: measured, derived (with its method), a default (with its source) or substituted. A file the record depends on (an ISO 10303-242 or ISO 10303-238 model, a DSTV file) is referenced by URI and SHA-256, never embedded.

## Shared definitions

A unit of measure, a quantity, a classification reference, a file reference and the basis of a value are not defined here: the record refers to shared, openly published definitions at an immutable versioned URL each, listed in `references.json`:

- `https://schemas.ectropy.ai/v/2.0.0/_definitions/units.schema.json`
- `https://schemas.ectropy.ai/v/0.1.0/_definitions/classification-ref.schema.json`
- `https://schemas.ectropy.ai/v/0.1.0/_definitions/file-reference.schema.json`
- `https://schemas.ectropy.ai/v/0.8.0/flow/flow-common.schema.json`

A versioned URL never changes, so neither does what a version of this record accepts. The check fetches each one, refuses it unless its bytes match the pinned SHA-256, and validates the examples against it.

## What is not here

A modelling tool, a fabrication planner or a product catalogue. Any one party's way of designing, pricing, scheduling or deciding. The standard names the published standards it relates to (ISA-95, IEC 61406, the IDTA submodel templates, IFC, ETIM, UN/CEFACT Recommendation 20) by their identifiers; it does not reproduce them.

## Using the standard

- **A version:** fetch `https://openmaterial.org/part-record/{version}/part-record.schema.json`. A published version's bytes never change; new content is a new version at a new path.
- **Vendored:** copy this repository at a commit or a release tag.

## Working on it

```sh
npm ci
npm run check
npm run site
```

The gate holds: canonical form, `$id` equals the served URL and names the record's version, closure under `https://openmaterial.org/` and the pinned shared definitions, every schema compiles, every example validates and holds together, public text clean, published versions immutable.

The schema and the AAS template are generated, never edited by hand. Change the aspect model, as a new version under `samm/org.openmaterial.part-record/`, and regenerate with the Eclipse ESMF SAMM CLI 2.16.0 (Java 25):

```sh
npm run generate -- java -jar samm-cli-2.16.0.jar
```

CI regenerates both from the aspect model and fails when they differ from what is committed.
