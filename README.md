# YOLOv8-P2-BiFPN

本项目基于 Ultralytics YOLOv8 进行结构改进，面向小目标检测和多尺度目标检测场景新增 `YOLOv8-P2-BiFPN` 模型配置。

当前模型保留 YOLOv8 的主体训练、验证、推理和导出流程，主要改动集中在检测网络结构上：

- 保留并启用 `P2/4` 小目标检测分支，使检测输出由原始 `P3/8、P4/16、P5/32` 扩展为 `P2/4、P3/8、P4/16、P5/32`。
- 将检测头中的普通 `Concat` 特征拼接替换为 `BiFPNConcat`，在多尺度融合前引入可学习的归一化权重。
- 保持 YOLOv8 默认 `Detect` 检测头、训练流程和损失函数，便于和官方 YOLOv8 基线做对比实验。

## 主要文件

```text
ultralytics/cfg/models/v8/yolov8-p2-bifpn.yaml   # 新增模型配置
ultralytics/nn/modules/conv.py                   # 新增 BiFPNConcat 模块
ultralytics/nn/modules/__init__.py               # 导出 BiFPNConcat
ultralytics/nn/tasks.py                          # 支持解析 BiFPNConcat
```

## 模型结构

新增配置文件为：

```text
ultralytics/cfg/models/v8/yolov8-p2-bifpn.yaml
```

可直接通过不同 scale 调用：

```text
yolov8n-p2-bifpn.yaml
yolov8s-p2-bifpn.yaml
yolov8m-p2-bifpn.yaml
yolov8l-p2-bifpn.yaml
yolov8x-p2-bifpn.yaml
```

## 环境安装

建议使用可编辑安装，确保本地修改生效：

```bash
pip install -e .
```

安装完成后可进行一次快速构建检查：

```bash
yolo detect train model=yolov8n-p2-bifpn.yaml data=coco8.yaml epochs=1 imgsz=320 device=cpu
```

## 训练

示例命令：

```bash
yolo detect train model=yolov8s-p2-bifpn.yaml data=data.yaml epochs=100 imgsz=640 batch=16 device=0
```

如需使用自己的数据集，将 `data=data.yaml` 替换为对应的数据集配置文件。

## 验证

```bash
yolo detect val model=runs/detect/train/weights/best.pt data=data.yaml imgsz=640 device=0
```

## 推理

```bash
yolo detect predict model=runs/detect/train/weights/best.pt source=path/to/images_or_video imgsz=640 device=0
```

## 本地构建结果

在 `imgsz=640` 下，本地模型构建结果如下：

| 模型 | 层数 | 参数量 | GFLOPs |
| --- | ---: | ---: | ---: |
| YOLOv8n-P2-BiFPN | 161 | 3.35M | 17.4 |
| YOLOv8s-P2-BiFPN | 161 | 10.88M | 39.7 |

## 说明

本项目适合用于小目标检测、多尺度目标检测以及与 YOLOv8 官方基线的对比实验。代码基于 Ultralytics YOLOv8 生态，使用方式保持与官方命令行和 Python API 兼容。

许可证遵循原项目许可证要求。
