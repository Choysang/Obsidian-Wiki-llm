# Obsidian-Wiki-LLM

> 把 Obsidian + Claude Code 变成一套**由 AI 持续编译维护的个人知识操作系统**——人只负责捕捉和提问,AI 负责摘要、归档、建链、保鲜。

灵感来自两篇 gist:

- [Andrej Karpathy — LLM Wiki v1](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f):别把 LLM 当查询器,把它当馆员;原始资料是「源代码」,LLM 持续编译出结构化 Wiki。
- [rohitg00 — LLM Wiki v2](https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2):补上生命周期治理 —— 认知状态、显式替代、冲突复核。

本项目是这两套思想在 **Obsidian + Claude Code** 上的实际落地方案,经过真实使用两个月以上迭代打磨,核心升级是把流程从"写在文档里靠 AI 记得做"变成"六个可执行斜杠命令"。

## 核心理念

普通笔记库的问题:知识**被存了,但从未被用**。文章剪进来再也不看,问 AI 的答案消失在聊天记录里。

解法:三层架构 + 四个动作循环。

```
L0 原始层(不可变)   PDF / 剪藏 / OCR 文本,带 SHA-256,AI 永不改写
L1 编译层(会生长)   Codex Wiki 页 + 项目/领域笔记 + Daily —— AI 编译维护
L2 系统层(管运行)   CLAUDE.md 宪法 + memory.md 补丁 + 六命令 + git

捕捉(人) → 入库 /ingest → 调用 /ask /daily → 进化 /crystallize /lint /review → 回到捕捉
```

回答问题前必须**先查库再生成**:查索引 → 读原文 → 综合输出并标注来源 → 有价值的新合成存回库 → 库里没有就明说「知识空白」,不脑补。

## 这个仓库里有什么

| 文件/目录 | 作用 |
|-----------|------|
| [`BOOTSTRAP.md`](BOOTSTRAP.md) | **从零构建的提示词** —— 复制粘贴到任意空 Obsidian vault + Claude Code,回答几个问题,约 10 分钟建成整套系统 |
| [`CLAUDE.md`](CLAUDE.md) | 系统宪法模板(唯一规则文档,含检索协议/写入规范/红线) |
| [`GUIDE.md`](GUIDE.md) | 方法论教学:第一性原理、架构详解、六级成长路线、避坑指南 |
| [`.claude/commands/`](.claude/commands) | 六个可执行斜杠命令:`/daily` `/ingest` `/ask` `/crystallize` `/lint` `/review` |
| [`Templates/`](Templates) | 六个笔记模板(daily / idea / project / reading / codex-wiki / crystal) |

## 快速开始

1. 新建一个空文件夹,用 Obsidian 打开作为 vault,在其中启动 Claude Code(或任何能读写文件的 AI agent)。
2. 打开 [`BOOTSTRAP.md`](BOOTSTRAP.md),把里面的提示词整段复制发给 AI。
3. 回答 AI 关于你自己的几个问题(称呼、身份、当前重心、建库初衷)。
4. 系统建成后,日常只需要:每天 `/daily` 开馆、随手把东西丢进 `00.Inbox/`、攒几篇跑 `/ingest`、有问题 `/ask`、想清楚一件事 `/crystallize`、每周 `/review`、每月 `/lint`。

更完整的原理讲解见 [`GUIDE.md`](GUIDE.md)。

## 与传统 RAG 的区别

| 维度 | 普通 RAG | 本方法(LLM Wiki) |
|------|---------|------------------|
| 知识存储 | 向量数据库 | Obsidian Markdown 文件 |
| 检索方式 | 语义相似度召回 | 索引 → 双链导航 |
| 知识复用 | 每次重新拼接上下文 | 编译一次,持续复用 |
| 可读性 | 对人不友好(向量) | 人和 AI 都可直接读 |
| 规模门槛 | 需要嵌入 API + 向量库 | 只需 Claude Code,小库无需向量化 |

本方法不排斥 RAG —— wiki 页超过 ~200 篇、索引定位明显失手时,再引入本地混合检索(BM25 + 向量)作为补充。

## License

[MIT](LICENSE)
