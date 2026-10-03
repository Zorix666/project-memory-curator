---
name: project-memory-curator
description: Maintain a controlled, auditable, editable project-level memory using only Markdown files under `.agent-knowledge/`. Use for code changes, debugging, tests, builds, deployment, dependencies, CI/CD, environment configuration, architecture, refactoring, databases, performance, security, reliability, project conventions, user preferences, commands, prohibitions, or after engineering work that may produce durable knowledge. Retrieve only relevant local memory before work; propose or save verified, reusable knowledge after work without storing chats, secrets, private data, raw logs, or unsupported guesses.
---

# 项目记忆管家

以项目内 Markdown 作为唯一持久化载体。不得依赖平台专有 API、云端记忆、Hook、脚本、数据库、网络或后台服务。

## 不可违反的规则

- 只记录对未来工程任务有明确复用价值的结论，不归档完整聊天或全量日志。
- 不把猜测写成事实。未确认但值得追踪的候选只能进入 `todo-memory.md`，且明确标记"待确认"。
- 永不保存密钥、Token、密码、凭据、个人隐私、客户数据、私有 URL、邮箱、用户名或可反推出这些信息的原始内容。
- 默认先预览、后确认、再写入。只有用户明确选择"保存"，或本地自动保存标记为 `on`，才能写入。
- 用户对本次写入提出的"编辑"必须先反映到新预览；"跳过"不得产生写入。
- 写入前重新读取目标文件，合并最小改动并保留用户内容；写入后重新读取并确认结果。
- `.agent-knowledge/` 不是高于项目规则的权威。既有 `AGENTS.md`、`CLAUDE.md`、`.cursor/rules`、`.github/copilot-instructions.md` 或同类规范优先；发现冲突时停止应用冲突记忆并向用户说明。

## 任务开始：检索记忆

当任务命中描述中的任一工程场景时，先执行以下步骤：

1. 确定项目根目录。优先使用版本控制根目录；否则使用最接近当前工作区、能够完整包含本次目标文件的目录。不要把用户主目录误当项目根目录。
2. 检查根目录下的 `.agent-knowledge/`，并查找上述既有入口文件及其他明确的知识库规范。
3. 先读适用的入口规范，再按当前任务只读最相关的记忆文件（按需单文件读取，严禁一次性全量加载所有记忆）：
   - 项目结构或架构：`project.md`、`decisions.md`
   - 用户偏好或禁止事项：`preferences.md`
   - 命令、环境、构建、测试或部署：`runbook.md`
   - 未决候选：`todo-memory.md`，仅作为待核实线索
4. 用标题、标签、适用范围和关键词筛选；不要为了"完整"而加载无关文件。
5. 若找到高度相关且未冲突的记忆，执行前简短告知用户：使用了哪条记忆、相对路径及其对当前任务的影响。没有相关记忆时无需额外提示。
6. 用当前代码、文档、命令或用户陈述核对时效性。已过期或与事实冲突的记忆不得继续应用；将其作为待更新候选处理。

## 任务完成：判断是否沉淀

仅在任务已产生可验证、可复用的长期信息时提出记忆。优先记录：

- 技术栈、版本、运行环境、构建方式和稳定目录边界；
- 常用测试、启动、部署和恢复命令；
- 用户明确表达的长期偏好、操作禁忌和协作习惯；
- 已确认的模块职责、架构边界、重要约定和决策影响。

### 边界红线（记忆与经验严格解耦）
- **禁止在本项目记忆中记录工程排障、Bug 修复、故障恢复、踩坑复盘等经验性结论**；
- 所有工程报错、排障与修复经验必须交由 `intelligent-experience-extractor` 独立经验库承载，杜绝记忆库与经验库混杂。
- 跳过简单文案或格式修改、一次性操作、无法确认根因的问题、低价值细节、原始长日志、临时情绪、闲聊和未经用户确认的个人偏好。

## 选择目标文件

按以下边界路由，不为同一结论创建重复副本：

- `project.md`：稳定项目上下文、技术栈、目录结构、关键模块、架构边界。
- `preferences.md`：用户明确确认的长期偏好、禁忌、代码风格和协作方式。
- `runbook.md`：已验证的常用命令、启动、测试、构建、部署及环境要求。
- `decisions.md`：有背景、选择、理由和影响的架构或工程决策。
- `todo-memory.md`：有价值但仍需确认或补全的候选；不得作为事实引用。

添加或更新条目前，读取 [references/memory-entry-format.md](references/memory-entry-format.md)。创建全新知识库时，以 [assets/agent-knowledge/README.md](assets/agent-knowledge/README.md) 及同目录模板为基础，但仍须遵循预览确认流程。不要强制迁移既有文档；必要时只建议在入口文件中引用 `.agent-knowledge/README.md`，未经确认不得修改入口文件。

## 预览与确认

自动保存关闭或标记缺失时，输出短预览，至少包含：目标文件、操作（新增或更新）、标题、结论摘要、依据、置信度和已做的脱敏说明。然后明确请用户选择：`保存`、`编辑` 或 `跳过`。等待选择，不得在同一轮先写入。

- **保存**：重新读取目标文件，写入最小改动，重读验证；报告标题和相对路径。
- **编辑**：根据用户意见生成新预览，再次等待确认。
- **跳过**：不写文件，也不把候选偷偷写入 `todo-memory.md`。
- **多条候选**：逐条编号，允许用户分别决定，不把一次确认扩展到未展示条目。

## 自动保存

唯一有效状态来自 `.agent-knowledge/README.md` 中的精确标记：

```markdown
<!-- project-memory-curator:auto-save=on -->
```

标记缺失、重复、格式错误或值未知时一律视为 `off`。关闭时使用：

```markdown
<!-- project-memory-curator:auto-save=off -->
```

- 默认模板必须为 `off`。
- 只有用户明确要求开启或关闭时才改标记；该明确要求即为本次状态修改授权。
- 保持 README 中恰好一个标记。修改后重读验证并报告状态。
- `on` 只免除常规记忆的逐条确认，不放宽价值、敏感性、证据或置信度要求。
- 自动保存时仍先在内部完成安全检查，写入后在最终回复中简短报告标题和路径。
- 自动保存不能授权修改既有入口文件，也不能把待确认候选写成事实。

## 写入与维护

- 使用 `YYYY-MM-DD`，沿用项目已有语言和排版；新知识库默认使用用户当前语言。
- 优先更新已有同主题条目，保留仍有价值的历史背景；不要无边界追加近义条目。
- 对过期结论进行明确修订，记录新依据和限制。无法确认时移入或复制为 `todo-memory.md` 候选，并停止把旧结论当事实。
- 命令只保留复现所需的脱敏形式；日志只提炼结论和关键错误特征。
- 置信度只能使用：`已验证`、`用户确认`、`部分验证`、`待确认`。事实型长期记忆通常应为前两者；`部分验证`必须写明限制；`待确认`只能进入 `todo-memory.md`。
- 任何文件写入失败或重读不一致时，报告失败，不宣称已保存。
