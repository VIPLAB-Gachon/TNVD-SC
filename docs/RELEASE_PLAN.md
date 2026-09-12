# TNVD-SC Release Plan

## Current Status

TNVD-SC is currently under manuscript preparation/review.

The complete source implementation is maintained in the internal development repository and is intentionally not distributed through this repository before publication.

---

## Current Repository Scope

Before publication, this repository contains documentation only.

```text
TNVD-SC/
├── README.md
├── .gitignore
└── docs/
    ├── DATASETS.md
    ├── METHOD_OVERVIEW.md
    ├── RESULTS.md
    └── RELEASE_PLAN.md
```

---

## Planned Full Release

After publication/acceptance, the internal frozen implementation will be migrated to this repository.

The planned release structure is:

```text
TNVD-SC/
├── README.md
├── LICENSE
├── CITATION.cff
├── CMakeLists.txt
├── package.xml
├── configs/
├── include/
├── src/
├── scripts/
├── tools/
├── protocols/
├── docs/
└── third_party/
```

The release will include:

- TNVD-SC descriptor implementation;
- terrain estimation and normalization;
- support-key retrieval;
- full circular Jensen-Shannon matching;
- compact uint16 descriptor representation;
- hierarchical JS matcher;
- public dataset protocols;
- reproduction scripts;
- benchmark tools;
- dependency/bootstrap instructions;
- third-party attribution;
- citation metadata.

---

## Frozen Implementation

The manuscript implementation is frozen as:

```text
tnvd:b24:h18:js:terrain:key_support:unweighted
```

The internal release is preserved separately so that the future public source can be matched to the implementation used for the manuscript experiments.

---

## Planned Release Tag

```text
tnvd-sc-paper-v1.0
```

The tag will be created only when the source tree, manuscript metadata, licensing information, and public release package are finalized.
