# TNVD-SC Results

This document summarizes representative results from the frozen manuscript implementation.

The complete evaluation scripts and protocol files are maintained internally until the source-code release.

---

## Wild-Places

Cross-session forest place-recognition evaluation:

| Dataset Family | Mean R@1 | Directed Pairs |
| --- | ---: | ---: |
| Venman | **92.25%** | 12 |
| Karawatha | **91.37%** | 12 |
| Overall | **91.81%** | 24 |

Frozen configuration:

```text
tnvd:b24:h18:js:terrain:key_support:unweighted
```

---

## Additional Public-Dataset Evaluation

The manuscript additionally evaluates TNVD-SC on public LiDAR place-recognition datasets including:

- NCLT
- Oxford

Detailed protocol files and evaluation scripts will be included with the full research release.

---

## Efficient Implementation

The final implementation includes a compact vertical-distribution representation and an exact hierarchical Jensen-Shannon matching strategy.

### Descriptor Payload

```text
Reference representation : 233.44 KiB / place
Compact representation   :  60.47 KiB / place

Reduction:
3.86x
74.1%
```

### Resident Database Memory

```text
Reference implementation : ~504.85 MiB
Compact implementation   : ~188.50 MiB

Reduction:
2.68x
62.7%
```

### Compute Time

```text
Reference implementation : 228.553 ms
Optimized implementation : 174.265 ms

Reduction:
23.75%
```

The compact representation and hierarchical matcher were validated to preserve the retrieval and yaw results of the reference implementation in the internal release validation suite.

---

## Private Mobile-LiDAR Evaluation

Additional end-to-end localization experiments were performed using internally collected mobile-LiDAR data.

The underlying dataset and private evaluation artifacts are not distributed through this repository.

These experiments are used for internal project validation rather than as the primary public benchmark for TNVD-SC.
