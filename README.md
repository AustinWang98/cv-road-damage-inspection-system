# Road Damage Inspection System

Final project by **Austin Wang**, **Kevin Fan**, and **RJ Xia** for Deep Learning and Computer Vision.

## Final deliverables

[`Road_Damage_Final_Project_Master_New.ipynb`](Road_Damage_Final_Project_Master_New.ipynb) is the complete report and source-code record. It contains:

- **Part A — Austin Wang:** full RDD2022 acquisition, data quality, EDA, duplicate-safe splitting, and YOLO/COCO export.
- **Part B — Kevin Fan:** YOLO11n vs YOLO11s, controlled shared-test comparison, bootstrap uncertainty, cross-country generalization, and error analysis.
- **Part C — RJ Xia:** full Pothole Mix audit, DeepLabV3-MobileNetV3 vs SegFormer-B0, severity-rule tests, and an integrated Gradio application.
- **Part D — Shared:** abstract, consolidated scorecard, deployment and maintenance plans, limitations, conclusion, and references.

The master notebook embeds verified outputs and short interpretations after every important figure or table. Completed-project runtime guidance and presentation-only material have been removed without deleting code or saved output.

[`Road_Damage_Final_Project_Presentation.ipynb`](Road_Damage_Final_Project_Presentation.ipynb) is the separate 20-minute, three-speaker presentation notebook. It contains no code: the audience-facing storyline uses verified tables, plots, labeled examples, qualitative failures, application screenshots, workflow diagrams, short metric explanations, and concise explanatory paragraphs after the main visuals.

[`Road_Damage_Final_Project_Presentation.pdf`](output/pdf/Road_Damage_Final_Project_Presentation.pdf) is the submission-ready, 32-page Letter-size PDF export with all presentation visuals, interpretations, document metadata, and page numbers preserved.

### Submission packaging

The course handout explicitly requests a **report/presentation PDF or PPT plus full source code**. Submit the PDF above as the presentation artifact and this GitHub repository as the complete source-code and evidence record.

## Headline results

### Object detection

Both models used the identical hash-pinned 8,000-image training manifest, seed 42, 640 px input, 30 epochs, and the same 5,783-image test set.

| Test metric | YOLO11n | YOLO11s |
|---|---:|---:|
| mAP@0.50 | 0.4336 | **0.4439** |
| mAP@0.50:0.95 | 0.2033 | **0.2080** |
| Mean per-class F1 at validation-selected `conf*` | 0.4627 | **0.4783** |
| D40 pothole recall | 0.2621 | **0.3024** |
| Parameters / GFLOPs | **2.58 M / 6.4** | 9.41 M / 21.4 |
| Batched inference on NVIDIA L4 | **1.58 ms** | 3.77 ms |

**Detection champion: YOLO11s.** Its paired-bootstrap micro-F1 gain is +0.0194 with 95% CI [+0.0112, +0.0268]. Removing US training data reduces strict US mAP@0.50 from 0.5064 to 0.4206 (-16.9% relative), and 74–79% of small-tercile targets remain missed.

### Pothole segmentation

Both models used the identical official 3,340/496/504 Pothole Mix split, 512 px input, 40 epochs, shared augmentation/loss/metrics, and one shared 504-image test set.

| Test metric | DeepLabV3-MobileNetV3 | SegFormer-B0 |
|---|---:|---:|
| Pothole IoU | 0.6533 | **0.6646** |
| Dice | 0.7903 | **0.7985** |
| Pothole precision | 0.7889 | **0.8623** |
| Pothole recall | **0.7917** | 0.7436 |
| Boundary F1 | 0.7188 | **0.7334** |
| Inference on NVIDIA T4 | **12.15 ms** | 12.55 ms |
| Parameters | 11.02 M | **3.71 M** |

**Segmentation champion: SegFormer-B0.** It has the best overlap and boundary quality with about one third of the parameters. DeepLabV3 remains the recall-oriented alternative when misses cost more than false alarms.

## Data and evidence

### RDD2022

- 47,420 official images inventoried.
- 38,385 images with public Pascal VOC XML annotations.
- 9,035 official-test images without released XML ground truth; used only for image-level EDA.
- 55,006 valid D00/D10/D20/D40 boxes.
- Duplicate-safe 26,888 / 5,714 / 5,783 primary split.
- The two formal detectors train on the same stratified 8,000-image subset because the full two-model schedule exceeded the available Colab budget.

### Pothole Mix

- All 4,340 image-mask pairs audited; split 3,340 / 496 / 504.
- 0 corrupt pairs and 0 image-mask dimension mismatches.
- 1,184 pothole-positive pairs; 3,156 crack-only/background hard negatives.
- 17 documented official-split near-duplicate groups (565 images); two groups contain pothole pixels. The published split is retained for benchmark comparability and disclosed as a limitation.

## Application and operations

The verified Gradio prototype loads **YOLO11s + SegFormer-B0** and displays boxes/classes/confidences, pothole mask and area, transparent priority reasons, model/runtime metadata, latency, and a human-review warning. The priority is a prototype ranking rule, **not** an engineering-certified road-condition assessment.

[`artifacts/final/deployment_architecture.svg`](artifacts/final/deployment_architecture.svg) documents the production boundary: validated input, versioned preprocessing, two champion services, evidence fusion, mandatory review, observability, model registry, canary deployment, and rollback.

## Repository layout

```text
.
├── README.md
├── Road_Damage_Final_Project_Master_New.ipynb
├── Road_Damage_Final_Project_Presentation.ipynb
├── output/
│   └── pdf/
│       └── Road_Damage_Final_Project_Presentation.pdf
├── artifacts/
│   ├── final/                 # final trade-off and deployment visuals
│   ├── member1/               # verified RDD2022 EDA evidence
│   ├── member2/
│   │   ├── configs/           # hash-pinned training manifests
│   │   └── runs/              # checkpoints, metrics, curves, confusion matrices
│   ├── member3/               # EDA, model histories, evaluation, app screenshots
│   └── presentation/          # presentation workflow and extracted result visuals
└── docs/
    ├── Course_Project_Requirements.pdf
    ├── Notebook_Structure_and_Rubric_Audit.md
    ├── Member1_EDA_Report.md
    └── Project_Proposal.docx
```

## Reproducibility record

The public multi-gigabyte datasets are intentionally excluded from GitHub. Exact archive identifiers and hashes, split/training manifests and hashes, random seeds, package/GPU records, checkpoint provenance, saved histories, test tables, figures, and executable source are retained. The three final detection checkpoints are tracked in `artifacts/member2/runs/`; complete original run directories remain in the shared Drive workspace.

## Presentation ownership

Use [`Road_Damage_Final_Project_Presentation.ipynb`](Road_Damage_Final_Project_Presentation.ipynb) for the final talk:

- **Member 1 — Austin Wang:** problem framing, RDD2022 data quality and EDA, split design, and combined scorecard.
- **Member 2 — Kevin Fan:** detector comparison, domain shift, error analysis, and deployment operations.
- **Member 3 — RJ Xia:** segmentation comparison, qualitative failures, application, and conclusion.

The master notebook remains available for detailed methods, code, complete tables, and questions.

## Primary sources

- RDD2022 dataset: <https://doi.org/10.6084/m9.figshare.21431547>
- RDD2022 paper: <https://doi.org/10.1002/gdj3.260>
- Pothole Mix distribution: <https://doi.org/10.17632/kfth5g2xk3.2>
- SHREC 2022 paper: <https://iris.cnr.it/retrieve/b6db3fe0-55ff-45b1-a2fb-3ac0027ebc80/main_small.pdf>
- Ultralytics YOLO11: <https://docs.ultralytics.com/models/yolo11>
- Torchvision DeepLabV3: <https://docs.pytorch.org/vision/stable/models/deeplabv3.html>
- Hugging Face SegFormer: <https://huggingface.co/docs/transformers/model_doc/segformer>

The master notebook D7 section contains the full dataset, model, implementation, and reused-code reference list.
