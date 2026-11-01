---
name: owned-outcome
description: "Turn settled intent into integrated work the parent session accepts on evidence it produced itself and then records. The parent plans, gates, and records; subagents research, implement, and review. Use when the user has a finished spec, plan, or tickets and asks to implement it, orchestrate delivery, delegate to subagents, verify, and commit."
license: MIT
compatibility: "Needs a host with subagents, a question tool, and git. Invoke explicitly. Review uses review-substance from the same repository."
metadata:
  author: hughluang
  version: "1.0.0"
disable-model-invocation: true
---

# Owned Outcome

把一份已定稿的 spec（或一组工单）推进到：全部单元实现、逐批通过 gate、逐单元提交、集成验证通过。主代理做 **planner**，子代理做具体工作。成功标准：每个单元的行为与 spec 一致，有独立验证证据，有对应 commit；最终全量验证通过或剩余失败已证明与本次无关。

## 角色

- **Planner（主代理）**：编制确认、调研编排、工单、排程、委派、gate、对账、commit、向用户汇报。只亲手做**不改变产品行为**的修改（重命名、import、工单状态、文档对齐）。判定测试：这次修改是否改变产品行为——是，就委派。
- **Executor（子代理）**：按委派契约实现一个单元，测试先行。已安装 `tdd` 则遵循它；未安装则按 [contract.md](contract.md) 的最低做法。
- **Reviewer（子代理）**：上下文干净，对一个**批次**的全部 diff 执行 `review-substance` 技能，直接修复发现的问题。
- **Researcher（子代理）**：只读调研，把带路径证据的事实写进交接文档。

子代理一律不执行 git 写操作；commit 只由 planner 做。同时运行的子代理文件归属互不重叠。

## Handoff

代理之间通过 `.md` **交接文档**传递工作，格式见 [handoff.md](handoff.md)。

- 开工时在系统临时目录建一个**运行目录**（例如 `$TMPDIR/owned-outcome-<spec 名>/`），所有交接文档都放在这里。它不在工作区内，这样不会混进 diff 和 stage。
- 每次派发，planner 都在契约里指定交接文档的绝对路径。子代理把全部实质内容写进文档，回报只有一行：状态加路径。
- planner 读交接文档，不读回报正文。下游代理（reviewer、接替者）拿到的是上游交接文档的路径，而不是 planner 转述的内容。
- 交接文档是临时的。需要长期保留的内容（实现摘要、偏差）在 Checkpoint 时提炼进工单。

## 流程

### 1. Staffing

开工前、派出任何子代理之前，用提问工具向用户确认本次运行的**编制**：委派哪些角色，各用什么模型。

- researcher、executor、reviewer 每个角色一个问题，在一次提问调用里问完。工具支持多选时开启多选；没有提问工具时用一条消息列出选项请用户回复。
- 选项取自当前环境列出的可用子代理模型，外加「沿用主代理模型」。researcher 和 reviewer 另有「不委派」：前者由 planner 亲自调研并照样写 research 交接文档，后者从 gate 中略去 review。executor 没有这个选项，因为改变行为的修改只能委派。
- 某角色选了多个模型：该角色的并行委派在这些模型间轮换；reviewer 优先用与本批 executor 不同的模型，换一双眼睛。
- 编制包含 reviewer 时，确认 `review-substance` 已安装。未安装则让用户先安装同仓库的该技能，再派 reviewer。

每次派发都把选定模型作为派发参数传入，编制对整次运行有效。派发时某个选定模型不可用，停下来问用户，不自行替换。环境不支持为子代理指定模型时，告知用户后全部沿用默认。

完成标准：每个角色都有用户确认过的编制：不委派，或一份模型清单。委派 reviewer 时 `review-substance` 已安装。

### 2. Research

对 spec 涉及的各个面并行派出 researcher，每个写一份 research 交接文档。

完成标准：每个将要动的区域都在某份交接文档的 Findings 段里，有现状描述和可复用资产清单；Unknowns 段里的问题已追加调研或标为风险。

### 3. Tickets

- 无工单时：已安装 `to-tickets` 则遵循它。未安装时，planner 把 spec 拆成按依赖排序的工单。每张工单是一条可单独验证的纵向切片，窄到一次运行能做完，并写明阻塞它的工单。覆盖一整片调用点的机械改动，按「先并存新形式、再分批迁移、最后删除旧形式」排序。把拆分交给用户确认粒度与依赖后，写成放在运行目录之外的工单文件。
- 用户已提供工单时：跳过拆分，对照 spec 和代码检查——覆盖是否完整、依赖边是否正确、单元是否可在一次运行内完成；有缺口就补。

然后把 research 交接文档中的发现写进每张工单的现状段（路径、当前缺陷、可复用实现）。工单要比运行目录活得久，所以这里写入事实本身，不写交接文档路径。

完成标准：每张工单自足——executor 只读它和仓库规则就能开工。

### 4. Schedule

- **Frontier**：依赖已全部完成的工单。
- **Batch**：一轮 Schedule 同时派出的 executor 委派。它是 gate 和 review 的单位。
- 共享文件指定一个 owner，其他单元只做局部定点编辑。
- 每次委派必须能在一次运行内完成。新抽象加大范围迁移的单元，拆成「primitives + pilot」和「rollout」两次委派。

完成标准：本批的单元清单及各自的文件归属已确定。

### 5. Delegate

每次委派附一份 **delegation contract**，模板见 [contract.md](contract.md)。契约写明范围边界、文件归属、验证命令和交接文档路径；executor 测试先行实现。本批委派并行派出。

完成标准：本批每个 executor 的交接文档都已写好，Verification 段有实际结果。Status 为 partial 或 blocked 的单元，按 Open 段处理：续跑补完，或把需要决定的问题交给用户。

### 6. Gate

单元在通过 gate 前不算完成。本批 executor 的交接文档全部就绪后依次：

1. **Independent verification（逐单元）**：planner 亲自运行每个单元契约里的验证命令。交接文档只是线索，一切以工作区真实状态为准。把 Changes 段与实际 diff 对账，用来发现越界改动；对照 Decisions 段抽查核心模块的 diff。未通过的单元续跑原 executor 修正，退出本批 review，修好后并入下一批；没有下一批时单独成批。
2. **Semantic check（逐单元）**：planner 对照 spec 判断行为语义是否正确（例如危险操作是否需要确认、错误路径是否符合契约）。偏差交回 executor 修正：续跑原子代理，消息里写明问题或引用交接文档的对应段落。修正完成并重跑验证后再进入 review。
3. **Review（每批一次）**：派**一个**新的 reviewer 子代理，覆盖本批所有通过前两步的单元，按 [contract.md](contract.md) 的 reviewer 契约执行。输入是各单元的 executor 交接文档，产出是一份 review 交接文档。本批 diff 超出一次运行能完成的量时，按文件归属拆成几个并行 reviewer，每个仍覆盖多张工单。
4. **Doubts**：planner 裁决 review 交接文档 Doubts 段里的每个问题，需要修改的按第 2 步交回 executor。reviewer 或修正改过代码后，重跑受影响单元的验证命令。

完成标准：本批每个单元验证通过、语义与 spec 一致，Doubts 段每一条都已裁决。

### 7. Checkpoint

- 本批通过 gate 后，planner 逐单元提交 commit：按文件归属 stage 该单元的文件，reviewer 的修复按 review 交接文档的 Fixes by ticket 段归入对应 commit，Cross-unit fixes 归入它涉及的某一张工单的 commit，或合并 commit。共享文件里几个单元的改动无法干净拆分时，合并成一个 commit，message 列出全部工单。message 遵循从 `git log` 推断的仓库约定。
- 中断的单元补完并通过 gate 后再提交。
- 在工单末尾追加实现摘要与偏差说明，提炼自 executor 交接文档的 Decisions 段和 gate 的裁决结果，然后把状态标为 done。
- push 或开 PR 只在用户明确要求时做。

完成标准：本批每个单元都有 commit，工单状态与工作区一致。

回到第 4 步，直到 frontier 为空。

### 8. Integration

全部单元完成后：

- 运行全量验证（类型检查、单元测试、构建、端到端、仓库自带检查脚本）。
- 做跨单元检查：单元各自正确但组合后可能出错的部分——共享注册表与配置的一致性、多语言 key 对齐、只差大小写的文件名、密钥与产物是否被忽略、遗留的死代码。
- 本次引入的失败和跨单元问题写成工单，回到第 4 步。
- 向用户汇报：交付了什么、验证证据、与 spec 的偏差、已证明与本次无关的遗留失败、需要用户决定的事项（汇总各交接文档 Open 段中仍未关闭的条目），以及运行目录路径。

完成标准：全量验证通过，或每个剩余失败都有「与本次无关」的证明。

## Resilience

- **Worktree is truth**：交接文档、记忆、摘要都可能过时。任何判断前先看工作区（diff、文件、测试结果）。
- **Baseline proof**：失败是否由本次引入，在干净基线上复现后再下结论（例如 `git worktree add --detach <tmp> HEAD` 后运行同一命令）。环境问题（依赖缺失、服务不可达）如实标注，不当作代码缺陷修。
- **Resume, don't redo**：子代理中断后，先读它的交接文档并核对工作区，再续跑同一子代理，只补缺口。交接文档缺失时，在续跑消息里要求它先补写。续跑失败才另派新子代理（沿用该角色的选定模型），契约的 Predecessor 段填前任交接文档的路径。
- **Reconcile**：上下文丢失或跨会话时，用运行目录里的交接文档、工单状态、commit 历史、文件修改时间重建进度，先把工单改成与现实一致再继续。运行目录已被清理时，只用其余来源。编制无从找回时，重新向用户确认。
- **Contract drift**：子代理越界（改了禁止的文件、执行了 git 写操作、扩大了范围）时，在 gate 中纠正，并把该约束更醒目地写进后续契约。
- **默认方向**：拿不准某个修改是否改变行为时，按改变处理并委派；拿不准某个失败是否本次引入时，按引入处理，直到基线证明不是。

## 自检

- [ ] 每次派发都符合用户确认的编制（角色与模型）？
- [ ] 每次派发都在指定路径产出了交接文档，下游代理拿到的是交接文档路径而不是转述？
- [ ] 委派了 reviewer 时，`review-substance` 已安装，且每个单元都在某一批里被 review 过？
- [ ] 每个单元都有 planner 亲自运行的验证证据和一个 commit（或注明合并的 commit）？
- [ ] 每个改变行为的修改都出自子代理？
- [ ] 工单状态、commit 历史、工作区三者一致？
- [ ] 遗留失败都有基线证明？
