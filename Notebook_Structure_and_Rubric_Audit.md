# Master Notebook Structure and Final-Project Rubric Audit

Audited artifact: `Road_Damage_Final_Project_Master.ipynb`

## Audit conclusion

The notebook is now structurally aligned with the final-project rubric and logically separates the work into two cognitive problems: multi-class object detection and binary pothole semantic segmentation. Member 1 is complete and contains verified full-run outputs and interpretation. Parts B-D intentionally remain instructions, executable setup guards, result formats, and TODOs; the project is not a complete final submission until those TODOs are replaced by executed work.

## Rubric coverage

| Requirement | Notebook evidence | Current completion |
|---|---|---|
| Author names and table of contents | Required author placeholders, manual TOC, contribution table | TOC complete; names/contributions pending |
| Abstract (5) | D1 specifies problem, data, four models, results, deployment, conclusion | Pending final results |
| EDA (15) | A2-A7: counts, schema/missingness, tables, univariate/bivariate analysis, histograms, Q-Q, heatmap, violin, samples, outliers, and a separate interpretation after each figure; C2 specifies segmentation EDA | Detection complete; segmentation pending |
| Two cognitive problems | B: object detection; C: semantic segmentation | Defined correctly |
| Two competing models per problem | YOLO11n vs RT-DETR-R18; DeepLabV3-MobileNetV3 vs SegFormer-B0 | Training pending |
| Detailed model evaluation (40) | Shared-test protocols, per-class/foreground metrics, confusion/PR/error figures, latency/resources, controlled experiments | Formats/protocol complete; results pending |
| Algorithm descriptions and pros/cons | Mechanism, expected strengths, data-specific risks, measured pros/cons tables in B3-B5 and C3-C5 | Structure complete; measured evidence pending |
| Model Operations (10) | C7 application; D3 deployment mechanism/acceptance table; D4 monitoring, retraining, champion gate, canary, rollback | Implementation/evidence pending |
| Conclusion (10) | D5 limitations, D6 conclusion requirements, D7 authoritative sources/reused-code requirements | Pending final results |
| Presentation (20) | D8 gives a 20-minute structure, evidence consistency, speaker assignment, and rehearsal requirements | Slides/rehearsal pending |
| Required deliverables | D0 checks report, PDF/PPT, source code, citations, authors, secrets, clean-runtime verification | Pending final submission |

## Logical and experimental design checks

- Main YOLO11n and RT-DETR-R18 runs use the same 38,385-image labeled pool and group-aware split.
- Official unlabeled test images are used only for image-level EDA, not fabricated supervised metrics.
- Empty target-label files remain valid negative examples.
- Exact/near-duplicate groups do not cross primary partitions.
- United States is held out only for the auxiliary cross-country experiment; 222 possible leakage images are excluded from that experiment only.
- Model/checkpoint/threshold selection uses validation data; final and held-out-country test sets are not tuning sets.
- Detection uses COCO-style and per-class metrics rather than accuracy.
- Segmentation prioritizes pothole IoU/Dice/recall and Boundary F1 rather than background-dominated pixel accuracy.
- Both segmentation models must share split, paired spatial transformations, loss implementation, and metrics; model-specific normalization is documented.
- Pothole segmentation is not presented as crack segmentation.
- Severity is explicitly a non-certified prototype with human review.

## Execution and handoff checks

- Part A installs only missing dependencies and has a Python download fallback when `wget` is unavailable.
- Part A records Python/package versions in `run_config.json`.
- Part B has a standalone Drive extraction/data-contract validator; extraction and training flags default to `False`.
- The YOLO template supports pipeline/timing/full stages and checkpoint resume.
- The RT-DETR template requires a pinned official commit, reviewed four-class config, correct category remapping, and explicit tune/resume behavior.
- Part C has a standalone image-mask pairing/split validator and independent runtime flags.
- DeepLabV3-MobileNetV3 and SegFormer-B0 construction templates use two output classes; the SegFormer processor keeps background class 0.
- Parts B-D contain no fake results or saved model outputs.

## Verification performed

- All notebook code cells parse successfully.
- All nine saved EDA image outputs occur inside Part A Section 6; every figure is in its own code/figure/interpretation sequence, and none occur in Parts B-D.
- Part A executed end-to-end on a small directory-compatible fixture, producing reports, nine figures, main YOLO/COCO exports, and cross-country YOLO/COCO exports.
- Parts B-D executed safely with preparation/training flags left at their defaults; no download, install, extraction, or training was triggered.
- The actual full Member 1 output remains validated at 47,420 inventoried images, 38,385 prepared labeled images, and 55,006 valid boxes.

## Remaining completion work

1. Add all three names and contribution evidence.
2. Member 2 must execute and interpret B3-B8.
3. Member 3 must verify Pothole Mix source/licenses, then execute and interpret C1-C7.
4. All members must replace D1-D8 TODOs with final evidence, citations, report, and presentation artifacts.
5. Perform a final clean-runtime/reproducibility review before submission.
6. Run the final editorial cleanup: delete resolved TODOs, placeholders, temporary status/handoff text, and debugging-only cells while retaining project methods, executable code, final outputs, key interpretations, citations, and essential runtime requirements.
