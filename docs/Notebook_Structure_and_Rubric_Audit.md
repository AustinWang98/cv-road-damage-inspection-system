# Master Notebook Final-Project Rubric Audit

Audited artifact: `Road_Damage_Final_Project_Master_New.ipynb`
Final owners: Austin Wang, Kevin Fan, RJ Xia

## Audit conclusion

The master notebook contains the complete report and a timed presentation outline. It satisfies the report-content requirements for authors/TOC, abstract, EDA, two cognitive problems, two competing models per problem, detailed evaluation, algorithm trade-offs, model operations, conclusion, references, and resources. D8 maps the same verified evidence to a 20-minute three-person presentation.

The notebook retains reproducible code, verified outputs, and a concise interpretation after each important plot or table.

## Rubric coverage

| Requirement | Notebook evidence | Completion |
|---|---|---|
| Author names and TOC | Title block; TOC; D0 contribution table | Complete |
| Abstract (5) | D1 summarizes problem, datasets, four models, results, application and limitations | Complete |
| EDA (15) | A2-A7 and C2: counts, schema/missingness, univariate/bivariate plots, heatmaps, Q-Q/violin, samples and outliers | Complete |
| Two cognitive problems | Part B four-class detection; Part C binary pothole semantic segmentation | Complete |
| Two models per problem | YOLO11n vs YOLO11s; DeepLabV3-MobileNetV3 vs SegFormer-B0 | Complete |
| Detailed model evaluation (40) | Shared held-out sets, appropriate class/foreground metrics, confusion/PR curves, bootstrap CI, error slices, qualitative cases, latency and resources | Complete |
| Algorithms and pros/cons | B3-B5 and C3-C5 state mechanisms, expected risks, measured advantages/disadvantages and deployment implications | Complete |
| Model Operations (10) | C7 verified demo; D3 architecture/serving contract; D4 monitoring, retraining, gate, canary and rollback | Complete |
| Conclusion (10) | D5 limitations/ethics; D6 evidence-based conclusion/next work; D7 references/resources | Complete |
| Presentation (20) | D8 assigns all three named members exact evidence and speaking time across the full 20 minutes | Complete as a notebook presentation plan |
| Submission package | Master notebook and repository provide the report, source, and verified evidence | Complete |
| Full source | Executable methods and all small evaluation evidence are in the repository; public multi-GB datasets are hash-referenced | Complete |

## Experimental-integrity checks

- RDD2022 supervised metrics use only the 38,385 images with released XML annotations; the 9,035 official-test images without public XML enter image-level EDA only.
- Non-target XML codes are audited separately and are not remapped into D00/D10/D20/D40.
- The primary detection split is duplicate-safe. Both formal detectors share the same 8,000-image manifest, seed, schedule, augmentation policy, validation set and test set.
- Detection checkpoints and `conf*` are selected on validation; the shared test is opened for final evaluation. The paired image-level bootstrap reports uncertainty.
- The strict cross-country evaluation excludes train and selection overlap before comparing all-country and non-US models on US images.
- Both segmentation models share the official split, paired transforms, label mapping, loss, checkpoint criterion, threshold procedure and metric implementation.
- The segmentation threshold is selected on validation and the 504-image test is evaluated once per model.
- Background-dominated pixel accuracy does not select the segmentation champion; pothole IoU/Dice/recall and Boundary F1 do.
- Cross-split Pothole Mix near-duplicate evidence is disclosed, not hidden. The published split is retained for benchmark comparability.
- Segmentation is described as pothole-only. The severity output is explicitly non-certified and requires human review.

## Verification evidence

- Part A: 47,420 inventoried images; 38,385 labeled images; 55,006 valid target boxes; 26,888 / 5,714 / 5,783 split; nine tracked figures.
- Part B: three 30-epoch formal histories; test tables and counts reconcile to 8,296 ground-truth boxes; bootstrap significance labels match their intervals; three readable detection checkpoints are tracked.
- Part C: 4,340-row manifest; 0 corrupt and 0 dimension mismatches; two 40-epoch histories; champion table, five evaluation figures, severity tests and CPU/GPU application screenshots.
- Part D: all numeric claims come from the tracked Part B/C tables and histories; deployment and trade-off visuals contain only those verified values.
- Notebook JSON and all code-cell Python syntax are checked during final validation; no saved error output is accepted.
