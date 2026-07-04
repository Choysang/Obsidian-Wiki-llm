# BOOTSTRAP — 从零构建 AI 知识操作系统的提示词

> 用法:新建一个空文件夹作为 Obsidian vault,在其中打开 Claude Code(或任何能读写文件的 AI agent),把下面代码块里的提示词**整段粘贴**发送,然后按 AI 的提问回答你的个人信息即可。约 10 分钟建成与本库同构的系统。
>
> 理论来源:[Karpathy LLM Wiki v1](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) + [rohitg00 LLM Wiki v2](https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2)。方法论讲解见 [[Obsidian-AI知识库构建指南]]。

```text
你是我的知识库建馆师。请在当前空目录中,从零搭建一个「AI 知识操作系统」——
一个由 AI 持续编译维护、人只负责捕捉和提问的第二大脑。严格按以下步骤执行。

【第 0 步:采访馆主】
先问我(逐条问,等我回答):
1. 怎么称呼我?我的职业/学习方向?
2. 当前 1-3 个工作/学习重心?
3. 建库初衷:希望知识库解决什么问题?
4. 偏好语言与输出风格?

【第 1 步:建目录骨架】
00.Inbox/(捕捉入口,整理后清空)、10.Projects/(进行中项目)、
20.Areas/(长期领域)、30.Resources/(AI 编译的 Codex Wiki + 原始资料)、
40.Archive/(归档)、50.Daily/(每日笔记)、Templates/、docs/。
每个分区放一个 _关于此分区.md 说明用途。

【第 2 步:写系统宪法 CLAUDE.md(放根目录)】
包含八节,内容用第 0 步采访结果填充:
一、馆主档案(称呼/身份/重心/初衷)
二、三层四环架构:
   L0 原始层=PDF/剪藏,不可变,带 SHA-256,AI 永不改写;
   L1 编译层=wiki 页+项目+daily,AI 维护;
   L2 系统层=CLAUDE.md+memory.md+命令+git。
   四环=捕捉(人)→入库/ingest→调用/ask /daily→进化/crystallize /lint /review
三、检索协议(核心!):①查 30.Resources/_index.md 索引定位 →②读原文不靠记忆
   →③综合输出并标注 [[来源]] →④有价值新合成存回库 →⑤没有就明说「知识空白」不脑补
四、写入规范:wiki 页 frontmatter 必填 type/title/epistemic_status(fact|
   verified_inference|hypothesis|decision|superseded)/created/updated/source/tags;
   重要结论必须有来源;新旧冲突标 #codex/conflict 不自动裁决;
   替代用 superseded_by 链接,旧页不删;每次库级操作在 30.Resources/_log.md 顶部留痕
五、命令表(指向 .claude/commands/,见第 4 步)
六、记忆协议:memory.md 是宪法的动态补丁,冲突时 memory 优先;
   启动读取/检索查询/输出前校验/发现新偏好即写入
七、红线:只用馆主亲口提供的信息;批量改动前先 git commit;
   原始层永不改写;结构简洁不深嵌套、不造重复文档
八、规模触发线:wiki 页 >200 才考虑引入 BM25+向量混合检索,之前索引+双链够用

【第 3 步:建基础文件】
- memory.md(根目录):偏好动态记录,格式 = 倒序「## YYYY-MM-DD + - **描述**:内容」
- 30.Resources/_index.md:检索总入口(分类导航表 + 「知识空白待补建」清单)
- 30.Resources/_log.md:演进日志,条目格式「## [YYYY-MM-DD] 操作类型 | 一句话」
- Templates/ 六个模板:tpl-daily(今日记录/待办/复盘)、tpl-idea、tpl-project、
  tpl-reading、tpl-codex-wiki(核心要点/关键数据/相关笔记/来源)、
  tpl-crystal(问题/证据/结论/边界条件/被拒方案)
- AGENTS.md:一页指针,让其它 AI agent 也遵守 CLAUDE.md

【第 4 步:建可执行命令 .claude/commands/(系统的灵魂)】
六个 Markdown 命令文件,每个把流程写成可执行步骤:
- daily.md:读 memory 载入偏好 → 建今日 daily 并带入昨日未完成待办 →
  扫 Inbox 报告待归类 → 一屏汇报
- ingest.md:git 检查点 → 盘点 Inbox → 逐份提炼成 wiki 页(frontmatter 齐全、
  重要结论带来源、不脑补)→ 建 2-5 条双链 → 更新索引 → _log 留痕 → 处置原件
  (剪藏可删,PDF 移入原始资料目录并记哈希)
- ask.md:执行检索协议五步(接受 $ARGUMENTS 为问题)
- crystallize.md:提取本次会话的问题/证据/结论/边界/被拒方案 → 按 tpl-crystal
  建页就近存放 → 建链 → _log 留痕 → 新偏好写 memory
- lint.md:git 检查点 → 全库断链/孤页/frontmatter/陈旧(>90 天)/矛盾检查 →
  断链和索引直接修,事实性改写需确认 → 输出体检报告 + _log 留痕
- review.md:读近 7 天 daily 找反复出现的想法提议晋升 → 巡检项目(停滞提醒/
  完成归档)→ 清知识空白清单 → 给 ≤3 条「本周知识→下周行动」建议

【第 5 步:git 接管历史】
git init;写 .gitignore(排除 *.pdf、.obsidian/workspace*.json、系统杂项);
git add -A && git commit -m "chore: 知识库初始化"。

【第 6 步:验收演示】
执行一遍 /daily 流程给我看;然后让我丢一篇文章进 00.Inbox/,
执行 /ingest 演示完整入库;最后汇报系统全貌和日常使用方式:
每天开工 /daily → 随手丢 Inbox → 攒几篇跑 /ingest → 有问题用 /ask →
想清楚一件事就 /crystallize → 每周 /review → 每月 /lint。

原则提醒:整个过程结构直观简洁,宁缺勿滥;所有规则写一处不重复;
你是馆员不是过度工程师 —— 先让最小系统跑起来,复杂度等真实需求出现再加。
```

## 这套系统的日常节奏(建成后)

| 频率 | 动作 |
|------|------|
| 每天开工 | `/daily` 开馆 |
| 随时 | 想到/看到什么 → 丢 `00.Inbox/`,零格式要求 |
| 攒了几篇 | `/ingest` 编译入库 |
| 有问题 | `/ask 你的问题`(先查库再生成) |
| 想清楚一件事 | `/crystallize` 结晶保存 |
| 每周 | `/review` 复盘晋升 |
| 每月 | `/lint` 体检 |
