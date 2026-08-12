# Member 3 运行指南（Part C：Pothole Mix 分割、严重度评分与应用）

本指南对应主 notebook `Road_Damage_Final_Project_Master.ipynb` 的 Part C（C1–C7）。所有代码已经写好并通过本地冒烟测试；你的任务是：**获取数据 → 在 Colab 上按阶段执行 → 把打印出来的真实数字填进解读和表格**。项目规则：绝不能在跑出结果之前填入"预期"数字。

## 0. 你负责交付什么

| 小节 | 内容 | 运行环境 |
|---|---|---|
| C1 | Pothole Mix 数据获取、配对校验、来源/许可记录 | CPU |
| C2 | 全量数据审计 + 分割 EDA（6 张图 + 审计 CSV）+ 解读 | CPU / 高内存 |
| C3 | DeepLabV3-MobileNetV3 训练（共享数据/损失/训练循环） | Colab GPU |
| C4 | SegFormer-B0 训练（与 C3 完全相同的数据与损失） | Colab GPU |
| C5 | 冠军-挑战者对比评估（测试集只跑一次）+ 5 张图 | Colab GPU |
| C6 | 严重度/维护优先级原型规则 + 单元测试（已通过，可直接运行） | 任意 |
| C7 | Gradio 应用（检测 + 分割 + 严重度 + 延迟 + 免责声明） | CPU/GPU |

## 1. 数据与来源（已完成 2026-08-10）

官方包 `pothole-mix-v1.0-20220526.zip` 已下载并放在仓库的 `Pothole Mix/Pothole-Mix/`，**SHA-256 已与官方 `.sha256sum` 文件核对一致**。来源、许可、六个组成子数据集（crack500 / gaps384 / edmcrack600 / pothole600 / cnr-road-dataset / cracks-and-potholes-in-road）的完整信息都在**压缩包内的 `readme.txt`** 里，C1 的 `M3_PROVENANCE` 已按它填好。只剩一个待办：把 `distribution_page` 里的下载链接换成你浏览器历史里的准确 URL。

许可要点（写报告要用）：作者自有部分 CC BY-NC-SA 4.0；各子数据集保留原许可，其中 GAPs384 仅限学术使用、EdmCrack600 禁止商用。本课程项目属学术用途，均可使用。

**掩码颜色已用真实数据验证**：红 (255,0,0)=坑洞、绿 (0,255,0)=裂缝、黑=背景。按 Part C 的二值坑洞定义，红→类别 1，绿色裂缝→背景（三个纯裂缝子数据集因此充当"困难负样本"，报告里要写明这一点）。crack500 的 EXIF 旋转问题已验证不影响图像-掩码对齐。

**目录整理**：官方结构是 `pothole-mix/{training,validation,testing}/<来源>/{images,masks}`，notebook 新增的「C1 preparation」cell 会自动把它整理成训练用的 `pothole_mix_prepared/{train,val,test}/{images,masks}`，文件名加 `<来源>__` 前缀。本地路径自动识别；上 Colab 时把官方 ZIP 解压到 `My Drive/RDD2022_Project/pothole_mix_official/`，同一个 cell 会在 Drive 上完成同样的整理。

## 2. 运行顺序（分阶段，不要一次 Run All）

Part C 是独立运行时，**不需要**先跑 Part A/B。

### 第 1 阶段：数据校验 + EDA（本地或 Colab CPU 均可，已在本地跑通）

1. 依次运行 C1 的三个 cell（bootstrap → preparation → 来源记录）。本地会自动使用 `Pothole Mix/` 下的数据，确认输出 `Paired Pothole Mix records validated: 4,340`。
2. 运行 C2 全量审计 EDA：配对数、损坏文件、尺寸一致性、掩码颜色清单、空掩码比例、损伤像素占比、连通域、精确/近似重复、跨 split 泄漏，manifest 和 6 张图输出到本地 `artifacts/member3/eda/`（Colab 上则是 `member3_runs/eda/`）。
3. **关键检查——跨 split 重复**：如果 C2 报告 `Duplicate groups crossing splits > 0`，需要在 C2 解读里记录，并决定沿用官方划分（保持可比性）还是把整组移进单一 split。两个模型必须用同一决定。
4. 把 C2 打印的数字填进紧随其后的 `C2 findings and interpretation` markdown（所有 `[FILL AFTER RUN: ...]` 槽位）。

### 第 2 阶段：管线测试 + 正式训练（Colab GPU）

0. 上传数据到 Drive：把官方 ZIP 解压后的 `pothole-mix/` 文件夹（或直接把本地已整理好的 `pothole_mix_prepared/` 重命名为 `pothole_mix`）上传到 `My Drive/RDD2022_Project/`，同时把本地 `artifacts/member3/eda/pothole_mix_manifest.csv` 等 EDA 产物复制到 `member3_runs/eda/`（训练 cell 会读 manifest）。注意：Drive 上的 manifest 路径列指向本地路径时需在 Colab 重跑一次 C1+C2 重新生成（CPU 运行时几分钟即可）。
1. 新建 **GPU** 运行时，运行 C1 三个 cell（`PREPARE_MEMBER3_RUNTIME = True` 挂载 Drive）。
2. 运行"Shared segmentation stack"cell（两模型共用的 Dataset/成对增强/CE+soft-Dice 损失/指标/AMP 训练循环）。
3. 把 `RUN_MEMBER3_TRAINING = True`，保持 `M3_STAGE = "pipeline_test"`，先后运行 C3、C4 的训练 cell。这是每 split 64 张、2 个 epoch 的小规模验证，确认数据流、损失下降、checkpoint 写入正常（几分钟）。
4. 通过后改 `M3_STAGE = "full"`，重新运行 C3、C4（各 40 epoch，512×512，batch 8）。中断后重跑同一 cell 会**自动从 last.pt 续训**。`best.pt` 按验证集 pothole IoU 保存；每个模型的 `history.json` 与 `run_registry.json`（seed、版本、GPU、耗时）会自动写入 `member3_runs/<模型名>/`。
5. Transformers 已固定 `4.46.3`；如果 pipeline_test 阶段 import 或训练报版本问题，换成当时 Colab 可用的稳定版本并更新 `TRANSFORMERS_VERSION`。

### 第 3 阶段：评估（Colab GPU，测试集只许跑一次）

1. 两个模型都完成 full 训练后，把 `RUN_MEMBER3_EVAL = True` 运行 C5。
2. 它会：在**验证集**上做阈值扫描（增益 < 0.005 IoU 就保持 0.5，选择只用验证集）；对测试集**单次**评估；同硬件同设置测延迟/参数量/显存；输出对比表 CSV/Markdown 和 5 张图（训练曲线、混淆矩阵、阈值曲线、逐图 IoU 分布、最好/最差样例四联图）。
3. 把打印的 Markdown 表数字填进 C5 的两个 TODO 表格，并写"赢家"结论——**必须以 pothole IoU/Dice/召回和 Boundary F1 为准，不许用像素准确率定胜负**。

### 第 4 阶段：严重度 + 应用

1. C6 直接运行即可（无需数据/GPU），单元测试已全部通过；阈值已文档化，只用开发数据校准。它是**原型分诊规则，不是工程认证的道路状况评级**——报告里必须保留这句声明。
2. C7：把 `RUN_MEMBER3_APP = True`。`M3_APP_SEG_MODEL` 填 C5 的赢家，`M3_APP_SEG_THRESHOLD` 填 C5 选出的阈值。若 Member 2 的 YOLO `best.pt` 在 Drive 上，会自动叠加检测框；没有也能纯分割运行。按 rubric 要求，各截一张 CPU 和 GPU 运行的界面截图（含延迟显示），存到 `member3_runs/app/`。

## 3. 红线（来自 README 协作规则）

- 两个分割模型必须用**完全相同**的 split、预处理、损失和指标实现（共享 stack 已保证，不要单独改某一个模型的数据管道）。
- 不许在测试集上调参、选阈值、挑 checkpoint。
- 正式训练开始后不许更改 split。
- 不许伪造或预填任何结果数字。
- 所有引用（数据集、论文、官方实现）最终要进 Part D 参考文献。

## 4. 完成核对清单

- [x] 官方 ZIP 已下载，SHA-256 与官方校验文件核对一致（2026-08-10）
- [x] `M3_PROVENANCE` 已按包内 readme.txt 填好（仅剩浏览器下载 URL 待确认）
- [x] 掩码颜色映射已用真实数据验证（红=坑洞→1，绿=裂缝→0）
- [x] C1 输出 `validated: 4,340`（本地已运行，2026-08-10）
- [x] C2 全量审计已完成：0 损坏、0 尺寸不齐、72.7% 空掩码为合法负样本；两阶段去重确认 17 个跨 split 组（含坑洞的仅 2 组 47 张）；**决定沿用官方 3,340/496/504 划分**，理由与局限已写入 C2 解读
- [x] C2 七张图 + manifest 在 `artifacts/member3/eda/`，解读已用真实数字填写
- [ ] C2 六张图 + manifest 在 `member3_runs/eda/`，解读槽位全部填完
- [ ] 两模型 pipeline_test 通过，full 训练完成，`best.pt`/`last.pt`/`history.json`/`run_registry.json` 齐全
- [ ] C5 表格填完，赢家结论基于 pothole IoU/Dice/Boundary F1
- [ ] C6 单测通过输出保留在 notebook 中
- [ ] C7 截图 + 延迟证据存档
- [ ] 提交前清理：删除已解决的 TODO、`[FILL AFTER RUN]` 说明行和本指南类工作流文字（按 README 的 submission cleanup 规则）
