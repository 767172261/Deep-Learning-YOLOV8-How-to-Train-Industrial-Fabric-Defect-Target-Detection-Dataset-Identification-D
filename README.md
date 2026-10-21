![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b1656967c23242dd9221166b4f2a94d9.png)
1
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/7566e1c3d2ed497bb0bdcec8b83ae317.png)
1
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/2c9a900327754a5fbcf888479c5283be.png)
以下是您提供的“工业织物缺陷目标检测数据集”信息整理成的表格，以及训练方法说明。

---

### **工业织物缺陷目标检测数据集（YOLO格式）**

| **项目** | **内容** |
|----------|----------|
| **数据集名称** | 工业织物缺陷检测数据集 |
| **图像总数** | 400 张 |
| **图像分辨率** | 768 × 512 像素 |
| **标注格式** | YOLO `.txt` 格式（每张图像对应一个标签文件） |
| **坐标格式** | 归一化中心坐标：`class_id center_x center_y w h` |
| **任务类型** | 目标检测（多类别缺陷识别） |
| **类别数量** | 4 类 |

---

### **类别信息**

| **类别编号** | **类别名称** | **缺陷说明** | **标注数量** |
|--------------|--------------|----------------|----------------|
| 0 | 洞（Hole） | 织物上的破洞或穿孔 | 135 个实例 |
| 1 | 异物（Foreign Object） | 织物中混入的杂质或非织物材料 | 124 个实例 |
| 2 | 油斑（Oil Stain） | 油脂污染形成的斑点 | 121 个实例 |
| 3 | 线程错误（Thread Error） | 缝线错位、跳线、断线等 | 113 个实例 |

> ✅ **总标注实例数**：135 + 124 + 121 + 113 = **493 个缺陷实例**

---

### **数据集特点**

- ✅ **高分辨率图像**：768×512，适合检测小尺寸缺陷  
- ✅ **真实工业场景**：适用于纺织、质检等自动化检测场景  
- ✅ **YOLO格式**：可直接用于 YOLOv5、YOLOv8 等主流模型训练  
- ✅ **类别均衡性较好**：各类缺陷数量接近，无严重类别不平衡  

---

## **如何训练该数据集？**

以下是使用 **YOLOv8**（推荐）进行训练的完整步骤：

---

### ✅ **1. 环境准备**

```bash
# 安装依赖（需已安装 Python 3.8+ 和 PyTorch）
pip install ultralytics
```

> 官方文档：[https://docs.ultralytics.com](https://docs.ultralytics.com)

---

### ✅ **2. 准备数据结构**

```
fabric_defect_dataset/
│
├── images/
│   ├── train/
│   └── val/
│
├── labels/
│   ├── train/
│   └── val/
│
└── data.yaml
```

---

### ✅ **3. 创建 `data.yaml` 文件**

```yaml
# data.yaml
train: ./images/train
val: ./labels/val

nc: 4
names: ['Hole', 'Foreign Object', 'Oil Stain', 'Thread Error']
```

---

### ✅ **4. 开始训练**

```bash
# 使用 YOLOv8n 模型进行训练
yolo train \
    model=yolov8n.pt \
    data=data.yaml \
    epochs=100 \
    imgsz=768 \
    batch=16 \
    name=fabric_defect_yolov8
```

> 📌 **参数说明**：
> - `epochs=100`：训练100轮
> - `imgsz=768`：输入尺寸与原图分辨率匹配（768×512 → 自动填充为 768×768）
> - `batch=16`：根据GPU显存调整（8~32）
> - `model=yolov8n.pt`：可换为 `yolov8s.pt` 提升精度

---

### ✅ **5. 验证与推理**

```bash
# 验证模型
yolo val model=runs/detect/fabric_defect_yolov8/weights/best.pt data=data.yaml

# 推理单张图像
yolo predict model=runs/detect/fabric_defect_yolov8/weights/best.pt source=test.jpg
```

---

### ✅ **6. 训练技巧（提升效果）**

| **建议** | **说明** |
|--------|----------|
| **数据增强** | YOLO 默认启用 Mosaic、mixup、旋转、翻转等，可提升小数据集泛化能力 |
| **调整输入尺寸** | `imgsz=512` 或 `768`，根据缺陷大小选择 |
| **使用预训练权重** | `yolov8n.pt` 能显著提升小样本训练效果 |
| **早停机制** | 默认启用，防止过拟合 |

---


