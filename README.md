# OwLab

OwLab 是一个基于 SwanLab 和飞书的 Python 工具库，用于机器学习实验的全生命周期管理，包括实验配置、追踪、管理以及消息通知和实验数据管理。

## 功能特性

- 🚀 **实验追踪**: 基于 SwanLab 的实验追踪和可视化
- 📊 **数据管理**: 飞书表格自动写入和管理实验结果
- 📢 **消息通知**: 飞书机器人自动发送实验开始和结束通知
- 💾 **本地存储**: 本地日志和 CSV 数据存储
- 🔧 **易于使用**: 简洁的 API，与 SwanLab 使用体验一致

## 安装

### 使用 uv (推荐)

```bash
uv pip install owlab
```

### 使用 pip

```bash
pip install owlab
```

### 从源码安装

```bash
git clone https://github.com/yourusername/owlab.git
cd owlab
uv pip install -e .
```

## 快速开始

### 1. 配置

创建配置文件 `~/.owlab/config.json` 或 `./owlab_config.json`:

```json
{
  "lark": {
    "webhook": {
      "webhook_url": "https://open.feishu.cn/open-apis/bot/v2/hook/...",
      "signature": "your_signature"
    },
    "api": {
      "app_id": "your_app_id",
      "app_secret": "your_app_secret",
      "root_folder_token": "your_root_folder_token"
    }
  },
  "swanlab": {
    "api_key": "your_api_key"
  },
  "storage": {
    "local_path": "./output"
  }
}
```

或者使用环境变量:

```bash
export OWLAB_LARK__WEBHOOK__WEBHOOK_URL="https://..."
export OWLAB_LARK__WEBHOOK__SIGNATURE="your_signature"
export OWLAB_LARK__API__APP_ID="your_app_id"
export OWLAB_LARK__API__APP_SECRET="your_app_secret"
export OWLAB_LARK__API__ROOT_FOLDER_TOKEN="your_root_folder_token"
export OWLAB_SWANLAB__API_KEY="your_api_key"
```

### 2. 基本使用

```python
from owlab import OwLab

# 初始化 OwLab
owlab = OwLab()

# 开始实验
owlab.init(
    project="my_project",  # 项目名称（必选）
    experiment_name="my_experiment",  # 实验名称（可选，默认为项目名称）
    description="This is a test experiment",
    tags=["baseline"],  # 标签（可选，用于分类：baseline/debug/ablation等）
    config={
        "learning_rate": 0.01,
        "batch_size": 32,
        "epochs": 100
    }
)

# 记录指标
for epoch in range(100):
    metrics = {
        "loss": 0.5 - epoch * 0.01,
        "accuracy": 0.5 + epoch * 0.01
    }
    owlab.log(metrics, step=epoch)

# 结束实验
owlab.finish(results={
    "final_loss": 0.1,
    "final_accuracy": 0.95
})
```

## 项目结构

```
owlab/
├── core/           # 核心模块
│   ├── config.py   # 配置管理
│   ├── experiment.py  # 实验管理
│   └── logger.py   # 日志管理
├── lark/           # 飞书集成
│   ├── webhook_bot.py  # Webhook Bot
│   └── api_bot.py  # API Bot
├── swanlab/        # SwanLab 集成
│   └── tracker.py  # 追踪器封装
├── storage/        # 存储模块
│   └── local_storage.py  # 本地存储
└── utils/          # 工具模块
    └── schema_validator.py  # Schema 验证
```

## 开发

### 环境设置

```bash
# 创建虚拟环境
uv venv

# 激活虚拟环境
source .venv/bin/activate  # Linux/macOS
# 或
.venv\Scripts\activate  # Windows

# 安装开发依赖
uv pip install -e ".[dev]"
```

### 代码规范

项目使用以下工具进行代码质量检查:

- **flake8**: 代码风格检查
- **isort**: Import 排序
- **black**: 代码格式化
- **mypy**: 类型检查

运行检查:

```bash
# 代码格式化
black owlab tests

# Import 排序
isort owlab tests

# 代码检查
flake8 owlab tests

# 类型检查
mypy owlab
```

### 测试

```bash
# 运行测试
pytest

# 运行测试并生成覆盖率报告
pytest --cov=owlab --cov-report=html
```

## 文档

详细文档请参考:

- [需求分析文档](docs/requirements.md)
- [设计文档](docs/design.md)
- [Schema 定义](docs/schema.md)

## 贡献

欢迎贡献！请阅读 [CONTRIBUTING.md](CONTRIBUTING.md) 了解详细信息。

## 许可证

本项目采用 MIT 许可证。详见 [LICENSE](LICENSE) 文件。

## 致谢

- [SwanLab](https://swanlab.cn/) - 实验追踪平台
- [飞书开放平台](https://open.feishu.cn/) - 企业协作平台

## 联系方式

如有问题或建议，请提交 [Issue](https://github.com/yourusername/owlab/issues)。
