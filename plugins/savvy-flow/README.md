# savvy-flow

An orchestrated delivery flow for Claude Code. Run `/savvy-flow <task>` (`/savvy-flow:savvy-flow` when installed as a plugin): the session model plans, owns design, delegates implementation to tiered worker subagents and adversarially reviews what they return.

## What ships

- **Skill** `savvy-flow`: the orchestrator's playbook.
- **Agents** by tier, cheapest that gets the task right first time:

  | Agent | Model / effort | For |
  | --- | --- | --- |
  | `savvy-fable` | Fable / high | the hardest logic and bugs; design features on request |
  | `savvy-heavy` | Opus / high | unfamiliar code, unclear root cause |
  | `savvy-careful` | Sonnet / high | delicate but well-understood changes |
  | `savvy-medium` | Sonnet / medium | standard feature work |
  | `savvy-light` | Haiku / medium | mechanical edits, builds, tests; stops and reports when a task needs judgment |

  `savvy-fable` runs on the Fable model; without access to it, change `model:` in `agents/savvy-fable.md` (say, to `opus`).

## Design work

For new screens and flows the orchestrator builds a design playground with Anthropic's `playground` plugin (its `design-playground` template), styled by the `frontend-design` plugin. Both live in the official marketplace:

```
/plugin install playground@claude-plugins-official
/plugin install frontend-design@claude-plugins-official
```

Without `playground` it falls back to a plain HTML mockup.

## With savvy-progress

The skill and the agents run fine alone. With the [savvy-progress](../savvy-progress) mod installed, the orchestrator reports the plan and accepted tasks to a progress bar above the prompt, and each worker reports its own steps to the agents panel. Without the mod those calls are skipped.

Install instructions are in the [repository README](../../README.md).
