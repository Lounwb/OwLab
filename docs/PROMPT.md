我现在要开发一个基于swanlab + 飞书的Python工具库，名字叫OwLab用于实验配置、追踪、管理以及消息通知和实验数据管理, 设计的目的是管理机器学习实验全生命周期。
我已经在./docs/schema.md中定义了部分数据结构schema。

项目的主要功能：
<ol>
    <li>飞书机器人消息通知和实验数据管理
        <ol>
            <li>飞书机器人包括webhook bot和api bot.</li>
            <li>webhook bot负责实验开始和实验结束的消息通知(消息通知中需要包含实验名称、描述、配置和实验结果以及swanlab的实验地址、实验数据保存的飞书文件夹地址)需要提供webhook_url和signature配置</li>
            <li>api bot负责将实验结果写入到飞书表格中, 需要提供app_id, app_secret和root_folder_token配置. 实验结果写入到飞书表格时需要最好的结果加粗以及合并单元格(一个dataset下面的多个metric需要对多个单元格合并, 并且将该dataset下的metric下面的最好的method结果bold)</li>
            <li>实验数据飞书表格存储需要做好版本控制和实验标注, 实验文件夹和文件名都需要能见名知义, 还需要在根文件夹下新建不同的文件以标注实验类型. </li>
        </ol>
    </li>
    <li>swanlab实验配置、追踪和管理
        <ol>
            <li>在原来swanlab的功能基础上拓展, 需要保证对外的使用体验和原来的swanlab一样.</li>
            <li>swanlab需要可选的api_key配置, 如果不提供则默认使用系统的swanlab账户</li>
            <li>有的代码原来使用的tensorboard等, 需要基于swanlab对这些进行支持</li>
        </ol>
    </li>
    <li>本地日志和模型存储管理
        <ol>
            <li>日志本地+云端存储(swanlab云端日志自动存储)</li>
            <li>实验数据csv本地+云端存储(飞书表格云端存储)</li>
        </ol>
    </li>
</ol>

### 技术栈
python>=3.9, loguru(负责日志记录, 不要使用print), swanlab, requests, ...

### 项目管理
uv, flake, isort, ...

### 编程规范
Google开源项目规范, 文档清晰, 注视英文, google docstring

### 打包上传
后续我打算上传到pypi上进行包管理

请你先完成下面任务:
1. 在./docs下完成需求分析、设计文档
2. 使用uv搭建好项目环境, 配置好flake, isort等服从开源项目规范
3. 为整体项目搭建好框架