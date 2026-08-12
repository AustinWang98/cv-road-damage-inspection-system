# Road Damage Inspection System - Project Workspace

## Main deliverable

Use `Road_Damage_Final_Project_Master.ipynb` as the single project notebook. It is intentionally structured as a complete final-project notebook:

- Part A - Member 1: full RDD2022 data engineering and detection EDA.
- Part B - Member 2: YOLO11n vs YOLO11s, champion-challenger evaluation, held-out-country experiment, and error analysis.
- Part C - Member 3: full Pothole Mix data/EDA, DeepLabV3-MobileNetV3, SegFormer-B0, severity scoring, and application.
- Part D - Shared: abstract, consolidated results, deployment, maintenance, limitations, conclusion, references, and presentation evidence.

Parts A and B are executed and carry their verified outputs. Part C is implemented and fully run (full Pothole Mix audit/EDA, both segmentation models trained 40 epochs, one-shot test evaluation, severity rule with unit tests, and the Gradio app); its training/evaluation cells stay flag-gated so Run All remains safe, and its evidence lives in `artifacts/member3/`. Part D cells remain scaffolds, not fake results; replace each TODO with verified content. After the last member finishes, perform a submission cleanup: remove resolved TODOs, placeholders, temporary status labels, handoff/run guides, and debugging-only cells, leaving only project-relevant methods, reproducible code, final outputs, key interpretations, citations, and concise runtime requirements.

The notebook starts with a manual table of contents, a final-project rubric matrix, and a submission gate. Part B was executed in Colab GPU sessions and its cells appear in execution order, so it is not part of a single Run All; Part C still ships a standalone runtime/data validator with training flags defaulted to `False`.

The master notebook embeds the verified full-run outputs directly inside **Part A, Section 6: Exploratory data analysis**. The nine figures are separated into nine presentation-friendly sequences of code, figure, and figure-specific interpretation. Re-running Part A regenerates the same figure files from the full dataset. **Part B** follows the same pattern for the detection work: executed code, its real Colab outputs, eight embedded figures, and an interpretation after each result.

The complete original [project proposal](docs/Project_Proposal.docx) and the one-page [course project requirements](docs/Course_Project_Requirements.pdf) are included under `docs/`.

## Repository layout

```text
.
├── README.md
├── Road_Damage_Final_Project_Master.ipynb
├── member2_configs/
├── member2_runs/
├── member2_configs-20260809T235937Z-1-001.zip
├── docs/
│   ├── Project_Proposal.docx
│   ├── Course_Project_Requirements.pdf
│   ├── Member1_EDA_Report.md
│   ├── Notebook_Structure_and_Rubric_Audit.md
│   ├── Member1_Run_Guide_CN.md
│   └── Member3_Run_Guide_CN.md
└── artifacts/
    ├── member1/
    │   ├── EDA_Figures/
    │   └── Member1_RDD2022_Full_EDA_Results.zip
    └── member3/
        ├── eda/
        ├── evaluation/
        └── app/
```

The root contains the two primary deliverables plus Member 2's tracked manifests, captured tables/plots, and small reproducibility-config archive. Human-readable documentation belongs in `docs/`; generated Member 1 evidence belongs in `artifacts/member1/`.

## Current status

| Section | Owner | Status | Recommended runtime |
|---|---|---|---|
| RDD2022 full inventory, XML parsing, data quality, EDA | Member 1 | Implemented and smoke-tested | CPU / High-RAM |
| YOLO + COCO conversion and shared splits | Member 1 | Implemented and smoke-tested | CPU / High-RAM |
| Local full-data execution | Member 1 | Run by Codex; see delivered full EDA report/results | Local CPU |
| Google Drive data handoff | Member 1 | ZIP + SHA-256 uploaded, shared, and validated by Member 2 | Google Drive + Colab |
| YOLO11n | Member 2 | Trained and evaluated (`det-yolo11n-8k-01`) | Colab GPU |
| YOLO11s | Member 2 | Trained and evaluated (`det-yolo11s-8k-01`) | Colab GPU |
| Detection comparison, cross-country, error analysis | Member 2 | Implemented with bootstrap CIs and error slices | Colab GPU |
| Pothole Mix full data and segmentation EDA | Member 3 | Completed on the full 4,340-pair dataset; audit report and figures in `artifacts/member3/eda/` | CPU / High-RAM |
| DeepLabV3-MobileNetV3 and SegFormer-B0 | Member 3 | Both trained 40 epochs on Colab T4; single test-set evaluation done — SegFormer-B0 wins (pothole IoU 0.665 vs 0.653); see `artifacts/member3/evaluation/` | Colab GPU |
| Severity score and Gradio/Streamlit app | Member 3 | Severity rule with passing unit tests; Gradio app smoke-tested with the YOLO11s champion detector (GPU 56 ms vs CPU 2,023 ms first-run); screenshots in `artifacts/member3/app/` | GPU training; CPU/GPU inference |
| Abstract, operations, maintenance, conclusion, references | All members | TODO scaffold | CPU |

The verified full Member 1 run produced **38,385 prepared labeled images**, **55,006 valid target boxes**, and a group-aware **26,888 / 5,714 / 5,783** train/validation/test split. It also scanned all **9,035** unlabeled official-test images at image level. See [the Member 1 EDA report](docs/Member1_EDA_Report.md) for the readable summary and [the results archive](artifacts/member1/Member1_RDD2022_Full_EDA_Results.zip) for the CSV audit tables and nine figures.

## Member 1 responsibility checklist

| Proposal responsibility | Completion evidence |
|---|---|
| Prepare the RDD2022 subset | Completed with the **entire usable labeled pool** (38,385 images), plus image-level inventory of all 9,035 unlabeled official-test images; this exceeds the original subset plan. |
| Convert detection labels | Pascal VOC XML converted to YOLO TXT (class IDs 0-3) and COCO JSON (category IDs 1-4); one degenerate D20 box was audited and excluded. |
| Prepare train/validation/test splits | Group-aware 26,888 / 5,714 / 5,783 primary split, shared by both detector models. |
| Detect corrupt and duplicate images | 0 corrupt images, 4 exact-duplicate rows, and 3,256 near-duplicate rows documented; duplicate groups stay within one split. |
| Produce EDA plots | Nine saved plots covering counts, class/country imbalance, box geometry, image properties, bivariate analysis, samples, and outliers. |
| Document data quality | Full manifest, schema/missingness, parse-error, bbox-quality, duplicate, annotation-code, and split reports plus a Markdown EDA report. |
| Support the cross-country experiment | Held-out-US COCO JSON plus `dataset_cross_country.yaml` and image lists; 222 possible leakage images are excluded only from this auxiliary experiment. |

## Member 2 results summary

Two single-stage detectors of different capacity on Part A's shared split, 640 px, batch 16, seed 42, 30 epochs, NVIDIA L4, Ultralytics 8.4.117. Confidence thresholds were selected on validation (`conf*` 0.228 / 0.237) and the 5,783-image test set was evaluated once.

| Test metric | YOLO11n | YOLO11s |
|---|---:|---:|
| mAP@0.50 | 0.4336 | **0.4439** |
| mAP@0.50:0.95 | 0.2033 | **0.2080** |
| Mean per-class F1 @ conf* | 0.4627 | **0.4783** |
| Ultralytics D40 (pothole) recall @ conf* | 0.2621 | **0.3024** |
| Parameters / GFLOPs | **2.58 M / 6.4** | 9.41 M / 21.4 |
| Batched inference | **1.58 ms** | 3.77 ms |
| Single-image latency | 16.07 ms | 16.28 ms |

The F1 and D40-recall rows above use Ultralytics' internal assignment. The paired bootstrap below uses the notebook's independent, slightly stricter greedy IoU matcher, so its micro-F1 values differ slightly; both methods use the same validation-selected thresholds and agree on the model ordering.

**Champion: YOLO11s.** A paired bootstrap over 1,000 resamples of the test images gives an overall micro-F1 gain of +0.0194 (95% CI [+0.0112, +0.0268]); the D40 gain is +0.0416 (95% CI [+0.0158, +0.0688]). D10 and D20 differences are not significant.

Other Part B findings:

- **Cross-country (strict, leakage-filtered 3,084-image slices):** removing US images from training costs -0.0858 mAP@0.50 on US images (0.5064 -> 0.4206, -17% relative), worst on D10 (-28%) and D40 (-21%).
- **Errors are recall-bound, not classification-bound:** 74-79% of small-tercile boxes are missed versus 38-39% of large ones, and only ~5% of false positives are class confusions (44% are localization, 32% background, 19% duplicates).
- **Both runs were compute-limited:** the best epoch was the last epoch for both primary runs, so these are budget results rather than converged ceilings.

Part B embeds eight figures: model comparison (per-class AP, headline metrics, accuracy vs inference cost), learning curves for both runs, normalized confusion matrices, precision-recall curves, miss-rate slices (class / object size / sharpness), miss rate by country, false-positive taxonomy, and matched qualitative predictions on five hard test images.

Selected reproducibility artifacts are tracked in `member2_runs/` and `member2_configs/`. The complete runs, including model weights, also live in Drive under `RDD2022_Project/member2_runs/`; the weights are intentionally not committed to GitHub.

## Full-data policy

RDD2022 is not randomly reduced:

- 47,420 official images are inventoried and scanned.
- 38,385 images have released Pascal VOC XML annotations and enter the supervised preparation pipeline.
- 9,035 official test images do not have public XML ground truth; they enter image-level EDA but are not used to fabricate supervised metrics.
- The XML files contain the four project target codes plus additional codes such as D44, D50, and REPAIR. Every image is retained; non-target boxes are audited separately and are not incorrectly remapped into the four target classes.
- The complete usable labeled pool is split into train/validation/test after corrupt/duplicate auditing.
- Near-duplicate groups stay in one primary split.
- Part B keeps the full validation (5,714) and test (5,783) splits, and trains on a hash-pinned, stratified **8,000-image** subset of the 26,888 training images because two GPU training runs on the full split exceed the available Colab budget. Both detectors use the identical manifest (`subset_train_8k.txt`, SHA-256 `fcfcb99b...0606e71`), and maximum domain/class composition drift versus the full training pool is 0.01 pp.
- The United States is held out for the cross-country experiment; non-US images visually duplicated with held-out images are excluded from that experiment to prevent leakage.

Pothole Mix should also use the complete 4,340 image-mask pairs and the documented 3,340/496/504 split unless Member 3's duplicate audit justifies a corrected group-aware split. Both segmentation models must use identical data.

## Member 1 output contract

The prepared `rdd2022_yolo_coco_full_labeled` folder contains:

```text
rdd2022_yolo_coco_full_labeled/
├── dataset.yaml
├── dataset_cross_country.yaml
├── cross_country_train.txt
├── cross_country_val.txt
├── cross_country_test.txt
├── images/
│   ├── train/
│   ├── val/
│   └── test/
├── labels/
│   ├── train/
│   ├── val/
│   └── test/
├── annotations/
│   ├── instances_train.json
│   ├── instances_val.json
│   ├── instances_test.json
│   ├── instances_cross_country_train.json
│   ├── instances_cross_country_val.json
│   └── instances_cross_country_test.json
├── figures/
├── reports/
├── CITATION.bib
└── README.md
```

YOLO class IDs are 0-3. COCO category IDs are 1-4. The images are shared; do not build a separate random dataset per model.

For the cross-country experiment, training uses `dataset_cross_country.yaml` and the three `instances_cross_country_*.json` files. Both point to the same held-out-US assignment.

## Google Drive data handoff action

The multi-gigabyte RDD2022 images are intentionally not committed to GitHub. Member 1 generated the complete training handoff archive locally on **2026-08-06**:

```text
rdd2022_yolo_coco_full_labeled.zip
size: 9.9 GiB (Finder display)
SHA-256: 2fc618a2dea071b83cf59aa585840bd421197298a7208111c20b8c6a7d403961
```

Team action status:

- [x] Generate the full prepared-data ZIP from all 38,385 labeled images.
- [x] Generate and record the SHA-256 checksum.
- [x] Upload the ZIP and `.sha256` file to `My Drive/RDD2022_Project/member1_outputs/`.
- [x] Share the uploaded data with Members 2 and 3.
- [ ] Each teammate adds `RDD2022_Project` as a shortcut under their own `My Drive`.
- [x] Run the Part B/B1 validator in Member 2's Colab session and confirm `Member 2 data contract validated.`

Use this shared-Drive layout:

```text
RDD2022_Project/
├── member1_outputs/
│   ├── rdd2022_yolo_coco_full_labeled.zip
│   └── rdd2022_yolo_coco_full_labeled.zip.sha256
├── member2_configs/
├── member2_runs/
├── pothole_mix/
└── member3_runs/
```

After adding the shared folder as a `My Drive` shortcut, the expected Colab archive path is:

```text
/content/drive/MyDrive/RDD2022_Project/member1_outputs/rdd2022_yolo_coco_full_labeled.zip
```

Do not resize or recompress the images before handoff. Both detectors must load the original-resolution images and apply their configured training transforms dynamically. Do not upload the official raw ZIP or the extracted raw tree; the prepared archive is the only RDD2022 image package Members 2 and 3 need from Member 1.

## Recommended run order

1. Run Part A on local CPU or Colab CPU/High-RAM.
2. Save the prepared archive and reports.
3. Change to a fresh Colab GPU runtime for Part B; unzip the archive to `/content/rdd2022_yolo_coco_full_labeled`.
4. Member 2 runs the staged YOLO11n and YOLO11s experiments and writes results into Part B.
5. Member 3 completes full Pothole Mix preparation and the two segmentation models in Part C.
6. Merge all final metrics, limitations, architecture, citations, and presentation evidence in Part D.
7. Execute the notebook from a clean runtime where practical and confirm every referenced artifact exists.
8. After all members finish, delete resolved TODOs, handoff instructions, temporary workflow text, and debugging-only cells before submission; retain the code and documentation needed to reproduce the project.

Do not use one uninterrupted Run All for the entire finished project: Part A is a CPU/high-storage data job, while Parts B and C should each restart independently in fresh GPU runtimes and consume persisted Drive artifacts.

## Collaboration rules

- Do not change the primary RDD2022 split after either detection model starts formal training.
- Do not compare models trained on different image counts in the main champion-challenger table.
- Keep `best` and `last` checkpoints and make all formal runs resumable.
- Record seed, data manifest/hash, package versions, repository commit, GPU type, duration, and checkpoint for every run.
- Do not tune on a final test set or the held-out United States set.
- Use the same segmentation split, preprocessing, and metric implementation for both segmentation models.
- Label severity output as a prototype decision rule, not an engineering-certified condition rating.
- Add citations for datasets, papers, official implementations, and reused code.
- Replace TODOs with actual evidence; never fill tables with expected or invented results.
- Keep workflow scaffolding during collaboration, then remove it in the final submission cleanup so the delivered notebook reads as a finished project rather than a work plan.

## Other documentation

[Member1_Run_Guide_CN.md](docs/Member1_Run_Guide_CN.md) contains the detailed Chinese proposal review, disk/runtime guidance, Colab steps, and handoff paths.

[Member3_Run_Guide_CN.md](docs/Member3_Run_Guide_CN.md) contains the Chinese guide for executing Part C: Pothole Mix acquisition, Colab data flow (Drive ZIP unzipped to the runtime's local disk), staged training flags, evaluation, and the app demo.

[Notebook_Structure_and_Rubric_Audit.md](docs/Notebook_Structure_and_Rubric_Audit.md) records the whole-notebook requirement mapping, design checks, execution checks, verification performed, and remaining completion work.

[Member1_EDA_Report.md](docs/Member1_EDA_Report.md) records the verified full-run statistics. [Member1_RDD2022_Full_EDA_Results.zip](artifacts/member1/Member1_RDD2022_Full_EDA_Results.zip) contains the complete small outputs (reports, manifests, audit CSVs, configuration, citation file, and figures) without duplicating the multi-gigabyte image dataset.

For direct viewing without unzipping, the nine PNGs are also available in [`artifacts/member1/EDA_Figures/`](artifacts/member1/EDA_Figures/).

## Sources

- RDD2022 official dataset: <https://doi.org/10.6084/m9.figshare.21431547>
- RDD2022 dataset paper: <https://doi.org/10.1002/gdj3.260>
- Ultralytics YOLO11 documentation: <https://docs.ultralytics.com/models/yolo11>
- Torchvision DeepLabV3 documentation: <https://docs.pytorch.org/vision/stable/models/deeplabv3.html>
- Hugging Face SegFormer documentation: <https://huggingface.co/docs/transformers/model_doc/segformer>
- SHREC 2022 / Pothole Mix paper: <https://iris.cnr.it/retrieve/b6db3fe0-55ff-45b1-a2fb-3ac0027ebc80/main_small.pdf>
