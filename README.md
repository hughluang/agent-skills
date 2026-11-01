# agent-skills

两个 [Agent Skills](https://agentskills.io/specification)。

| Skill | 结果 |
| --- | --- |
| `owned-outcome` | 已定稿的意图变成主会话亲自验收、并留下记录的集成结果。主会话规划、把关、记录；子代理调研、实现、审查。 |
| `review-substance` | 变更里每个留下的单元都持有真实决策或变换；空心实现直接在代码里收拢。 |

`owned-outcome` 的审查步骤使用 `review-substance`，两个一起装。

## 安装

```bash
npx skills add hughluang/agent-skills --skill owned-outcome --skill review-substance
```

```bash
gh skill install hughluang/agent-skills owned-outcome
gh skill install hughluang/agent-skills review-substance
```

`owned-outcome` 需要能派子代理、向用户提问、使用 git 的宿主，并且只在被点名时启用。

## 许可证

MIT
