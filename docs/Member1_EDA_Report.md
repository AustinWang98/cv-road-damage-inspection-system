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

## Figure-by-figure interpretations

1. **Annotation-code distribution:** The official XML files contain 10,705 boxes outside D00/D10/D20/D40. They are audited but not remapped, preserving a reproducible four-class problem definition.
2. **Country and class counts:** Japan contributes 10,506 labeled images versus 2,829 from Czech; D00 contributes 47.3% of target boxes while D40 contributes 11.9%. Report country slices and per-class AP/recall so aggregate mAP cannot hide weak pothole performance.
3. **Damage count and country-class mix:** The 14,618 no-target images are valid negatives, and 8,300 images contain multiple target classes. This supports multi-object detection and a held-out-country experiment rather than single-label classification.
4. **Bounding-box distributions:** The median target occupies only 1.43% of the image, and 339 valid boxes occupy less than 0.01%. Compare 512 versus 640 resolution and report small-object/per-class recall.
5. **Scale by class and object location:** D40 has the smallest median relative area (0.54%) while D20 has the largest (8.17%). Models should be checked for edge-of-frame misses instead of relying on the common center-road location prior.
6. **Resolution, brightness, contrast, and blur:** Norway's median resolution is 8.22 MP versus roughly 0.26-0.52 MP in other domains, and brightness also shifts by country. This motivates country-stratified reporting and the held-out-US test.
7. **Brightness relationship and Q-Q plot:** Brightness and target count have only a weak Spearman association (about 0.20), while the Q-Q plot is non-linear. Robust percentiles and country slices are more appropriate than one pooled Gaussian assumption.
8. **Annotated class samples:** Differences in camera height, pavement texture, scale, and lighting confirm the need for per-class recall and matched qualitative predictions; several thin cracks may be vulnerable to resizing.
9. **Lighting and sharpness outliers:** Dark, bright, blurred, and sharp files remain readable rather than corrupt. Later error analysis should quantify missed damage and false positives under these conditions instead of deleting the images automatically.

**Leakage decision:** Four exact-duplicate rows and 3,256 exact/near-duplicate rows are retained but group-locked, preventing visually similar images from crossing train, validation, and test partitions.

## Sources

- RDD2022 official dataset: <https://doi.org/10.6084/m9.figshare.21431547>
- RDD2022 dataset paper: <https://doi.org/10.1002/gdj3.260>
