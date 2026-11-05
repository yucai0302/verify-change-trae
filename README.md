# verify-change

**Trae 适配版** | 中文 | [English](#english)

用 Feature Map（特征地图）验证一次代码或产品改动是否真的做对了，并产出可复核的证据报告。

> 本仓库是 verify-change 方法的 **Trae 适配版本**：不要求为每个项目自建一套 CLI，直接用 Trae / IDE 内置的终端、浏览器预览、截图、文件读写能力驱动真实应用。

## 项目简介

把「验证」从每次对话里的口头交代，变成项目里一项可复用的能力：Agent 自己找到该验哪些功能、自己驱动真实应用、自己拿出证据、自己迭代到通过。人从每一步的签核者，变成报告的审阅者。

三条不可让渡的原则：

1. **没有证据就没有结论。** 不写「看起来正常」「应该没问题」。每条结论必须挂到一条可复核的观察上。
2. **只加载需要的上下文。** 一次验证只读与本次改动相关的 1~N 个 feature 文件，不读整份地图、不通读源码。Feature Map 是 codebase 的压缩索引，作用是省 token。
3. **记忆随代码走。** Feature Map 进 git，和改动在同一个 PR 里维护；发现漂移就顺手更新，不让它变成过期 wiki。

## 三种模式

| 模式 | 触发场景 | 输入 | 产出 |
|---|---|---|---|
| A · 建图 | 首次使用 / 新增功能域 | 项目根目录 | `.verify/features/` 骨架，本模式不做验证 |
| B · 验证（默认） | 「验证一下这个改动」「复现这个 bug」 | 改动描述 / diff / 变更文件 / PR / issue | 一份验证报告 + 证据文件 + 地图回写 |
| C · 回归 | 「跑一遍回归」 | 无（按 `features/README.md` 顺序） | 一份汇总报告，标注通过 / 跳过 / 被挡住 |

模式 B 走五步：**定位 → 健康检查 → 驱动 → 取证 → 出报告**，发现问题就修，再从健康检查重跑，直到通过或明确判定做不到。

## 输入输出

**输入**

- 必需（模式 B / C）：改动描述，或以下任一等价物——diff、变更文件列表、PR、issue、bug 报告、一句自然语言。
- 可选但显著提高质量：期望行为与验收口径、复现步骤、相关日志、已知会踩的坑、要重点覆盖的路径、运行环境。
- 隐式输入：项目里的 Feature Map、跑着本次改动的一个应用或服务、IDE 内置能力。

**输出**

- 对话里先给一句结论。
- 报告写到 `<项目根>/.verify/reports/YYYY-MM-DD-<slug>.md`：结论 / 改动范围与命中的 feature / 验证矩阵 / 证据清单 / 未覆盖与被挡住 / 地图更新 / 下一步。
- 截图与日志片段落到 `<项目根>/.verify/evidence/`。
- 更新的 Feature Map 条目（新增入口、新发现的坑、漂移修正）。

## Trae 适配点

| 能力 | 原版做法 | 本版本 |
|---|---|---|
| 驱动应用 | 项目自建 `control-atlas.mjs` 之类的 CLI 封装 | 直接用 Trae / IDE 内置：终端、浏览器或预览面板、截图、文件读写 |
| 驱动契约写在 | 命令名 | feature 文件第三段「用哪个内置能力 + 什么可观察状态算到位」 |
| 依赖 | 需要为每个项目维护一套控制脚本 | 零额外依赖，装进 skills 目录即可用 |

硬规则不变：驱动真实用户路径，不用直接调内部函数、改数据库、mock 渲染层来充当「功能通过」的证据。

## 快速开始

1. 把本仓库放进 Trae 全局 skills 目录：

```bash
# Windows
xcopy /E /I verify-change "%USERPROFILE%\.trae-cn\skills\verify-change"

# macOS / Linux
cp -r verify-change ~/.trae-cn/skills/verify-change
```

2. 在 Trae 里对项目说一句话触发，例如：

```
用 verify-change 验证一下「记住我」这个改动
```

3. 首次使用没有 `.verify/` 时，它会先走模式 A 建图（先建 5~10 个高频功能域），再验证本次改动。

## 目录结构

```
verify-change/
├── SKILL.md                  # 技能主文件：完整方法论、硬规则、能力映射
├── references/
│   ├── feature-map.md        # 四段式模板、拆分粒度、索引与 sweep 顺序、维护约定
│   ├── proof-bar.md          # 什么算合格证据、五类路径、不可达如何报告
│   └── report-template.md    # 报告格式与结论判定口径
└── examples/
    ├── input.md              # 一组输入示例
    └── output.md             # 对应产出示例
```

Feature Map、证据与报告落在**被验证的项目里**，不在本仓库：

```
<项目根>/.verify/
├── features/                 # 进 git，团队共享
├── evidence/                 # 截图、日志片段
└── reports/                  # 验证报告
```

## License

MIT

---

<a id="english"></a>

# verify-change

**Trae-adapted edition** | [中文](#verify-change) | English

Verify whether a code or product change actually works, using a project-local Feature Map, and produce an evidence report that someone else can re-check.

> This repository is the **Trae-adapted edition** of the verify-change method: instead of building a per-project control CLI, it drives the real application through the capabilities Trae / the IDE already ships — terminal, browser preview, screenshots, file access.

## Overview

It turns verification from something re-explained in every chat into a reusable project capability: the agent finds which features are affected, drives the real application, collects evidence, and iterates until it passes. The human moves from signing off every step to reviewing a report.

Three non-negotiable principles:

1. **No evidence, no conclusion.** No "looks fine" or "should be ok". Every conclusion hangs on an observation that can be re-checked.
2. **Load only the context you need.** One verification reads the 1~N feature files related to the change — not the whole map, not the whole codebase. The Feature Map is a compressed index of the codebase; its job is saving tokens.
3. **Memory travels with the code.** The Feature Map lives in git and is maintained in the same PR as the change; drift gets fixed on the spot instead of rotting into a stale wiki.

## Three modes

| Mode | When | Input | Output |
|---|---|---|---|
| A · Build the map | First use / a new feature area | Project root | A `.verify/features/` skeleton; no verification in this mode |
| B · Verify (default) | "verify this change", "reproduce this bug" | Change description / diff / changed files / PR / issue | A verification report + evidence files + map updates |
| C · Regression sweep | "run the regression" | None (follows `features/README.md` order) | One summary report marked pass / skipped / blocked |

Mode B runs five steps: **locate → health check → drive → collect evidence → report**. If something fails, fix it and restart from the health check, until it passes or is explicitly declared impossible.

## Inputs and outputs

**Inputs**

- Required (modes B / C): a change description, or any equivalent — a diff, a list of changed files, a PR, an issue, a bug report, or one plain sentence.
- Optional, but improves quality a lot: expected behavior and acceptance criteria, reproduction steps, relevant logs, known traps, paths you care about, runtime environment.
- Implicit inputs: the project's Feature Map, a running instance of the app with this change, the IDE's built-in capabilities.

**Outputs**

- One-sentence verdict in the conversation first.
- A report at `<project root>/.verify/reports/YYYY-MM-DD-<slug>.md`: verdict / change scope and matched features / verification matrix / evidence list / uncovered and blocked / map updates / next steps.
- Screenshots and log excerpts under `<project root>/.verify/evidence/`.
- Updated Feature Map entries (new entry points, newly found traps, drift fixes).

## Trae adaptations

| Concern | Original approach | This edition |
|---|---|---|
| Driving the app | A project-specific CLI wrapper such as `control-atlas.mjs` | Trae / IDE built-ins: terminal, browser or preview panel, screenshots, file access |
| Where the driving contract lives | Command names | Section 3 of each feature file: "which built-in capability + which observable state counts" |
| Dependencies | One control script to maintain per project | None; drop it into the skills directory and go |

The hard rule is unchanged: drive real user paths. Calling internal functions, editing the database, or mocking the rendering layer does not count as proof that a feature works.

## Quick start

1. Put this repository into the Trae global skills directory:

```bash
# Windows
xcopy /E /I verify-change "%USERPROFILE%\.trae-cn\skills\verify-change"

# macOS / Linux
cp -r verify-change ~/.trae-cn/skills/verify-change
```

2. Trigger it from Trae with one sentence in your project:

```
Use verify-change to verify the "remember me" change
```

3. On first use, with no `.verify/` yet, it runs mode A first (5–10 high-traffic feature areas), then verifies the change.

## Project structure

```
verify-change/
├── SKILL.md                  # The skill: methodology, hard rules, capability mapping
├── references/
│   ├── feature-map.md        # Four-section template, granularity, index and sweep order
│   ├── proof-bar.md          # What counts as evidence, five paths, how to report blocked paths
│   └── report-template.md    # Report format and verdict criteria
└── examples/
    ├── input.md              # An example input
    └── output.md             # The matching output
```

The Feature Map, evidence and reports live in **the project being verified**, not here:

```
<project root>/.verify/
├── features/                 # Committed to git, shared by the team
├── evidence/                 # Screenshots, log excerpts
└── reports/                  # Verification reports
```

## License

MIT
