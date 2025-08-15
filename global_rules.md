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
- 当对代码进行修改、增加或删除时，提炼精确描述，并更新 PROJECT_MEMORY.json 中的 recent_changes。
- 添加一条包含日期和简要变动说明的记录。
- 如果修改涉及架构或编码规范的变化，必须同步更新对应字段。
- 更新时保持 JSON 格式正确，并保留已有信息。

### 自动摘要模板（必须严格遵守）
```json
{
  "date": "YYYY-MM-DD",
  "change": "一句简明扼要的变更描述（不超过 20 字）"
}
````

**生成规则**：

1. 仅记录对架构或功能有意义的改动。
2. 使用动词开头（如 新增、修改、删除、修复、优化）。
3. 保持语言一致（全部中文或全部英文）。
4. 不记录无意义改动（如 “格式化代码”、“调整缩进”）。

## Step 4：压缩历史

* 当 recent_changes 超过 10 条时，合并为“历史版本摘要”，仅保留重要里程碑。

## Step 5：分离历史变更存储

- 最近的 10 条变更保存在 PROJECT_MEMORY.json 的 recent_changes 字段。
- 超出 10 条的旧变更，需移动到单独的 CHANGE_HISTORY.json 文件中追加保存。
- 每次任务结束后，检查 recent_changes 条数：
   - 如果超过 10 条，删除最早的若干条，并将其内容追加到 CHANGE_HISTORY.json。
- CHANGE_HISTORY.json 保持为数组格式，顺序记录所有过往变更。
- 读写两个文件时确保格式正确，避免数据丢失。

