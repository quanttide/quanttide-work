---
name: context-to-profile
description: 量潮「语境到档案」工作流——把语境仓 `data/context` 里的材料（当前是各〈主体／域〉的当日流水）逐条认归属、粗加工，写进档案仓 `data/profile` 的对应位置，再把已迁出的从语境删掉，并沿「档案仓 → 语境仓 → quanttide-work 指针 → 伞仓指针」逐层提交推送。当用户说「把语境收进档案」「整理流水」「归位材料」「这份该放哪」时使用。
---

# 语境到档案

语境仓只收原始——当前就是各〈主体／域〉的当日流水；整理时把流水逐条认归属，粗加工成「**标签**：内容」的一节，写进档案仓的对应位置。材料只在档案仓保留，迁出后语境里那条即删，流水文件留空、目录保留。

步骤与验收与画廊里的同名工作流一致（`docs/gallery/workflows/context-to-profile.md`，人看的正本；同名 `.yaml` 是机器形态）。

## 仓库拓扑

```
<伞仓>/                                   quanttide/quanttide，项目根的上上级
└── domains/quanttide-work                项目根（当前工作仓）
    ├── data/context/<主体>/<域>/          语境仓：原始，当前只收当日流水
    │   └── journal/<日期>.md
    ├── data/profile/<主体>/<域>/<记忆类型>/  档案仓：材料落点
    └── 其余子模块
```

各仓路径用 `git rev-parse --show-toplevel` 逐层解析，不写死绝对路径。

## 落点

材料落到 `data/profile/<主体>/<域>/<记忆类型>/`：`<主体>` 与 `<域>` 照抄流水那两段（如 `quanttide/quanttide-code`、`quanttide-tech/default`）；`<记忆类型>` 按这条讲什么定，用各仓统一的那套名字——archive、brochure、context、history、insight、intention、journal、library、profile、report、roadmap、bylaw、essay、gallery、handbook、specification、tutorial。

类别目录不在就新建，里面放 `index.md`；拿不准的单列出来交创始人裁决。

## 执行步骤

五步链，程序能查的都实查：

1. **pull 拉语境**：`git -C data/context pull --ff-only`，把新到的日期文件逐条读一遍，清单写进报告的「条目清单」一节。
2. **classify 认归属**：逐条定域与记忆类型，写进报告同一节；拿不准的单列。
3. **coarsen 粗加工**：每条口语流水收成「**标签**：内容」的一节，写进 `data/profile/<主体>/<域>/<记忆类型>/index.md`；一条一意、平实陈述，不写「不是…而是…」式表达。
4. **move-out 迁出语境**：已进档案的条目从语境日期文件删掉，文件留空、目录保留；没处理的留着。顺手删掉指向源材料的相对链接，不做跨仓补丁。
5. **commit 分层提交**：档案仓提交推送，语境仓提交推送，再回项目根更新 `data/profile` 与 `data/context` 两个指针，最后更新伞仓指针。

先写后删：材料在档案仓写就推送后，才从语境删，迁移期间至少在一处。

## 提交信息风格

- 档案仓：`docs: 迁入 2026-09-29 流水（归位 quanttide-code）`
- 语境仓：`refactor: 流水迁出档案（留空待续）`
- quanttide-work：`chore: 更新 data/profile、data/context 指针（语境到档案）`
- 伞仓：`chore: 同步 quanttide-work 指针（语境到档案）`

## 硬性约束

- 显式 pathspec：永远 `git add -- <path>`，禁用 `git add -A`，免得卷入工作区里用户自己的未提交改动；动手前先 `git status -s` 摸底。
- `terminal` 的 `cd` 只能是项目根或子目录：操作项目外仓库用 `cd=<项目根>` 加 `git -C <该仓路径>`；`read_file` 拒绝项目外路径，改用 `cat`。
- 不传 `timeout_ms`：会导致工具请求 JSON 解析失败。
- 一条命令只做一步、控制在 1–2 个短调用：并行发长命令会 `tool input was not fully received`。
- 核对再提交：每次 commit 前看 `git diff --cached --stat`，确认只有预期文件。

## 完成后

汇报各层提交哈希（档案仓／语境仓／quanttide-work／伞仓），并指出这批材料现在的路径与语境里剩下的条目。若伞仓还有其他落后指针（`git -C <伞仓> status -s`），只提及、不擅自同步。
