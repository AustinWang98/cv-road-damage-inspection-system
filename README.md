# Road Damage Inspection System - Project Workspace

## Main deliverable

Use `Road_Damage_Final_Project_Master.ipynb` as the single project notebook. It is intentionally structured as a complete final-project notebook:

- Part A - Member 1: full RDD2022 data engineering and detection EDA.
- Part B - Member 2: YOLO11n, RT-DETR-R18, evaluation, resolution experiment, held-out-country experiment, and error analysis.
- Part C - Member 3: full Pothole Mix data/EDA, DeepLabV3-MobileNetV3, SegFormer-B0, severity scoring, and application.
- Part D - Shared: abstract, consolidated results, deployment, maintenance, limitations, conclusion, references, and presentation evidence.

The Part B-D cells are scaffolds, not fake results. Keep them while the project is in progress and replace each TODO with verified code, tables, and interpretation. After the last member finishes, perform a submission cleanup: remove resolved TODOs, placeholders, temporary status labels, handoff/run guides, and debugging-only cells, leaving only project-relevant methods, reproducible code, final outputs, key interpretations, citations, and concise runtime requirements.

The notebook starts with a manual table of contents, a final-project rubric matrix, and a submission gate. Parts B and C include standalone runtime/data validation cells with extraction/training flags defaulted to `False`; this makes Run All safe while requiring later members to opt in deliberately after checking paths, versions, and GPU availability.

The master notebook embeds the verified full-run outputs directly inside **Part A, Section 6: Exploratory data analysis**. The nine figures are separated into nine presentation-friendly sequences of code, figure, and figure-specific interpretation. Re-running Part A regenerates the same figure files from the full dataset.

The complete original [project proposal](docs/Project_Proposal.docx) and the one-page [course project requirements](docs/Course_Project_Requirements.pdf) are included under `docs/`.

## Repository layout

```text
.
├── README.md
├── Road_Damage_Final_Project_Master.ipynb
├── docs/
│   ├── Project_Proposal.docx
│   ├── Course_Project_Requirements.pdf
│   ├── Member1_EDA_Report.md
│   ├── Notebook_Structure_and_Rubric_Audit.md
│   └── Member1_Run_Guide_CN.md
└── artifacts/
    └── member1/
        ├── EDA_Figures/
        └── Member1_RDD2022_Full_EDA_Results.zip
```

The root stays limited to the README and the master notebook. Human-readable documentation belongs in `docs/`; generated Member 1 evidence belongs in `artifacts/member1/`.

## Current status

| Section | Owner | Status | Recommended runtime |
|---|---|---|---|
| RDD2022 full inventory, XML parsing, data quality, EDA | Member 1 | Implemented and smoke-tested | CPU / High-RAM |
| YOLO + COCO conversion and shared splits | Member 1 | Implemented and smoke-tested | CPU / High-RAM |
| Local full-data execution | Member 1 | Run by Codex; see delivered full EDA report/results | Local CPU |
| YOLO11n | Member 2 | TODO scaffold | Colab GPU |
| RT-DETR-R18 | Member 2 | TODO scaffold | Colab GPU |
| Detection comparison/ablations/error analysis | Member 2 | TODO scaffold | Colab GPU |
| Pothole Mix full data and segmentation EDA | Member 3 | TODO scaffold | CPU / High-RAM |
| DeepLabV3-MobileNetV3 and SegFormer-B0 | Member 3 | TODO scaffold | Colab GPU |
| Severity score and Gradio/Streamlit app | Member 3 | TODO scaffold | GPU training; CPU/GPU inference |
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
| Support the cross-country experiment | Held-out-US COCO JSON for RT-DETR plus `dataset_cross_country.yaml` and image lists for YOLO; 222 possible leakage images are excluded only from this auxiliary experiment. |

## Full-data policy

RDD2022 is not randomly reduced:

- 47,420 official images are inventoried and scanned.
- 38,385 images have released Pascal VOC XML annotations and enter the supervised preparation pipeline.
- 9,035 official test images do not have public XML ground truth; they enter image-level EDA but are not used to fabricate supervised metrics.
- The XML files contain the four project target codes plus additional codes such as D44, D50, and REPAIR. Every image is retained; non-target boxes are audited separately and are not incorrectly remapped into the four target classes.
- The complete usable labeled pool is split into train/validation/test after corrupt/duplicate auditing.
- Near-duplicate groups stay in one primary split.
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

YOLO class IDs are 0-3. COCO category IDs are 1-4. The images are shared; do not make a separate random RT-DETR dataset.

For the cross-country experiment, YOLO uses `dataset_cross_country.yaml`; RT-DETR uses the three `instances_cross_country_*.json` files. Both point to the same held-out-US assignment.

## Recommended run order

1. Run Part A on local CPU or Colab CPU/High-RAM.
2. Save the prepared archive and reports.
3. Change to a fresh Colab GPU runtime for Part B; unzip the archive to `/content/rdd2022_yolo_coco_full_labeled`.
4. Member 2 completes staged YOLO and RT-DETR runs and writes results into Part B.
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

[Notebook_Structure_and_Rubric_Audit.md](docs/Notebook_Structure_and_Rubric_Audit.md) records the whole-notebook requirement mapping, design checks, execution checks, verification performed, and remaining completion work.

[Member1_EDA_Report.md](docs/Member1_EDA_Report.md) records the verified full-run statistics. [Member1_RDD2022_Full_EDA_Results.zip](artifacts/member1/Member1_RDD2022_Full_EDA_Results.zip) contains the complete small outputs (reports, manifests, audit CSVs, configuration, citation file, and figures) without duplicating the multi-gigabyte image dataset.

For direct viewing without unzipping, the nine PNGs are also available in [`artifacts/member1/EDA_Figures/`](artifacts/member1/EDA_Figures/).

## Sources

- RDD2022 official dataset: <https://doi.org/10.6084/m9.figshare.21431547>
- RDD2022 dataset paper: <https://doi.org/10.1002/gdj3.260>
- RT-DETR official implementation: <https://github.com/lyuwenyu/RT-DETR>
- Ultralytics YOLO11 documentation: <https://docs.ultralytics.com/models/yolo11>
- Ultralytics RT-DETR documentation: <https://docs.ultralytics.com/models/rtdetr/>
- Torchvision DeepLabV3 documentation: <https://docs.pytorch.org/vision/stable/models/deeplabv3.html>
- Hugging Face SegFormer documentation: <https://huggingface.co/docs/transformers/model_doc/segformer>
- SHREC 2022 / Pothole Mix paper: <https://iris.cnr.it/retrieve/b6db3fe0-55ff-45b1-a2fb-3ac0027ebc80/main_small.pdf>
