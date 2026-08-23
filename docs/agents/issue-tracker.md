# Issue 追踪：GitHub

本仓库的 issue 与规格以 GitHub issue 形式管理。所有操作统一使用 `gh` CLI。

## 约定

- **创建 issue**：`gh issue create --title "..." --body "..."`。多行正文请使用 heredoc。
- **读取 issue**：`gh issue view <number> --comments`，用 `jq` 过滤评论并同时抓取标签。
- **列出 issue**：`gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`，配合合适的 `--label` 与 `--state` 过滤条件。
- **评论 issue**：`gh issue comment <number> --body "..."`
- **加/去标签**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **关闭**：`gh issue close <number> --comment "..."`

仓库身份从 `git remote -v` 推断 —— 在克隆目录内运行时 `gh` 会自动完成。

## 把 PR 作为 triage 请求来源

**PR 作为请求来源：否。** _（若本仓库把外部 PR 当作功能请求，改为 `yes`；`/triage` 会读取该标记。）_

当该标记为 `yes` 时，PR 与 issue 走相同的标签和状态，使用 `gh pr` 对应命令：

- **读取 PR**：`gh pr view <number> --comments`，查看改动用 `gh pr diff <number>`。
- **列出待 triage 的外部 PR**：`gh pr list --state open --json number,title,body,labels,author,authorAssociation,comments`，然后只保留 `authorAssociation` 为 `CONTRIBUTOR`、`FIRST_TIME_CONTRIBUTOR` 或 `NONE` 的（剔除 `OWNER`/`MEMBER`/`COLLABORATOR`）。
- **评论 / 打标签 / 关闭**：`gh pr comment`、`gh pr edit --add-label`/`--remove-label`、`gh pr close`。

GitHub 的 issue 与 PR 共用一套编号空间，因此裸 `#42` 可能是其中任意一种 —— 先用 `gh pr view 42` 解析，失败再回退到 `gh issue view 42`。

## 当技能说「发布到 issue 追踪器」

创建一个 GitHub issue。

## 当技能说「获取相关 ticket」

运行 `gh issue view <number> --comments`。

## Wayfinding 操作

由 `/wayfinder` 使用。**map** 是一个带子 issue 作为 ticket 的单一 issue。

- **Map**：一个打上 `wayfinder:map` 标签的 issue，承载 Notes / Decisions-so-far / Fog 正文。`gh issue create --label wayfinder:map`。
- **子 ticket**：通过 GitHub 子 issue 与 map 关联的 issue（`gh api` 调用 sub-issues 端点）。未启用子 issue 时，把子项加入 map 正文的任务列表，并在子项正文顶部写 `Part of #<map>`。标签：`wayfinder:<type>`（`research`/`prototype`/`grilling`/`task`）。被认领后，ticket 分配给负责的开发者。
- **阻塞关系**：GitHub **原生 issue 依赖** —— 规范的、UI 可见的表示。用 `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>` 添加边，其中 `<blocker-db-id>` 是阻塞者的数字**数据库 id**（`gh api repos/<owner>/<repo>/issues/<n> --jq .id`，_不是_ `#number` 或 `node_id`）。GitHub 会报告 `issue_dependencies_summary.blocked_by`（仅含打开的阻塞者 —— 实时闸门）。依赖不可用时，回退到在子项正文顶部写一行 `Blocked by: #<n>, #<n>`。当所有阻塞者都被关闭时，ticket 解除阻塞。
- **前沿查询**：列出 map 的打开子项（`gh issue list --state open`，限定到 map 的子 issue / 任务列表），剔除任何带打开阻塞者（`issue_dependencies_summary.blocked_by > 0`，或 `Blocked by` 行中存在打开的 issue）或已有指派人的子项；按 map 顺序取第一个。
- **认领**：`gh issue edit <n> --add-assignee @me` —— 本会话的第一次写入。
- **解决**：`gh issue comment <n> --body "<answer>"`，然后 `gh issue close <n>`，最后把上下文指针（gist + 链接）追加到 map 的 Decisions-so-far。
