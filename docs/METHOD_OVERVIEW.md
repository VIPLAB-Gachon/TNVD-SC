# TNVD-SC Method Overview

## Motivation

Scan Context represents a LiDAR scan using a polar ring-sector descriptor.

In forested and off-road environments, conventional height-based Scan Context
representations can become ambiguous because:

1. Similar tree-height patterns repeatedly occur at different locations.
2. Uneven terrain changes raw point height even when the surrounding structure remains similar.
3. A single maximum-height value cannot fully represent the vertical structure inside each ring-sector cell.

TNVD-SC addresses these limitations using terrain-normalized vertical distributions.

---

## Pipeline

```text
Raw LiDAR Scan
      │
      ▼
Local Terrain Estimation
      │
      ▼
Terrain-Normalized Heights
      │
      ▼
Ring-Sector Partition
      │
      ▼
Vertical Distribution Encoding
      │
      ▼
Yaw-Invariant Retrieval Key
      │
      ▼
Top-K Candidate Retrieval
      │
      ▼
Full Circular JS Matching
      │
      ▼
Place Recognition
```

---

## Frozen Manuscript Configuration

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
| Candidate count | 25 |
| Retrieval key | Support-only |
| Distribution matching | Jensen-Shannon divergence |
| Yaw search | Full circular search |
| Support weighting | Unweighted |

---

## Terrain Normalization

A local terrain surface is estimated for each LiDAR scan.

The frozen implementation uses:

```text
Grid size                : 2.0 m
Terrain statistic        : 10th percentile
Minimum points per cell  : 3
Local filter/fill        : 3 x 3 neighborhood
```

Each point height is expressed relative to the estimated local terrain.

This reduces descriptor variation caused by slope and uneven ground.

---

## Vertical Distribution Representation

Conventional Scan Context:

```text
Ring-sector cell
      │
      ▼
Maximum height
      │
      ▼
Single scalar
```

TNVD-SC:

```text
Ring-sector cell
      │
      ▼
Terrain-normalized points
      │
      ▼
Vertical histogram
      │
      ▼
Distribution descriptor
```

The additional vertical information provides greater discrimination in repetitive vegetation environments.

---

## Retrieval and Matching

TNVD-SC uses a yaw-invariant support key for the initial database search.

```text
Candidate retrieval:
Top-K = 25
```

The selected candidates are then compared using full circular yaw search.

For each circular sector shift, descriptor distributions are compared using
Jensen-Shannon divergence.

The candidate with the lowest matching distance is selected as the final
place-recognition result.

---

## Source Availability

The full implementation is maintained internally while the associated
manuscript is under preparation/review.

Source code will be released after publication or approval for public release.
