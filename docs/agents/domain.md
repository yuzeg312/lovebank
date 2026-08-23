# Domain 文档

工程技能在探索代码库时，应如何消费本仓库的领域文档。

## 探索前先读这些

- 仓库根目录的 **`CONTEXT.md`**，或
- 若存在则读仓库根目录的 **`CONTEXT-MAP.md`** —— 它指向每个上下文各一个 `CONTEXT.md`。把与主题相关的每个都读一遍。
- **`docs/adr/`** —— 阅读与你即将处理的区域相关的 ADR。在多上下文仓库中，还要检查 `src/<context>/docs/adr/` 中的上下文级决策。

如果这些文件都不存在，**安静地继续**。不要指出它们缺失，也不要一开始就建议创建它们。`/domain-modeling` 技能（经 `/grill-with-docs` 与 `/improve-codebase-architecture` 触达）会在术语或决策真正被敲定时才惰性创建它们。

## 文件结构

单上下文仓库（大多数仓库）：

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

多上下文仓库（根目录存在 `CONTEXT-MAP.md`）：

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          ← 系统级决策
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  ← 上下文级决策
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## 使用词汇表的术语

当你的输出要命名一个领域概念（issue 标题、重构提案、假设、测试名）时，使用 `CONTEXT.md` 中定义的术语。不要漂移到词汇表明确回避的同义词。

如果你需要的概念不在词汇表里，那是个信号 —— 要么你在发明项目不用的语言（重新考虑），要么存在真实缺口（记下来给 `/domain-modeling`）。

## 标记 ADR 冲突

如果你的输出与既有 ADR 矛盾，请显式地浮出它，而不是默默覆盖：

> _与 ADR-0007（event-sourced orders）冲突 —— 但值得重开，因为…_
