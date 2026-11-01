# Handoff Document

子代理的全部实质产出都写进一份 `.md` 交接文档。planner 和后续代理只读这份文档、它引用的产物和工作区；回报只是「文档已就绪」的通知。

## 位置与生命周期

- 路径由 planner 在契约中指定，位于运行目录下：`<工单 id>.executor.md`、`batch-<n>.review.md`、`research-<主题>.md`。
- 结束前写好。因任何原因提前停下（阻塞、中断、预算耗尽）也先写，状态如实标注。
- 续跑时原地更新同一文件，让它反映当前状态，而不是追加流水账。接替的新代理写自己的文件，并在头部引用前任文件。

## 写法

- 写给**接手者**：一个没有你上下文的代理，只凭这份文档、所引用的产物和工作区就能继续。
- 只写工作区和工单看不出来的东西：决策及理由、偏离、试过但走不通的路、未决问题。工单、spec、diff、commit 用路径引用，不复述，不贴 diff。
- 证据写实际结果：命令、通过数/失败数、失败项名称。
- 密钥、口令、个人信息不写入。

## 骨架

所有角色共用头部，正文取本角色的段落；没有内容的段落写 `none`。

```
# <工单 id | batch-<n> | 调研主题> — <executor | reviewer | researcher>

- Status: done | partial | blocked
- Ticket(s): <工单路径>
- Predecessor: <前任交接文档路径 | none>
```

### Executor

```
## Changes
按新增 / 修改 / 删除列出文件；共享文件注明改动的位置。

## Decisions
关键设计决策及理由；偏离工单之处及原因。

## Verification
契约中每条命令的实际结果。看起来与本单元无关的失败，附命令与关键输出。

## Open
未完成项、阻塞、需要 planner 决定的问题。

## Next
接替者从哪里继续、第一步做什么。Status 为 done 时写 none。

## Review focus
风险最高、最值得 reviewer 细看的位置。
```

### Reviewer

```
## Fixes by ticket
每张工单一个小节：文件、问题、处理方式。planner 据此把修复归入对应 commit。

## Cross-unit fixes
跨工单的修复，注明涉及哪些工单。

## Doubts
涉及产品行为、因此没有修改的问题，各附位置与理由。

## Verification
每张工单验证命令的实际结果。
```

### Researcher

```
## Findings
按调研区域分组：现状、缺陷、可复用实现（路径 + 一句用途）、风险。每条结论附文件路径作为证据。

## Unknowns
没查清的问题及原因。
```
