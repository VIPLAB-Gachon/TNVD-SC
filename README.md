# TNVD-SC

**Terrain-Normalized Vertical Distribution Scan Context for LiDAR Place Recognition in Forested Environments**

TNVD-SC is a LiDAR place recognition method designed for forested and off-road environments, where conventional Scan Context representations can become ambiguous due to repetitive vegetation structures and uneven terrain.

> **The source implementation is currently maintained internally and will be released after publication/acceptance.**

---

## Overview

Scan Context represents a LiDAR scan using a polar ring-sector descriptor in which each cell is summarized by a height statistic.

While this representation is effective in many structured environments, forest scenes present a particularly challenging case:

- repetitive tree structures can produce similar height patterns at different locations;
- uneven terrain introduces variation unrelated to place identity;
- a single height statistic cannot fully represent the vertical structure inside each cell.

TNVD-SC addresses these limitations by combining **terrain normalization** with a **vertical distribution representation** while preserving the efficient ring-sector structure and circular yaw matching of Scan Context.

The overall pipeline is:

```text
Raw LiDAR Scan
      │
      ▼
Local Terrain Estimation
      │
      ▼
Terrain-Normalized Point Heights
      │
      ▼
Ring-Sector Partition
      │
      ▼
Vertical Distribution Encoding
      │
      ├──────────────► Yaw-Invariant Retrieval Key
      │                        │
      │                        ▼
      │                 Top-K Retrieval
      │                        │
      └────────────────────────┘
               │
               ▼
     Full Circular JS Matching
               │
               ▼
        Place Recognition
```

---

## Method

### 1. Terrain Normalization

For each LiDAR scan, a local terrain surface is estimated using a grid-based representation.

The current manuscript configuration uses:

```text
Terrain grid size      : 2.0 m
Terrain statistic      : 10th percentile
Minimum points / cell  : 3
Local fill/filter      : 3 × 3 neighborhood
```

Each LiDAR point is then converted from its raw vertical coordinate to a terrain-relative height.

This reduces descriptor variation caused by local slope and uneven ground.

---

### 2. Ring-Sector Representation

The normalized point cloud is divided using the standard polar Scan Context structure.

```text
Number of rings      : 20
Number of sectors    : 60
Maximum radius       : 80 m
```

Each point is assigned to one ring-sector cell according to its horizontal position relative to the LiDAR sensor.

---

### 3. Terrain-Normalized Vertical Distribution

Instead of representing each ring-sector cell using only a single maximum-height value, TNVD-SC constructs a vertical distribution.

The frozen manuscript configuration uses:

```text
Vertical bins             : 24
Maximum normalized height : 18 m
```

The resulting descriptor therefore preserves richer vertical structure within each spatial cell.

Conceptually:

```text
Conventional Scan Context

ring-sector cell
      │
      ▼
single max-height value


TNVD-SC

ring-sector cell
      │
      ▼
terrain-normalized points
      │
      ▼
vertical histogram
      │
      ▼
distribution descriptor
```

---

### 4. Yaw-Invariant Retrieval

A compact support-based key is generated from the TNVD descriptor for candidate retrieval.

The retrieval key is invariant to circular sector shifts, allowing efficient database search without explicitly evaluating every yaw configuration during the first retrieval stage.

The current configuration retrieves:

```text
Top-K candidates : 25
```

---

### 5. Circular Distribution Matching

The retrieved candidates are evaluated using full circular yaw search.

For every possible sector shift, the vertical distributions of the query and database descriptors are compared using Jensen-Shannon divergence.

```text
Similarity / distance : Jensen-Shannon divergence
Yaw search             : Full 60-sector circular search
```

The candidate with the minimum descriptor distance is selected as the final place-recognition result.

---

## Frozen Manuscript Configuration

The current TNVD-SC implementation is frozen as:

```text
tnvd:b24:h18:js:terrain:key_support:unweighted
```

| Parameter | Value |
| --- | ---: |
| Rings | 20 |
| Sectors | 60 |
| Maximum radius | 80 m |
| Terrain grid | 2.0 m |
| Vertical bins | 24 |
| Maximum normalized height | 18 m |
| Retrieval key | Support-only |
| Candidate count | 25 |
| Distribution matching | Jensen-Shannon divergence |
| Yaw search | Full circular search |
| Support weighting | Unweighted |

---

## Experimental Results

### Wild-Places

TNVD-SC is evaluated on the forest sequences of the Wild-Places dataset using the cross-session protocol adopted in the current manuscript.

| Dataset Family | Mean R@1 | Directed Pairs |
| --- | ---: | ---: |
| Venman | **92.25%** | 12 |
| Karawatha | **91.37%** | 12 |
| Overall | **91.81%** | 24 |

The results demonstrate that representing terrain-normalized vertical structure substantially improves place discrimination in repetitive forest environments.

Additional evaluations are conducted on other public LiDAR datasets, including NCLT and Oxford.

---

## Efficient Implementation

The final TNVD-SC implementation also includes a compact descriptor representation and an exact hierarchical Jensen-Shannon matching strategy.

### Descriptor Storage

The reference floating-point representation requires:

```text
233.44 KiB / place
```

The compact `uint16` representation requires:

```text
60.47 KiB / place
```

corresponding to:

```text
3.86× reduction
74.1% lower descriptor payload
```

### Database Memory

Measured resident database memory is reduced from approximately:

```text
504.85 MiB
```

to:

```text
188.50 MiB
```

corresponding to approximately:

```text
2.68× reduction
62.7% lower resident memory
```

### Matching Efficiency

The optimized implementation reduces measured compute time from:

```text
228.553 ms
```

to:

```text
174.265 ms
```

while preserving the retrieval and matching results of the reference implementation.

The compact representation and hierarchical matching strategy were validated for exact Top-1 retrieval and yaw equivalence in the release validation experiments.

---

## Repository Status

This repository currently serves as the project repository for TNVD-SC.

The complete source implementation is maintained internally while the associated manuscript is being prepared and evaluated.

```text
Current repository
├── README.md
└── project documentation

Internal repository
├── TNVD-SC source implementation
├── evaluation code
├── dataset adapters
├── benchmark tools
├── public-dataset protocols
└── reproducibility scripts
```

The full implementation will be migrated to this repository after publication/acceptance.

---

## Planned Public Release

The future public release is planned to include:

```text
TNVD-SC/
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

The release package will include:

- TNVD-SC descriptor generation;
- terrain estimation and normalization;
- support-key candidate retrieval;
- full circular Jensen-Shannon matching;
- compact `uint16` descriptor implementation;
- hierarchical JS matching;
- Wild-Places reproduction protocol;
- evaluation and benchmark scripts;
- build and dependency instructions;
- third-party attribution;
- citation metadata.

---

## Code Availability

The source implementation is intentionally not distributed through this repository before publication.

Internal project members can access the frozen implementation through the internal project package.

The complete research release will be made available after the associated manuscript is accepted or otherwise approved for public release.

---

## Dependencies

The internal TNVD-SC implementation currently uses:

```text
ROS 2
PCL
Eigen
Scan Context
nanoflann
```

Scan Context is maintained as an external dependency.

The current internal implementation pins Scan Context to:

```text
Repository:
https://github.com/gisbi-kim/scancontext

Commit:
93672835bb85e9c03fedf0fbaf2d0491df12161e
```

Detailed installation instructions and third-party licensing information will be included with the full source release.

---

## Authors

**Jineon Kim**  
**Jhonghyun An**

VIP Lab  
Gachon University

---

## Citation

Citation information will be added after publication.

---

## License

The source implementation is not currently distributed through this repository.

Source-code licensing information will be provided together with the full implementation release after publication.