# OwLab 设计文档

## 1. 架构设计

### 1.1 整体架构

```
┌─────────────────────────────────────────────────────────┐
│                      OwLab Core                         │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │
│  │   Config     │  │   Logger     │  │   Storage    │   │
│  │   Manager    │  │   Manager    │  │   Manager    │   │
│  └──────────────┘  └──────────────┘  └──────────────┘   │
└─────────────────────────────────────────────────────────┘
         │                    │                    │
         ▼                    ▼                    ▼
┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│   Lark Bot   │    │  SwanLab     │    │   Local      │
│   Manager    │    │  Tracker     │    │   Storage    │
└──────────────┘    └──────────────┘    └──────────────┘
         │                    │                    │
         ▼                    ▼                    ▼
┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│ Webhook Bot  │    │   SwanLab    │    │   File       │
│  API Bot     │    │   Cloud API  │    │   System     │
└──────────────┘    └──────────────┘    └──────────────┘
```

### 1.2 模块划分

#### 1.2.1 核心模块 (owlab/core)

- `config.py`: 配置管理
- `experiment.py`: 实验管理核心类
- `logger.py`: 日志管理封装
- `storage.py`: 存储管理抽象接口

#### 1.2.2 飞书模块 (owlab/lark)

- `webhook_bot.py`: Webhook Bot 实现
- `api_bot.py`: API Bot 实现
- `sheet_manager.py`: 飞书表格管理
- `folder_manager.py`: 飞书文件夹管理
- `client.py`: 飞书 API 客户端封装

#### 1.2.3 SwanLab 模块 (owlab/swanlab)

- `tracker.py`: SwanLab 追踪器封装
- `adapter.py`: TensorBoard 等适配器
- `client.py`: SwanLab API 客户端

#### 1.2.4 存储模块 (owlab/storage)

- `local_storage.py`: 本地存储实现
- `csv_manager.py`: CSV 文件管理
- `model_manager.py`: 模型文件管理

#### 1.2.5 工具模块 (owlab/utils)

- `schema_validator.py`: Schema 验证
- `formatter.py`: 数据格式化
- `version_manager.py`: 版本管理

## 2. 类设计

### 2.1 OwLab 核心类

```python
class OwLab:
    """Main entry point for OwLab experiment management."""
    
    def __init__(self, config: Optional[Dict] = None):
        """Initialize OwLab with configuration."""
        
    def init(
        self,
        experiment_name: str,
        description: str = "",
        config: Optional[Dict] = None,
        **kwargs
    ) -> None:
        """Initialize a new experiment."""
        
    def log(self, metrics: Dict[str, Any], step: Optional[int] = None) -> None:
        """Log metrics to SwanLab and local storage."""
        
    def finish(self, results: Optional[Dict] = None) -> None:
        """Finish experiment and generate reports."""
```

### 2.2 LarkWebhookBot 类

```python
class LarkWebhookBot:
    """Lark Webhook Bot for sending notifications."""
    
    def __init__(self, webhook_url: str, signature: str):
        """Initialize webhook bot."""
        
    def send_start_notification(
        self,
        experiment_name: str,
        description: str,
        config: Dict,
        swanlab_url: str,
        folder_url: str
    ) -> bool:
        """Send experiment start notification."""
        
    def send_finish_notification(
        self,
        experiment_name: str,
        results: Dict,
        swanlab_url: str,
        folder_url: str
    ) -> bool:
        """Send experiment finish notification."""
```

### 2.3 LarkAPIBot 类

```python
class LarkAPIBot:
    """Lark API Bot for managing sheets and folders."""
    
    def __init__(self, app_id: str, app_secret: str, root_folder_token: str):
        """Initialize API bot."""
        
    def create_experiment_folder(
        self,
        experiment_name: str,
        experiment_type: str
    ) -> Optional[str]:
        """Create experiment folder in Lark."""
        
    def write_results_to_sheet(
        self,
        folder_token: str,
        experiment_config: Dict,
        experiment_data: List[Dict],
        sheet_name: Optional[str] = None
    ) -> Optional[str]:
        """Write experiment results to Lark sheet."""
```

### 2.4 SwanLabTracker 类

```python
class SwanLabTracker:
    """SwanLab tracker wrapper."""
    
    def __init__(self, api_key: Optional[str] = None, **kwargs):
        """Initialize SwanLab tracker."""
        
    def init(
        self,
        experiment_name: str,
        description: str = "",
        config: Optional[Dict] = None
    ) -> None:
        """Initialize SwanLab run."""
        
    def log(self, metrics: Dict[str, Any], step: Optional[int] = None) -> None:
        """Log metrics to SwanLab."""
        
    def finish(self) -> str:
        """Finish SwanLab run and return URL."""
```

## 3. 数据流设计

### 3.1 实验开始流程

```
User calls OwLab.init()
    │
    ├─> Validate config
    ├─> Initialize SwanLabTracker
    ├─> Initialize LarkWebhookBot
    ├─> Initialize LarkAPIBot
    ├─> Create experiment folder in Lark
    ├─> Send start notification
    └─> Initialize local storage
```

### 3.2 实验记录流程

```
User calls OwLab.log(metrics)
    │
    ├─> Validate metrics schema
    ├─> Log to SwanLabTracker
    ├─> Log to local CSV
    └─> Log to local logger
```

### 3.3 实验结束流程

```
User calls OwLab.finish(results)
    │
    ├─> Finish SwanLabTracker (get URL)
    ├─> Format experiment results
    ├─> Write results to Lark sheet
    ├─> Save results to local CSV
    ├─> Send finish notification
    └─> Cleanup resources
```

## 4. 配置设计

### 4.1 配置文件结构

```python
{
    "lark": {
        "webhook": {
            "webhook_url": "https://...",
            "signature": "..."
        },
        "api": {
            "app_id": "...",
            "app_secret": "...",
            "root_folder_token": "..."
        }
    },
    "swanlab": {
        "api_key": "..."  # Optional
    },
    "storage": {
        "local_path": "./experiments",
        "csv_path": "./experiments/csv",
        "model_path": "./experiments/models"
    },
    "logging": {
        "level": "INFO",
        "format": "...",
        "file": "./logs/owlab.log"
    }
}
```

### 4.2 配置加载优先级

1. 环境变量
2. 配置文件（`~/.owlab/config.json` 或 `./owlab_config.json`）
3. 代码中传入的参数
4. 默认值

## 5. 错误处理设计

### 5.1 错误类型定义

```python
class OwLabError(Exception):
    """Base exception for OwLab."""
    pass

class ConfigError(OwLabError):
    """Configuration error."""
    pass

class LarkError(OwLabError):
    """Lark API error."""
    pass

class SwanLabError(OwLabError):
    """SwanLab API error."""
    pass

class StorageError(OwLabError):
    """Storage error."""
    pass
```

### 5.2 重试机制

- 网络请求失败：最多重试 3 次，指数退避
- 飞书 API 限流：等待后重试
- 文件写入失败：记录错误，继续执行

## 6. 日志设计

### 6.1 日志格式

```
{time:YYYY-MM-DD HH:mm:ss} | {level} | {name} | {message}
```

### 6.2 日志文件

- 主日志：`{storage_path}/logs/owlab.log`
- 实验日志：`{storage_path}/experiments/{experiment_name}/log.log`
- 错误日志：`{storage_path}/logs/error.log`

## 7. 测试设计

### 7.1 单元测试

- 每个模块都有对应的测试文件
- 使用 pytest 作为测试框架
- 测试覆盖率目标：> 80%

### 7.2 集成测试

- 测试完整的实验流程
- 使用 Mock 对象模拟外部 API
- 测试错误处理场景

## 8. 部署设计

### 8.1 包结构

```
owlab/
├── __init__.py
├── core/
│   ├── __init__.py
│   ├── config.py
│   ├── experiment.py
│   ├── logger.py
│   └── storage.py
├── lark/
│   ├── __init__.py
│   ├── webhook_bot.py
│   ├── api_bot.py
│   ├── sheet_manager.py
│   ├── folder_manager.py
│   └── client.py
├── swanlab/
│   ├── __init__.py
│   ├── tracker.py
│   ├── adapter.py
│   └── client.py
├── storage/
│   ├── __init__.py
│   ├── local_storage.py
│   ├── csv_manager.py
│   └── model_manager.py
└── utils/
    ├── __init__.py
    ├── schema_validator.py
    ├── formatter.py
    └── version_manager.py
```

### 8.2 安装方式

```bash
# 使用 uv
uv pip install owlab

# 或使用 pip
pip install owlab
```

## 9. 性能优化

### 9.1 批量操作

- 批量写入飞书表格（减少 API 调用）
- 批量写入本地 CSV（减少文件 I/O）

### 9.2 异步操作

- 消息通知异步发送（不阻塞主流程）
- 文件写入异步执行

### 9.3 缓存机制

- 缓存飞书访问令牌
- 缓存 SwanLab 配置

## 10. 安全设计

### 10.1 敏感信息

- API Key、Secret 等敏感信息不记录到日志
- 支持从环境变量读取敏感配置
- 配置文件权限控制

### 10.2 数据验证

- 所有输入数据都进行 Schema 验证
- 防止注入攻击（SQL、命令注入等）
