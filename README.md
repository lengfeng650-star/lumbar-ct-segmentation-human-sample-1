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

本数据集包含 1 例腰骶部 CT 及对应分割掩码，格式为 NIfTI（`.nii.gz`）。每个掩码含 16 个非零类别，另以 0 表示背景。标注范围为腰椎、骶骨、髋骨和腰背部肌群；未提供独立病灶类别。

标注方式：人工标注。标注人员为**三甲医院放射科主任医师级别**。 标注软件为 **3D Slicer 5.12.4**。

## 数据与 meta

- `data/images/<case_id>.nii.gz`：CT 影像。
- `data/labels/<case_id>.nii.gz`：分割掩码。
- `meta/records.jsonl`：逐例索引，包含文件路径、标注方式、人员级别、软件版本、影像尺寸、体素间距、标签值和 SHA-256。
- `meta/dataset.json`：数据集描述、脱敏方式、类别对应表及逐例标签体积统计。

病例采用 `case-0001` 等公开编号。NIfTI 自由文本字段为空，无扩展字段；gzip 头不含原始文件名或压缩时间。影像及掩码的体素值、数据类型、强度缩放和空间参数均保持原值。

`case-0004` 在人工组和机器组使用同一份影像，两份掩码分别保留。配套数据：[另一标注组](https://huggingface.co/datasets/SHPDRG/lumbar-ct-segmentation-machine-sample-6)。

## 标签对应

下表的类别名称依据影像及掩码位置推定，在 `meta` 中标记为 `inferred`。椎体序号未依据全脊柱影像确认，左右侧按 NIfTI 空间方向解释。

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

人工组的右侧多裂肌、竖脊肌群、腰大肌、腰方肌分别使用 7、8、9、10；机器组对应使用 13、12、10、11。两组比较时按类别名称对应。

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

独立许可证暂未指定。
