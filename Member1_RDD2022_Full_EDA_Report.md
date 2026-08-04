# RDD2022 Member 1 — Full-data EDA report

This report was generated from the complete official RDD2022 release with random seed `42`.

## Dataset summary

- Official images inventoried: **47,420**
- Official test images without public XML labels: **9,035**
- Prepared labeled images: **38,385**
- Valid exported target-class bounding boxes: **55,006**
- Non-target annotation boxes audited but not exported: **10,705**
- Images without a D00/D10/D20/D40 target box: **14,618**
- Images containing multiple target damage classes: **8,300**
- Corrupt images: **0**
- Exact duplicate rows: **4**
- Near-duplicate rows grouped for leakage control: **3,256**
- Out-of-bounds boxes: **0**
- Degenerate boxes excluded from export: **1**
- Extremely small valid boxes flagged for review: **339**

## Labeled images by source domain

| Source domain | Images |
|---|---:|
| China_Drone | 2,401 |
| China_MotorBike | 1,977 |
| Czech | 2,829 |
| India | 7,706 |
| Japan | 10,506 |
| Norway | 8,161 |
| United_States | 4,805 |
| **Total** | **38,385** |

## Exported target boxes

| Class | Meaning | Valid boxes |
|---|---|---:|
| D00 | Longitudinal crack | 26,016 |
| D10 | Transverse crack | 11,830 |
| D20 | Alligator crack | 10,616 |
| D40 | Pothole | 6,544 |
| **Total** |  | **55,006** |

One D20 annotation was degenerate and was excluded from export, so its exported count is 10,616 rather than the raw XML count of 10,617.

## Additional official XML codes

All labeled images were retained. Boxes outside the four-class project taxonomy were audited, not silently relabeled.

| Code | Raw boxes | Export decision |
|---|---:|---|
| D44 | 5,057 | Audited; not exported |
| D50 | 3,581 | Audited; not exported |
| REPAIR | 1,046 | Audited; not exported |
| D43 | 793 | Audited; not exported |
| D01 | 179 | Audited; not exported |
| D11 | 45 | Audited; not exported |
| BLOCK CRACK | 3 | Audited; not exported |
| D0W0 | 1 | Audited; not exported |

## Primary group-aware split

| Split | Images |
|---|---:|
| Train | 26,888 |
| Validation | 5,714 |
| Test | 5,783 |
| **Total** | **38,385** |

Exact and near-duplicate groups are kept within a single primary split to reduce leakage. Empty YOLO label files are intentional target-task negatives, because images with only non-target codes or no target codes are not removed.

## Cross-country experiment

- Train: **28,394** non-US images
- Validation: **4,964** non-US images
- Test: **4,805** United States images
- Leakage exclusions from this auxiliary experiment: **222** non-US images visually grouped with held-out US images

Those 222 images remain in the main 70/15/15 experiment and are excluded only from the held-out-country experiment.

## Main interpretation points

1. Strong country and class imbalance means the final comparison should report mAP, per-class AP/recall, macro summaries, and country slices rather than accuracy alone.
2. The 339 extremely small boxes and the box-area distributions make thin cracks partly a small-object problem; Member 2 should report the planned resolution experiment.
3. Country-level differences in brightness, contrast, blur, resolution, and scene composition motivate the held-out-US domain-shift experiment.
4. Shadows, lane markings, repaired regions, blur, and darkness visible in the sample/outlier panels should become explicit error-analysis categories.
5. Both detector models must consume the same images and split manifests; otherwise model effects are confounded with data effects.

## Sources

- RDD2022 official dataset: <https://doi.org/10.6084/m9.figshare.21431547>
- RDD2022 dataset paper: <https://doi.org/10.1002/gdj3.260>
