# 模拟 Cursor CLI SDK

> 🚀 一个轻量级的 Python SDK，用于模拟和测试 Cursor CLI 工具的行为。零依赖，开箱即用！

这是一个模拟 Cursor CLI 工具的 Python SDK，用于在 Python 中调用类似 cursor-agent 的功能。该 SDK 支持非交互式模式、文件修改、输出格式控制等功能，主要用于测试和开发环境。

## ✨ 为什么需要这个项目？

- 🧪 **测试友好**：无需真实的 Cursor API 密钥即可测试你的集成代码
- 🎓 **学习工具**：了解 Cursor CLI 的工作机制和数据结构
- 🔧 **开发辅助**：在开发阶段模拟 API 响应，避免消耗真实 API 配额
- 📦 **零依赖**：仅使用 Python 标准库，无需安装额外包
- ⚡ **快速上手**：几分钟内即可集成到你的项目中

## 🎯 功能特性

- ✅ **完整 CLI 模拟**：模拟 cursor-agent CLI 命令行为，支持所有主要参数
- ✅ **多格式输出**：支持 text、json、stream-json 三种输出格式
- ✅ **流式处理**：支持流式输出和增量更新，模拟真实的 API 响应
- ✅ **批量处理**：支持 glob 模式匹配文件批量处理
- ✅ **双重接口**：提供面向对象和函数式两种 API 风格
- ✅ **错误处理**：完整的错误处理和退出码支持，符合 Unix 标准
- ✅ **命令行工具**：可直接作为命令行工具使用
- ✅ **零配置**：无需安装，无需依赖，即下即用

## 📦 安装

本项目是纯 Python 实现，无需额外依赖（仅使用 Python 标准库）。

### 要求

- Python >= 3.7

### 快速安装

**方式一：直接下载**

```bash
# 克隆或下载项目
git clone https://github.com/yourusername/sim-cursor-agent.git
cd sim-cursor-agent

# 直接使用，无需安装
python sim_sdk.py --help
```

**方式二：作为模块使用**

将 `sim_sdk.py` 复制到你的项目中，然后：

```python
from sim_sdk import cursor_agent, CursorAgent
```

就是这么简单！无需 pip install，无需依赖管理。

## 快速开始

### 1. 设置 API 密钥

```bash
# Windows (PowerShell)
$env:CURSOR_API_KEY="your-api-key-here"

# Linux/Mac
export CURSOR_API_KEY="your-api-key-here"
```

### 2. 作为 Python 模块使用

```python
from sim_sdk import cursor_agent, CursorAgent

# 函数式调用
result = cursor_agent(
    prompt="这个代码库是做什么的？",
    print_mode=True,
    output_format="text"
)
print(result)

# 面向对象调用
agent = CursorAgent(api_key="your-api-key")
result = agent.review("recent changes")
print(result)
```

### 3. 作为命令行工具使用

```bash
# 基本用法
python sim_sdk.py "分析这个项目的结构" --print

# JSON 格式输出
python sim_sdk.py "审查代码质量" --print --output-format json

# 流式输出
python sim_sdk.py "分析项目" --print --output-format stream-json --stream-partial-output

# 从标准输入读取
echo "你的提示语" | python sim_sdk.py --print
```

## API 文档

### 函数式 API

#### `cursor_agent(prompt, print_mode=False, force=False, output_format="text", stream_partial_output=False, api_key=None)`

模拟 cursor-agent CLI 命令的主要函数。

**参数：**
- `prompt` (str): 要发送给代理的提示语（必填）
- `print_mode` (bool): 是否启用打印模式（非交互式），默认为 False
- `force` (bool): 是否强制执行更改，默认为 False
- `output_format` (str): 输出格式，可选 'text'、'json'、'stream-json'，默认为 'text'
- `stream_partial_output` (bool): 是否增量流式输出变更，默认为 False
- `api_key` (str, optional): API 密钥，默认从环境变量 `CURSOR_API_KEY` 读取

**返回：**
- `text` 格式：返回纯文本字符串
- `json` 格式：返回结构化字典
- `stream-json` 格式：应使用 `stream_analysis` 获取流式输出，此处返回空字符串

**示例：**

```python
# 文本输出
result = cursor_agent(
    prompt="分析代码质量",
    print_mode=True,
    output_format="text"
)

# JSON 输出
result = cursor_agent(
    prompt="代码审查",
    print_mode=True,
    output_format="json"
)
```

#### `stream_analysis(prompt, output_file="analysis.txt")`

流式分析项目结构，模拟真正的逐行输出行为。

**参数：**
- `prompt` (str): 分析提示
- `output_file` (str): 输出文件路径，默认为 "analysis.txt"

**返回：**
生成器，每次 yield 一个事件字典对象

**示例：**

```python
for event in stream_analysis("分析项目结构"):
    print(json.dumps(event))
```

#### `process_files_glob(pattern, prompt_template)`

批量处理符合 glob 模式的文件。

**参数：**
- `pattern` (str): 文件路径模式，如 "src/**/*.js"
- `prompt_template` (str): 提示模板，其中 `{file}` 会被替换为文件名

**返回：**
处理结果列表，每个元素是一个字典

**示例：**

```python
results = process_files_glob(
    pattern="src/**/*.js",
    prompt_template="为 {file} 添加 JSDoc 注释"
)
```

#### `stream_process_files_glob(pattern, prompt_template)`

流式批处理符合 glob 模式的文件，支持实时进度跟踪。

**参数：**
- `pattern` (str): 文件路径模式
- `prompt_template` (str): 提示模板

**返回：**
生成器，每次 yield 一个包含处理状态的事件对象

**示例：**

```python
for event in stream_process_files_glob(
    pattern="src/**/*.js",
    prompt_template="优化 {file} 的代码结构"
):
    print(json.dumps(event))
```

### 面向对象 API

#### `CursorAgent` 类

提供面向对象的接口来调用 cursor_agent 功能。

**初始化：**

```python
agent = CursorAgent(api_key="your-api-key")
# 或从环境变量读取
agent = CursorAgent()
```

**方法：**

##### `analyze(prompt, force=False)`

分析代码库或文件。

```python
result = agent.analyze("分析这个项目的架构", force=True)
```

##### `review(target="recent changes")`

执行代码审查。

```python
result = agent.review("recent changes")
```

##### `stream_analysis(output_file="analysis.txt")`

流式分析项目结构。

```python
for event in agent.stream_analysis("report.txt"):
    print(event)
```

##### `stream_batch_process(pattern, instruction)`

流式批处理文件。

```python
for event in agent.stream_batch_process(
    pattern="src/**/*.js",
    instruction="添加类型注解"
):
    print(event)
```

## 命令行参数

```
usage: sim_sdk.py [-h] [-p] [--force] [--output-format {text,json,stream-json}]
                  [--stream-partial-output] [--api-key API_KEY]
                  [prompt]

positional arguments:
  prompt                要执行的提示语

optional arguments:
  -h, --help            显示帮助信息
  -p, --print           启用打印模式
  --force               强制执行更改
  --output-format       输出格式（text/json/stream-json）
  --stream-partial-output  启用部分输出流
  --api-key             显式指定API密钥
```

## 使用示例

### 示例 1：简单查询

```python
from sim_sdk import cursor_agent

result = cursor_agent(
    prompt="这个代码库是做什么的？",
    print_mode=True,
    output_format="text"
)
print(result)
```

### 示例 2：代码审查（JSON 格式）

```python
from sim_sdk import CursorAgent
import json

agent = CursorAgent(api_key="dummy_key")
result = agent.review()
print(json.dumps(result, indent=2, ensure_ascii=False))
```

### 示例 3：流式分析

```python
from sim_sdk import CursorAgent
import json

agent = CursorAgent()
for event in agent.stream_analysis():
    print(json.dumps(event))
```

### 示例 4：批量处理文件

```python
from sim_sdk import stream_process_files_glob
import json

for event in stream_process_files_glob(
    "src/**/*.js",
    "为 {file} 添加全面的 JSDoc 注释"
):
    print(json.dumps(event))
```

### 示例 5：运行示例代码

直接运行 `sim_sdk.py` 而不带参数，将执行内置的示例：

```bash
python sim_sdk.py
```

## 🎯 使用场景

- ✅ **单元测试**：为你的 Cursor 集成代码编写测试用例
- ✅ **CI/CD 测试**：在持续集成中测试代码逻辑，无需真实 API
- ✅ **原型开发**：快速原型开发，验证集成方案
- ✅ **文档演示**：为你的项目创建演示和示例代码
- ✅ **离线开发**：在没有网络或 API 访问时继续开发

## ⚠️ 注意事项

**重要提示：**

1. 这是一个**模拟 SDK**，不会实际调用外部 API 或修改文件系统
2. 所有操作都是模拟的，返回的是预定义的测试数据
3. 主要用于测试、开发和演示目的
4. API 密钥验证是模拟的，不会实际验证密钥有效性

**⚠️ 不要在生产环境使用此 SDK 替代真实的 Cursor API！**

## 退出码

- `0`: 成功执行
- `1`: 错误（如缺少参数、API 密钥未设置等）

## 开发说明

### 项目结构

```
sim_cursor_agent/
├── sim_sdk.py      # 主 SDK 文件
├── README.md       # 本文档
└── LICENSE         # 许可证文件
```

### 修改记录

- **初始版本**：实现了基本的 `cursor_agent` 函数
- **第2轮修改**：添加了批处理、流式输出、命令行支持等功能
- **第3轮修改**：完善了流式输出行为、错误处理、事件ID一致性等

## 许可证

本项目采用 MIT 许可证。详见 [LICENSE](LICENSE) 文件。

## 🤝 贡献

欢迎贡献代码！请遵循以下步骤：

1. Fork 本项目
2. 创建你的特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交你的更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启一个 Pull Request

### 贡献指南

- 保持代码风格一致
- 添加必要的测试和文档
- 确保所有测试通过
- 提交信息清晰明确

## 📝 更新日志

暂无

## 🐛 问题反馈

如果遇到问题或有功能建议，请通过以下方式反馈：

- [提交 Issue](https://github.com/yourusername/sim-cursor-agent/issues)
- [发起讨论](https://github.com/yourusername/sim-cursor-agent/discussions)

## 📄 许可证

本项目采用 MIT 许可证。详见 [LICENSE](LICENSE) 文件。

## 🙏 致谢

- 感谢 Cursor 团队提供的优秀工具
- 感谢所有贡献者的支持

---

如果这个项目对你有帮助，请给个 ⭐ Star 支持一下！

