# Datasets

TNVD-SC is evaluated on multiple LiDAR place-recognition datasets.

Raw datasets are not redistributed through this repository.

---

## Wild-Places

Wild-Places is used as the primary forest place-recognition benchmark.

The public TNVD-SC release will include:

- dataset preparation instructions;
- cross-session protocol metadata;
- database/query manifest generation;
- evaluation scripts.

Users will download the original dataset separately.

---

## NCLT

NCLT is used to evaluate generalization beyond the forest-only setting.

The source release will include the manifest-generation and evaluation tools used by the TNVD-SC experiments.

The original NCLT dataset must be obtained from its official source.

---

## Oxford

Oxford LiDAR data are used as an additional generalization evaluation.

Raw Oxford data are not distributed by this project.

---

## Internal Mobile-LiDAR Dataset

Additional experiments are conducted using an internally collected mobile-LiDAR dataset.

This dataset contains private/project-specific sensor data and is not included in the public TNVD-SC repository.

The public repository will therefore contain only reusable evaluation logic where appropriate, without:

- raw point clouds;
- GNSS/INS logs;
- calibration files;
- private manifests;
- internal experiment outputs.
