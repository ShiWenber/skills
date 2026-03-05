---
name: uv-python
description: "使用 uv 进行 Python 项目开发与管理。TRIGGER when: 用户提到 'uv'、'安装包'、'运行脚本'、'添加依赖'、'运行测试'、'创建虚拟环境' 或任何与 Python 包管理相关的操作。DO NOT TRIGGER when: 用户明确要求使用 pip、conda 或其他包管理器。"
---

# 使用 uv 进行 Python 项目开发与管理

本技能强制使用 `uv` 作为本项目的唯一 Python 包管理器和运行环境。

## 核心原则

**严格禁止**使用以下工具：
- `pip`
- `conda`
- `poetry`
- `python -m venv`（手动创建虚拟环境）

**必须使用**：
- `uv` — 包管理、依赖安装、虚拟环境管理
- `uv run` — 执行 Python 脚本
- `uvx` — 运行 CLI 工具（不污染项目依赖）

---

## 1. 项目初始化与环境同步

### 新项目创建
```bash
uv init
```
这会创建标准的 `pyproject.toml` 结构。

### 恢复现有项目
```bash
uv sync
```
- 读取 `uv.lock` 并极速还原完全一致的虚拟环境
- **不需要**手动执行 `source .venv/bin/activate` 或任何环境激活命令
- `uv sync` 会自动处理所有环境设置

---

## 2. 依赖管理

### 添加常规依赖
```bash
uv add <包名>
```
示例：
```bash
uv add requests
uv add django
```

### 添加开发/测试依赖
```bash
uv add --group dev <包名>
```
示例：
```bash
uv add --group dev pytest
uv add --group dev black ruff
```

### 依赖移除
```bash
uv remove <包名>
```

### 依赖升级
```bash
uv add --upgrade <包名>
```

**严格禁令**：绝对禁止手动编辑 `uv.lock` 文件。禁止手动编辑`pyproject.toml` 文件。所有依赖状态变更必须通过 `uv` 命令行完成。

---

## 3. 代码执行与运行

### 运行 Python 脚本
**禁止**直接使用：
```bash
python script.py  # 错误！
```

**必须**使用：
```bash
uv run script.py
```

这确保代码在正确的、含有已声明依赖的虚拟环境中执行。

### 示例场景
- 运行训练脚本：`uv run train.py`
- 执行评估：`uv run evaluate.py`
- 启动服务器：`uv run server.py`

---

## 4. 工具调用与零摩擦运行 (uvx)

当需要运行代码检查、格式化工具或挂载基于 Python 的服务时，**优先使用** `uvx`（即 `uv tool run`），避免污染项目依赖。

### 常用示例
```bash
# 代码检查
uvx ruff check .
uvx ruff format .

# 测试运行
uvx pytest .

# 格式化
uvx black .
uvx isort .

# 启动本地 MCP 服务器
uvx mcp-server
```

---

## 5. 强制工作流

当用户输入以下**模糊指令**时，必须生成以 `uv` 或 `uvx` 开头的命令：

| 用户输入 | 正确命令 |
|---------|---------|
| "安装包" | `uv add <包名>` |
| "跑一下这个脚本" | `uv run <script.py>` |
| "运行测试" | `uvx pytest` |
| "添加依赖" | `uv add <包名>` |
| "恢复环境" | `uv sync` |
| "代码检查" | `uvx ruff check .` |

### 缺失 pyproject.toml 时
如果检测到当前目录缺少 `pyproject.toml`，请**主动询问**用户是否需要执行 `uv init`。

---

## 6. 快速命令参考

| 操作 | 命令 |
|-----|------|
| 初始化项目 | `uv init` |
| 同步环境 | `uv sync` |
| 添加依赖 | `uv add <package>` |
| 添加开发依赖 | `uv add --group dev <package>` |
| 移除依赖 | `uv remove <package>` |
| 运行脚本 | `uv run <script.py>` |
| 运行 CLI 工具 | `uvx <tool>` |
| 查看已安装包 | `uv pip list` |
| 更新依赖 | `uv add --upgrade <package>` |
