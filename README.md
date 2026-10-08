---
license: other
language:
- zh
task_categories:
- image-segmentation
tags:
- medical-imaging
- ct
- nifti
- lumbar-spine
- segmentation
- human-annotation
size_categories:
- n<1K
configs:
- config_name: default
  data_files:
  - split: train
    path: meta/records.jsonl
---

# 腰骶部CT人工分割标注样本1例

本数据集包含 1 例腰骶部 CT，每例提供成对的三维影像与多类别分割掩码，格式均为 `.nii.gz`。标注对象涵盖腰椎、骶骨、髋骨及腰背部肌群；未提供独立病灶类别。

人工标注；标注人员为**三甲医院放射科主任医师级别**。 标注软件：**3D Slicer 5.12.4**。软件名称不代表机器分割模型名称。

## 数据与 meta

- `data/images/<case_id>.nii.gz`：CT 影像。
- `data/labels/<case_id>.nii.gz`：对应掩码，保留原体素值、数据类型、强度缩放和空间信息。
- `meta/records.jsonl`：逐例记录影像与掩码路径、标注来源、人员级别、软件、维度、间距、标签值和文件校验值。
- `meta/dataset.json`：数据集说明、类别对应表及逐例标签体积统计。

公开病例统一使用 `case-0001` 等编号。两组中相同 `case_id` 指向相同影像，`case-0004` 同时具备人工和机器掩码。配套数据：[另一标注组](https://huggingface.co/datasets/SHPDRG/lumbar-ct-segmentation-machine-sample-6)。

## 标签对应

类别名称依据影像及掩码空间位置推定，未取得源标签字典；`meta` 将类别语义标记为 `inferred`。椎体序号按当前扫描范围推定，不包含对解剖变异的确认。左右侧按 NIfTI 空间方向解释。

| 掩码值 | 推定标注对象 |
| --- | --- |
| 0 | 背景 |
| 1 | 第1腰椎（推定L1） |
| 2 | 第2腰椎（推定L2） |
| 3 | 第3腰椎（推定L3） |
| 4 | 第4腰椎（推定L4） |
| 5 | 第5腰椎（推定L5） |
| 6 | 骶骨 |
| 7 | 右侧多裂肌 |
| 8 | 右侧竖脊肌群 |
| 9 | 右侧腰大肌 |
| 10 | 右侧腰方肌 |
| 14 | 左侧腰大肌 |
| 15 | 左侧腰方肌 |
| 16 | 左侧竖脊肌群 |
| 17 | 左侧多裂肌 |
| 18 | 左侧髋骨（扫描范围内） |
| 19 | 右侧髋骨（扫描范围内） |

人工组的右侧多裂肌、竖脊肌群、腰大肌、腰方肌分别使用 7、8、9、10；机器组对应使用 13、12、10、11。比较两组时应按解剖类别对齐，不能直接按同一数值比较。

## 读取

下载数据集文件后，可用 3D Slicer 导入影像与掩码，也可用 NiBabel 读取：

```python
import json
from pathlib import Path
import nibabel as nib

root = Path("数据集本地目录")
record = json.loads((root / "meta/records.jsonl").read_text(encoding="utf-8").splitlines()[0])
image = nib.load(root / record["image_path"])
label = nib.load(root / record["label_path"])
assert image.shape == label.shape
```

## 许可

当前未指定独立许可证；不额外声明 Apache-2.0 或其他授权。
