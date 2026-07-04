---
description: 入库:把 Inbox/Clippings 原始材料编译成 Codex Wiki 页
---

执行入库管线(依据 CLAUDE.md 三层四环)。目标不是搬运,是**提炼、重组、联系**。

0. **检查点**:`git add -A && git commit -m "chore: ingest 前检查点"`。
1. **盘点**:列出 `00.Inbox/` 与 `Clippings/` 待处理文件(排除 `_` 开头),给出每份的领域判断与去向建议。
2. **逐份编译**:
   - 按 `Templates/tpl-codex-wiki.md` 在 `30.Resources/<领域>/` 创建或更新页面,frontmatter 必填齐全(`type/title/epistemic_status/created/updated/source/tags`)
   - 提炼核心要点(1-3 句讲清本质)、关键数据/公式/结论、我的关联(与馆主当前工作学习的联系)
   - 重要结论必须写 `source`;不脑补原文没有的内容
3. **建双链**:链接 2-5 个相关已有页(用索引查找确定);更新领域子索引与 `30.Resources/_index.md`。
4. **处置原件**:纯文本剪藏编译后可删(git 已留档);PDF/不可再生原件移入对应 `*原始资料/` 目录并在 manifest 记 SHA-256,永不改写。
5. **留痕**:`30.Resources/_log.md` 顶部追加 `## [YYYY-MM-DD] ingest | 标题` 记录;汇报编译清单(新建/更新/删除)。
