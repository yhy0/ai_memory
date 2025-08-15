# AI 项目持久化记忆系统

本目录存放 AI（如 Cursor）用于保持项目上下文的“持久层记忆”文件，解决 AI 在多项目、多轮对话中丢失上下文的问题。


## 文件结构
ai_memory/
│
├── PROJECT_MEMORY.json   # 项目的长期记忆（持久层）
└── README.md             # 使用说明


## PROJECT_MEMORY.json 说明

该文件包含四大部分：

| 字段              | 类型       | 说明 |
|-------------------|------------|------|
| `arch`            | object     | 项目架构信息（前端/后端/数据库等） |
| `core_modules`    | array      | 核心功能模块列表 |
| `coding_conventions` | object | 编码规范（前端、后端等） |
| `recent_changes`  | array      | 最近 10 条变更记录 |

示例：
```json
{
  "arch": {
    "frontend": "Vue 3 + Pinia",
    "backend": "FastAPI",
    "database": "PostgreSQL"
  },
  "core_modules": [
    "用户认证（JWT）",
    "文件上传（S3 兼容，支持分块上传）"
  ],
  "coding_conventions": {
    "frontend": "使用 Composition API，所有 API 请求封装在 api/ 目录",
    "backend": "模块化路由，依赖注入，所有模型放在 models/ 目录"
  },
  "recent_changes": [
    {
      "date": "2025-08-12",
      "change": "新增 upload_service.py 支持分块上传"
    }
  ]
}
````

---

## 使用流程

1. **初始化**

   * 手动创建 `PROJECT_MEMORY.json`，填入项目当前架构、核心模块和编码规范。
   * 将该文件提交到版本控制（如 Git）。

2. **日常使用**

   * 每次与 AI（Cursor）交互时，先读取 `PROJECT_MEMORY.json` 作为上下文。
   * 修改代码后，AI 必须更新 `recent_changes`，并保持文件格式正确。

3. **压缩历史**

   * 当 `recent_changes` 超过 10 条时，将较旧的记录合并为“历史版本摘要”，放在一个新的字段（如 `history`）中。

4. **跨项目切换**

   * 每个项目独立维护一个 `PROJECT_MEMORY.json`。
   * 切换项目时，只需更新 Cursor 全局规则里的项目路径。

---

## 自动摘要规则

* 每条变更记录格式：

```json
{
  "date": "YYYY-MM-DD",
  "change": "一句简洁变更描述"
}
```

* 变更描述以动词开头（新增、修改、删除、修复、优化）。
* 避免记录无意义变更（如格式化代码）。

---

## 优点

* 跨项目长期保持上下文记忆
* 结构化存储，易读易维护
* 变更历史可回溯（结合 Git）
* 可与任何 AI 代码助手兼容
