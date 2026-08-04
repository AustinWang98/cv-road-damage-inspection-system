# Final Project 评估与 Member 1 运行说明

## 结论

三人组 proposal 的总体方向合理，能够覆盖课程最重要的硬性要求：两个不同的认知问题（目标检测、语义分割），每个问题两个 champion-challenger 模型，并包含 EDA、模型评估、部署架构和维护计划。

根据“不要只使用部分数据”的决定，Member 1 的最终方案改为：

- 盘点和读取官方全部 47,420 张图片。
- 对全部图片做文件存在性、损坏、分辨率、亮度、对比度、模糊度和重复图片检查。
- 对有公开 XML 框标注的全部 38,385 张图片做完整 detection EDA 和数据转换。
- 官方 9,035 张 test 图片没有公开框标注，因此可以进入全量图像 EDA，但不能假装有标签，也不能直接用于有监督指标计算。
- 从完整的有标签池重新建立 70%/15%/15% train/validation/test，保证近重复图片不跨分区。
- 同时导出 YOLO 和 COCO 标注，让 YOLO11 与 RT-DETR-R18 使用完全相同的图片和分区。

官方文件清单的完整构成为：

| Source domain | 有 XML 标签的 train | 无公开 XML 的 official test | 合计 |
|---|---:|---:|---:|
| China_Drone | 2,401 | 0 | 2,401 |
| China_MotorBike | 1,977 | 500 | 2,477 |
| Czech | 2,829 | 709 | 3,538 |
| India | 7,706 | 1,959 | 9,665 |
| Japan | 10,506 | 2,627 | 13,133 |
| Norway | 8,161 | 2,040 | 10,201 |
| United_States | 4,805 | 1,200 | 6,005 |
| **Total** | **38,385** | **9,035** | **47,420** |

## 已完成的全量运行结果

这不是只在小样本上试跑：本机已经完成一次全量读取、图像审计、EDA、split 和 YOLO/COCO 导出，并通过一致性校验。

| 指标 | 实际结果 |
|---|---:|
| 官方图片总盘点 | 47,420 |
| 有标签图片 | 38,385 |
| 无公开 XML 的 official-test 图片 | 9,035 |
| 有效四分类目标框 | 55,006 |
| 非目标代码框（单独审计、不导出） | 10,705 |
| 腐坏图片 | 0 |
| 无四分类目标框的负样本图片 | 14,618 |
| 极小目标框 | 339 |
| 排除的退化框 | 1 |
| Primary split | 26,888 / 5,714 / 5,783 |

完整运行还生成了九张 EDA 图、逐图 manifest、逐框 audit、schema/missingness、重复与近重复报告以及 cross-country COCO 标注。结果包只收录这些小型报告与图表，不再复制多 GB 图片。

主 notebook 已将上述全量运行的真实统计和九张 EDA 图放回 Part A 的 `6. Exploratory data analysis` 正常流程中。现在每一张图都有独立的“代码 → 图 → 该图解释”，方便答辩时逐图阅读；非核心检查只保留一句简短说明。直接打开 notebook 即可查看，不需要先重新运行；单独的 PNG 文件也保存在 `Member1_EDA_Figures/`。

协作阶段应保留 Part B-D 的脚手架和 TODO，方便后续成员接手。最后一位成员完成后，必须做一次提交清理：删除已解决的 TODO、占位符、临时状态、交接/操作指南和仅用于调试的单元，只保留项目方法、可复现代码、最终输出、关键解释、引用以及必要的运行要求。

## 与评分要求的对应关系

| 评分项 | Proposal 覆盖情况 | 需要落实的证据 |
|---|---|---|
| Abstract (5) | 合理 | 训练结束后写入真实方法和结果，不能只写预期结果 |
| EDA (15) | 覆盖充分 | 全量记录数、schema、缺失值、图像样本、直方图、Q-Q、热力图、violin、异常样本和双变量分析 |
| 两个认知问题 | 满足 | Detection 与 semantic segmentation |
| 每个问题两个模型 | 满足 | YOLO11n vs RT-DETR-R18；DeepLabV3-MobileNetV3 vs SegFormer-B0 |
| 详细评估 | 方向合理 | 相同测试集；检测使用 mAP、precision、recall、F1、per-class AP、速度与资源；分割使用 IoU、Dice、Boundary F1 等 |
| Model Operations (10) | 满足 | Gradio/Streamlit 架构、监控、人工复核、再训练与 champion gate |
| Conclusion (10) | 尚待结果 | 必须讨论发现、局限、引用和后续工作 |
| Presentation (20) | 可行 | 三人 20 分钟建议每人约 6 分钟，留 2 分钟开场/总结或提问 |

## Proposal 必须修正的地方

1. **不要让两个检测模型使用不同数量的训练图片。** Proposal 中允许 RT-DETR 使用更小子集，这会把“模型架构差异”和“数据量差异”混在一起，比较不公平。主实验必须共享同一套 split。
2. **明确官方 test 没有公开 XML 标签。** 全量 47,420 张可以完整盘点，但监督训练与指标只能使用 38,385 张有标签图片，再从中划分 test。
   官方 XML 还包含 D44、D50、REPAIR 等非本项目四分类目标代码；完整流水线保留所有有标签图片，将这些框单独审计，但不会错误映射成 D00/D10/D20/D40。
3. **RT-DETR-R18 不能直接按 Ultralytics 的轻量模型来写。** Ultralytics 官方预训练权重主要是 RT-DETR-L/X；R18 应使用官方 `lyuwenyu/RT-DETR` PyTorch/Paddle 实现。为此 Member 1 同时输出 COCO JSON。
4. **全量训练时间要重新估计。** 38,385 张检测图片跑 YOLO 和 RT-DETR，不应再承诺所有正式训练都能在普通免费 Colab 的一次连续 session 内完成。必须保存 `best` 与 `last` checkpoint，并支持 resume。
5. **补正式引用。** 当前 proposal 的“Official repository / paper / Kaggle mirror”只是文字，没有真实超链接或参考文献条目。最终报告至少引用官方 Figshare、RDD2022 论文、模型原论文/官方实现和复用代码。
6. **修复 Word 中损坏的公式。** 例如 8,000-12,000、1,500-2,500、损伤面积公式在渲染后已经变形。虽然现在改为全量数据，最终报告仍不能保留这些坏公式。
7. **最终报告补作者名与目录。** 这是课程 PDF 明确要求。
8. **Severity score 必须标注为 prototype。** 不能将图像面积规则表述成经过土木工程认证的道路维修标准。
9. **Pothole Mix 也建议改成全量。** 该数据集公开说明为 4,340 组 image-mask，官方 split 是 3,340/496/504；既然团队决定不抽子集，Member 3 应让 DeepLabV3 与 SegFormer 使用同一套完整官方 split。还要检查组成数据集中的相似帧和各自许可，不能只写“public dataset”而不核对来源。

## 在哪里运行

### Member 1：数据读取、转换和 EDA

优先选择：**本机 CPU** 或 **Google Colab CPU / High-RAM**。不需要 GPU。

- 官方 ZIP：约 13.26 GB（页面显示约 12.36 GB）。
- 全量解压、硬链接数据集、临时压缩包和 EDA 输出同时存在时，建议准备 45-60 GB 空闲空间。
- 本次全量运行后，本机可用空间已经明显下降；不要在本机再次下载或生成第二套完整数据。需要复现时优先使用具有足够临时磁盘的 Colab/云端环境。
- 本次全量运行已经下载并保留官方 ZIP 和准备后的数据，因此不要再重复下载一份；正式清理前先确认 Member 2 已拿到所需的 prepared archive 或共享目录。
- notebook 默认在同一个本地/Colab 文件系统中使用硬链接，避免为了 YOLO/COCO 导出再复制一遍图片。
- 如果在 Colab 运行并保存完整训练集 ZIP，Google Drive 还需留出约 13-20 GB。

### Member 2/3：模型训练

使用 **Google Colab GPU**。全量训练建议：

- 先做 300-500 张、1-2 epoch 的 pipeline test。
- 然后做 1,000-2,000 张、3-5 epoch 的耗时与显存测试。
- 正式全量训练保存 `best` 和 `last` checkpoint，并启用 resume。
- YOLO11 与 RT-DETR 主比较必须使用 Member 1 生成的同一 train/val/test。
- 一次 Colab session 不够时，从 Drive 中的 `last` checkpoint 继续，不要重新开始。

## Colab 操作步骤

1. 打开 Google Colab，上传 `Road_Damage_Final_Project_Master.ipynb`。Part A 是本次已经完成的 Member 1 流水线；Part B-D 是为 Member 2、Member 3 和共享报告保留的后续章节，不要删除。
2. 在 `Runtime -> Change runtime type` 中选择 CPU；如果能选 High-RAM，优先选择 High-RAM。不要为 EDA 占用 GPU。
3. 只执行 Part A（从 dependency/configuration 到 `10. Handoff to Members 2 and 3`），不要在 CPU runtime 继续运行 Part B-D。第一次会要求挂载 Google Drive。
4. notebook 自动从官方 Figshare 下载 RDD2022，不需要 Kaggle token。
5. 等待完整下载、解压、47,420 张图像扫描、EDA、YOLO/COCO 转换和压缩完成；首次全量运行可能需要数小时，取决于下载和 Drive 速度。
6. 结果默认保存到：

   `MyDrive/RDD2022_Project/member1_outputs/`

7. 重点检查：

   - `reports_and_figures/reports/EDA_REPORT.md`
   - `reports_and_figures/reports/prepared_image_manifest.csv`
   - `reports_and_figures/reports/prepared_bbox_audit.csv`
   - `reports_and_figures/figures/`
   - `rdd2022_yolo_coco_full_labeled.zip`

## Member 2 如何使用数据

在新的 GPU Colab session 中执行：

```python
from pathlib import Path

archive = Path('/content/drive/MyDrive/RDD2022_Project/member1_outputs/rdd2022_yolo_coco_full_labeled.zip')
target = Path('/content/rdd2022_yolo_coco_full_labeled')
target.mkdir(parents=True, exist_ok=True)
```

然后在一个 shell cell 解压：

```bash
!unzip -q "/content/drive/MyDrive/RDD2022_Project/member1_outputs/rdd2022_yolo_coco_full_labeled.zip" -d "/content/rdd2022_yolo_coco_full_labeled"
```

YOLO11 使用：

```python
data_yaml = '/content/rdd2022_yolo_coco_full_labeled/dataset.yaml'
```

RT-DETR-R18 使用同一图片目录以及：

```text
/content/rdd2022_yolo_coco_full_labeled/annotations/instances_train.json
/content/rdd2022_yolo_coco_full_labeled/annotations/instances_val.json
/content/rdd2022_yolo_coco_full_labeled/annotations/instances_test.json
```

跨国家实验使用 `instances_cross_country_train.json`、`instances_cross_country_val.json` 和 `instances_cross_country_test.json`；United States 是完全 held-out test country，其余国家进入 train/validation。

YOLO 的跨国家实验直接使用：

```python
cross_country_yaml = '/content/rdd2022_yolo_coco_full_labeled/dataset_cross_country.yaml'
```

其中 `cross_country_train.txt`、`cross_country_val.txt` 和 `cross_country_test.txt` 指向与主实验相同的物理图片和 YOLO 标签，不会生成第二套随机数据。

## 正式引用

- RDD2022 官方数据集：<https://doi.org/10.6084/m9.figshare.21431547>
- RDD2022 数据论文：<https://doi.org/10.1002/gdj3.260>
- RDD2022 arXiv：<https://arxiv.org/abs/2209.08538>
- RT-DETR 官方实现：<https://github.com/lyuwenyu/RT-DETR>
- Ultralytics YOLO11 文档：<https://docs.ultralytics.com/models/yolo11>
- Ultralytics RT-DETR 文档：<https://docs.ultralytics.com/models/rtdetr/>
- Torchvision DeepLabV3 文档：<https://docs.pytorch.org/vision/stable/models/deeplabv3.html>
- Hugging Face SegFormer 文档：<https://huggingface.co/docs/transformers/model_doc/segformer>
