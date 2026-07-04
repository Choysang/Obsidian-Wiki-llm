# Obsidian-Wiki-LLM

**🌐 English | [中文](README.md)**

> This isn't note-taking software — it's a knowledge operating system where the AI is the librarian. You only drop things in and ask questions; the AI summarizes, files, links, and keeps things fresh. The theory comes from Karpathy's LLM Wiki idea: plain RAG re-derives an answer on every query and then forgets it. This system compiles the result of every derivation once and keeps reusing it — knowledge snowballs the more you use it.

Inspired by two gists:

- [Andrej Karpathy — LLM Wiki v1](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f): don't treat the LLM as a query engine, treat it as a librarian; raw sources are "source code," the LLM continuously compiles them into a structured wiki.
- [rohitg00 — LLM Wiki v2](https://gist.github.com/rohitg00/2067ab416f7bbe447c1977edaaa681e2): adds lifecycle governance — epistemic status, explicit supersession, conflict review.

This project is a working implementation of both ideas on **Obsidian + Claude Code**, refined over two-plus months of real daily use. The key upgrade over a plain "write the rules in a doc" approach: the workflow is now six executable slash commands, so it doesn't degrade across sessions or models.

## Architecture: Three Layers + Four Loops

**What gets stored (three layers)**

| Layer | Holds | Rule |
|-------|-------|------|
| Raw | PDFs, web clippings, scans | Immutable — the final arbiter of fact |
| Compiled | AI-written summary pages, project notes, daily logs | Grows continuously — this is what actually gets retrieved |
| System | One rules file + six executable commands + git history | Governs how the whole thing runs |

**How it cycles (four loops)**: capture → ingest → invoke → evolve → back to capture.

```
Capture (human) → Ingest /ingest → Invoke /ask /daily → Evolve /crystallize /lint /review → back to capture
```

## 1. How Knowledge Gets Ingested

The human side is minimal: see something worth keeping, drop it straight into an "inbox" folder — no formatting required.

What the AI does once an ingest command fires:

1. Reads the source, figures out which knowledge domain it belongs to
2. Distills it into a structured page — one or two sentences of core insight, key data/formulas listed separately, important claims tagged with their source (never invents content the source doesn't have)
3. Auto-links it to 2-5 existing related notes, building bidirectional links
4. Updates the master index and appends an entry to the changelog
5. If the original is non-reproducible (e.g. a scanned PDF), it's moved into a dedicated raw-materials folder with its hash recorded; ordinary clippings can be cleared afterward since git already kept the history

Core principle: **ingesting isn't copy-paste — it's distilling, restructuring, and connecting.** It turns "knowing about" into "structured understanding."

## 2. How Knowledge Stays Organized and Fresh

- **Daily**: on the first session of the day, the AI creates today's log, carries over yesterday's unfinished to-dos, and sweeps the inbox for backlog
- **Weekly review**: the AI scans the week's daily logs for recurring ideas and proposes promoting them into formal knowledge pages; checks in-progress projects for stalls; clears the "knowledge gap" backlog — topics you've hit but the wiki doesn't cover yet
- **Monthly lint**: a full sweep — links pointing to notes that don't exist, orphaned pages nobody links to, important content that hasn't been touched in too long, two notes contradicting each other. Contradictions are **never auto-resolved** — they're flagged for human review
- **Session crystallization**: after any deep discussion, debugging session, or decision, the question, the evidence, the final conclusion, the conditions under which it holds, and the rejected alternatives get saved as one page — so you never lose ground you already covered

## 3. How Retrieval and Synthesis Work When You Need Them

This is the single most important rule in the whole system, and it runs before answering anything:

```
① Check the index first — don't answer from nothing; locate candidate notes via the master index and sub-indexes
② Open and read the source — actually open the matching notes and read them, don't guess from memory
③ Synthesize the answer — build the response from what was actually read, tagging each claim with the note it came from
④ Write back anything valuable — if the answer produces an insight not yet in the wiki, save it as a new page; the wiki compounds
⑤ Be honest about gaps — if the wiki genuinely has nothing relevant, say so explicitly ("knowledge gap") instead of passing off the model's general knowledge as your own recorded knowledge
```

The payoff is a **traceable answer** — not something the model made up, but something that points back to a specific note. When the wiki really doesn't have the answer, the AI says plainly that this part is "supplementary," not something from your knowledge base.

## 4. How to Build Your Own

You don't need to design this from scratch — three steps get it running:

1. **Lay out the skeleton**: a handful of functional zones (inbox / active projects / long-term areas / the wiki itself / archive / daily logs), plus one rules file describing how the system runs
2. **Turn the workflow into commands, not just prose** — this is the easiest step to skip and the most important one. Rules written only in a doc get "forgotten" by the AI after a few sessions; turned into invocable commands, the workflow executes reliably regardless of session or model changes
3. **Let git own the version history** — commit a checkpoint before any big change, so the AI can maintain boldly and you can delegate boldly, knowing anything can be rolled back

A few pitfalls worth mentioning up front:

- **Don't over-engineer from day one** — keep folders shallow, don't maintain two rule documents that can drift out of sync; get it running first and add complexity only when a real need shows up
- **Don't reach for a vector database too early** — at a few dozen to a few hundred notes, plain index + bidirectional links is faster and more legible; only add hybrid retrieval once the wiki genuinely scales past that
- **Every important claim needs a traceable source** — this is the one thing standing between you and an AI that confidently makes things up
- **Minimize the human's burden** — the human only does two things: drop things in, and ask questions. Formatting, categorizing, and linking are all the AI's job. The moment the human's side gets complicated, the system gets abandoned

## What's in This Repo

| File / Folder | Purpose |
|----------------|---------|
| [`BOOTSTRAP.md`](BOOTSTRAP.md) | **The from-scratch build prompt** — paste it into any empty Obsidian vault + Claude Code, answer a few questions, and the whole system is built in about 10 minutes |
| [`CLAUDE.md`](CLAUDE.md) | The system constitution template (the single source of truth: retrieval protocol, write rules, hard limits) |
| [`GUIDE.md`](GUIDE.md) | Methodology walkthrough: first principles, architecture deep-dive, a six-level maturity path, pitfalls |
| [`.claude/commands/`](.claude/commands) | Six executable slash commands: `/daily` `/ingest` `/ask` `/crystallize` `/lint` `/review` |
| [`Templates/`](Templates) | Six note templates (daily / idea / project / reading / codex-wiki / crystal) |

## Quick Start

1. Create an empty folder, open it in Obsidian as a vault, and launch Claude Code (or any file-capable AI agent) inside it.
2. Open [`BOOTSTRAP.md`](BOOTSTRAP.md) and paste its prompt to the AI, whole and unedited.
3. Answer the AI's questions about yourself (what to call you, your role, current focus, why you're building this).
4. Once built, daily use is just: `/daily` each morning, drop things into `00.Inbox/` as they come up, run `/ingest` once you've accumulated a few, `/ask` when you have a question, `/crystallize` after you've worked something out, `/review` weekly, `/lint` monthly.

Full reasoning behind the design is in [`GUIDE.md`](GUIDE.md).

## vs. Traditional RAG

| Dimension | Plain RAG | This method (LLM Wiki) |
|-----------|-----------|-------------------------|
| Storage | Vector database | Obsidian Markdown files |
| Retrieval | Semantic similarity recall | Index → bidirectional-link navigation |
| Reuse | Re-assembles context every time | Compiled once, reused continuously |
| Readability | Not human-friendly (vectors) | Directly readable by both humans and AI |
| Scale threshold | Needs an embedding API + vector store | Just Claude Code — no vectorization needed at small scale |

This method doesn't reject RAG — once the wiki passes roughly 200 pages, or index lookups start visibly missing, add local hybrid retrieval (BM25 + vectors) as a supplement.

## License

[MIT](LICENSE)
