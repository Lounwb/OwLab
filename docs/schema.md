# 实验数据Schema定义

## 1. 实验数据格式

实验数据采用JSON数组格式，每个元素代表一个方法的实验结果。

```json
[
    {
        "method": "<method_name>",
        "<dataset_name_1>": {
            "measure": "<measure_name>",
            "<metric_name_1>": 21.46,
            "<metric_name_2>": 95.08,
            "<metric_name_...>": 98.67
        },
        "<dataset_name_2>": {
            "measure": "<measure_name>",
            "<metric_name_1>": 25.41,
            "<metric_name_2>": 94.46,
            "<metric_name_...>": 98.60
        },
        "<dataset_name_...>": {
            "measure": "<measure_name>",
            "<metric_name_1>": 34.76,
            "<metric_name_2>": 91.38,
            "<metric_name_...>": 97.65
        },
        "Average": {
            "measure": "<measure_name>",
            "<metric_name_1>": 31.832,
            "<metric_name_2>": 92.505,
            "<metric_name_...>": 98.312
        }
    },
    ...
]
```

### 字段说明

- **method** (string, required): 方法名称，唯一标识一个实验方法
- **\<dataset_name\>** (object, required): 数据集结果对象，键名为数据集名称
  - **measure** (string, required): 测量方法名称（如 "MCM", "GL-MCM"）
  - **\<metric_name\>** (number, required): 指标值，键名为指标名称
- **Average** (object, optional): 所有数据集的平均值结果
  - **measure** (string, required): 测量方法名称
  - **\<metric_name\>** (number, required): 各指标的平均值

---

## 2. 实验配置Schema

实验配置用于定义实验的结构化信息，包括方法、数据集、指标、测量方法等。

```json
{
    "version": "1.0",
    "experiment_name": "<experiment_name>",
    "description": "<experiment_description>",
    
    "methods": [
        {
            "name": "<method_name>",
            "display_name": "<method_display_name>",
            "description": "<method_description>",
            "category": "<method_category>",
            "metadata": {
                "author": "<author_name>",
                "paper": "<paper_url>",
                "code": "<code_url>"
            }
        }
    ],
    
    "datasets": [
        {
            "name": "<dataset_name>",
            "display_name": "<dataset_display_name>",
            "description": "<dataset_description>",
            "order": 1,
            "metadata": {
                "url": "<dataset_url>",
                "size": "<dataset_size>",
                "type": "<dataset_type>"
            }
        }
    ],
    
    "metrics": [
        {
            "name": "<metric_name>",
            "display_name": "<metric_display_name>",
            "description": "<metric_description>",
            "unit": "<metric_unit>",
            "direction": "min|max",
            "format": "float|int|percentage",
            "decimal_places": 3,
            "order": 1
        }
    ],
    
    "measures": [
        {
            "name": "<measure_name>",
            "display_name": "<measure_display_name>",
            "description": "<measure_description>",
            "order": 1
        }
    ],
    
    "experiment_params": {
        "model": "<model_name>",
        "epochs": 100,
        "learning_rate": 0.01,
        "batch_size": 32,
        "optimizer": "<optimizer_name>",
        "seed": 42,
        "device": "cuda|cpu",
        "other": {}
    },
    
    "visualization": {
        "highlight_best": true,
        "color_scheme": "default",
        "show_average": true,
        "sort_by": "<metric_name>"
    }
}
```

### 配置字段说明

#### 2.1 methods (array, required)
定义实验中使用的方法列表。

- **name** (string, required): 方法唯一标识符，需与实验数据中的method字段一致
- **display_name** (string, optional): 方法显示名称，用于报告和可视化
- **description** (string, optional): 方法描述
- **category** (string, optional): 方法分类（如 "baseline", "proposed", "ablation", "debug"）
- **metadata** (object, optional): 方法元数据
  - **author**: 作者名称
  - **paper**: 论文链接
  - **code**: 代码链接

#### 2.2 datasets (array, required)
定义实验中使用的数据集列表。

- **name** (string, required): 数据集唯一标识符，需与实验数据中的键名一致
- **display_name** (string, optional): 数据集显示名称
- **description** (string, optional): 数据集描述
- **order** (integer, optional): 数据集在报告中的显示顺序
- **metadata** (object, optional): 数据集元数据
  - **url**: 数据集链接
  - **size**: 数据集大小
  - **type**: 数据集类型

#### 2.3 metrics (array, required)
定义实验评估指标列表。

- **name** (string, required): 指标唯一标识符，需与实验数据中的键名一致
- **display_name** (string, optional): 指标显示名称（如 "FPR@95 (↓)"）
- **description** (string, optional): 指标描述
- **unit** (string, optional): 指标单位
- **direction** (string, required): 优化方向，"min"表示越小越好，"max"表示越大越好
- **format** (string, optional): 数值格式，"float"、"int"或"percentage"
- **decimal_places** (integer, optional): 小数位数，默认3
- **order** (integer, optional): 指标在报告中的显示顺序

#### 2.4 measures (array, required)
定义测量方法列表（如MCM、GL-MCM等）。

- **name** (string, required): 测量方法唯一标识符，需与实验数据中的measure字段一致
- **display_name** (string, optional): 测量方法显示名称
- **description** (string, optional): 测量方法描述
- **order** (integer, optional): 测量方法在报告中的显示顺序

#### 2.5 experiment_params (object, optional)
定义实验超参数和配置。

- **model** (string, optional): 模型名称
- **epochs** (integer, optional): 训练轮数
- **learning_rate** (float, optional): 学习率
- **batch_size** (integer, optional): 批次大小
- **optimizer** (string, optional): 优化器名称
- **seed** (integer, optional): 随机种子
- **device** (string, optional): 设备类型
- **other** (object, optional): 其他自定义参数

#### 2.6 visualization (object, optional)
定义可视化选项。

- **highlight_best** (boolean, optional): 是否高亮最佳值，默认true
- **color_scheme** (string, optional): 颜色方案，默认"default"
- **show_average** (boolean, optional): 是否显示平均值，默认true
- **sort_by** (string, optional): 排序依据的指标名称

---

## 3. 配置示例

```json
{
    "version": "1.0",
    "experiment_name": "OOD Detection Benchmark",
    "description": "Out-of-distribution detection experiments",
    
    "methods": [
        {
            "name": "LoCoOp(1)",
            "display_name": "LoCoOp Run 1",
            "category": "baseline"
        },
        {
            "name": "SCT(1)",
            "display_name": "SCT Run 1",
            "category": "baseline"
        },
        {
            "name": "OURS(1)",
            "display_name": "Our Method Run 1",
            "category": "proposed"
        }
    ],
    
    "datasets": [
        {
            "name": "iNaturalist",
            "display_name": "iNaturalist",
            "order": 1
        },
        {
            "name": "SUN",
            "display_name": "SUN",
            "order": 2
        },
        {
            "name": "place365",
            "display_name": "place365",
            "order": 3
        },
        {
            "name": "Texture",
            "display_name": "Texture",
            "order": 4
        }
    ],
    
    "metrics": [
        {
            "name": "fpr95",
            "display_name": "FPR@95 (↓)",
            "direction": "min",
            "format": "float",
            "decimal_places": 2,
            "order": 1
        },
        {
            "name": "auroc",
            "display_name": "AUROC (↑)",
            "direction": "max",
            "format": "float",
            "decimal_places": 2,
            "order": 2
        },
        {
            "name": "aurpc",
            "display_name": "AURPC (↑)",
            "direction": "max",
            "format": "float",
            "decimal_places": 2,
            "order": 3
        }
    ],
    
    "measures": [
        {
            "name": "MCM",
            "display_name": "MCM",
            "order": 1
        },
        {
            "name": "GL-MCM",
            "display_name": "GL-MCM",
            "order": 2
        }
    ],
    
    "experiment_params": {
        "model": "ResNet50",
        "epochs": 100,
        "learning_rate": 0.01,
        "batch_size": 32
    },
    
    "visualization": {
        "highlight_best": true,
        "show_average": true
    }
}
```