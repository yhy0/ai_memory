---
description: 
globs:
alwaysApply: true
---

# 项目记忆规则（AI 持久化上下文）

## Step 1：读取项目持久层记忆文件（任务开始前）
- 文件路径：./ai_memory/PROJECT_MEMORY.json
- 理解其中的：
  - 架构 (arch)
  - 核心模块 (core_modules)
  - 编码规范 (coding_conventions)
  - 近期改动 (recent_changes)
- 在本次对话和任务中，必须遵循这些约束和规范。

## Step 2：保持一致性（执行中）
- 所有新增文件和代码必须与 arch 和 coding_conventions 中的描述一致。
- 不得破坏已有的架构风格或模块划分，除非用户明确要求。

## Step 3：更新记忆（任务结束后）

当对代码进行修改、增加或删除时，必须提炼精确描述并更新 PROJECT_MEMORY.json。  
更新分为两个层级：  

### 3.1 短期变动（recent_changes）
- 所有功能性或修复性改动，必须追加到 recent_changes。
- 使用以下模板：

```json
{
  "date": "YYYY-MM-DD",
  "change": "一句简明扼要的变更描述（不超过 20 字）"
}
```

**生成规则**：

1. 仅记录对架构或功能有意义的改动。
2. 使用动词开头（如 新增、修改、删除、修复、优化）。
3. 保持语言一致（全部中文或全部英文）。
4. 不记录无意义改动（如 “格式化代码”、“调整缩进”）。

### 3.2 长期基线（arch / core_modules / coding_conventions）

* 如果改动涉及 **架构、模块、规范的根本性变化**，必须同步更新对应字段。
* 更新时必须保持 JSON 正确，并与实际代码/项目保持一致。

#### 自动判别条件表

当 `recent_changes` 中包含以下关键词或语义时，必须更新 arch/core_modules/coding_conventions：

* **架构层面**

  * “架构调整”、“替换框架”、“新增服务”、“移除服务”、“重构系统”
* **核心模块层面**

  * “新增模块”、“删除模块”、“模块职责变更”、“模块迁移”、“模块拆分/合并”
* **编码规范层面**

  * “修改命名规范”、“调整目录结构”、“更换样式方案”、“统一API管理方式”

#### 自动更新示例

**示例 1：新增模块**

recent_changes 新增：
```json
{
  "date": "2025-08-20",
  "change": "新增日志分析模块"
}
```
core_modules 更新：

```json
{
  "name": "日志分析模块 (backend/logs)",
  "description": "负责收集、解析并分析系统日志，与威胁检测引擎联动，支持攻击溯源和合规审计。"
}
```

**示例 2：架构调整**

recent_changes 新增：
```json
{
  "date": "2025-08-20",
  "change": "架构调整为事件驱动"
}
```

arch 更新：
"backend": "Python 3，基于 FastAPI 框架，后端架构调整为事件驱动模式。通过事件总线统一调度多智能体系统，替代传统请求驱动模式。"


**示例 3：编码规范变化**

recent_changes 新增：
```json
{
  "date": "2025-08-20",
  "change": "统一API调用方式为 services 层封装"
}
```

coding_conventions 更新：
"frontend": "所有 API 调用必须通过 services 目录的统一封装方法完成，禁止直接在组件中调用 axios。"


## Step 4：压缩历史

* 当 recent_changes 超过 10 条时，合并为“历史版本摘要”，仅保留重要里程碑。

## Step 5：分离历史变更存储

* 最近的 10 条变更保存在 PROJECT_MEMORY.json 的 recent_changes 字段。
* 超出 10 条的旧变更，需移动到单独的 CHANGE_HISTORY.json 文件中追加保存。
* 每次任务结束后，检查 recent_changes 条数：
* 如果超过 10 条，删除最早的若干条，并将其内容追加到 CHANGE_HISTORY.json。
* CHANGE_HISTORY.json 保持为数组格式，顺序记录所有过往变更。
* 读写两个文件时确保格式正确，避免数据丢失。

## Step 6：交互规范

* 当用户提出“我的分析是否正确”或类似问题时：

  1. 不得直接回答“正确”或“你说的对”。
  2. 必须逐点验证用户分析，与 arch / core_modules / coding_conventions / recent_changes 对照。
  3. 给出明确的验证结果：

     * ✅ 正确：指出依据。
     * ⚠️ 部分正确：指出错误点并解释。
     * ❌ 不正确：给出反例或证据。
  4. 如果存在不确定性，必须提出澄清问题，而不是默认正确。
  5. 在未确认用户分析前，**不得直接改代码**。

### 回答格式约束

所有回答必须分为三部分输出：

* 【分析验证结果】
* 【修改建议】
* 【代码（如有必要）】
