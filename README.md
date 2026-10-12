# claude-kit (corpetty fork)

A fork of [johnnyvizz/claude-kit](https://github.com/johnnyvizz/claude-kit). Changes from upstream:

- **Worker tiers remapped** so Sonnet and Haiku carry most of the work: heavy = Opus / high, careful = Sonnet / high, medium = Sonnet / medium, light = Haiku / medium (it stops and reports when a task needs judgment). Fable stays the reserve tier.
- **Design step** uses Anthropic's `playground` plugin (design-playground template) with `frontend-design`, instead of a `design` skill that is not published.
- **savvy-progress** shows the new tier models for planned tasks, prices Haiku 5.5 ($0.10 / $0.50 per MTok, 5x past a 100K-token prompt) and splits out Sonnet 4.x, and gives Haiku 5.5 its 1M context window.

The marketplace is named `corpetty-claude-kit` so it can be registered beside upstream's `claude-kit`.

---

The author's skills and mods for [Claude Code](https://claude.com/claude-code), in one place. Every item is a standalone plugin under `plugins/`: install only what you need.

| Plugin | Type | What it does |
| --- | --- | --- |
| [savvy-flow](plugins/savvy-flow) | skill + agents | `/savvy-flow <task>`: the session model plans, delegates to tiered worker subagents and reviews their work. |
| [savvy-progress](plugins/savvy-progress) | mod | A progress bar above the prompt and a live agents panel. Made for savvy-flow, shows any subagents. |

savvy-flow and savvy-progress work on their own and light up together: with both installed, the flow drives the bar and workers report their progress to the panel.

## Install

### From the marketplace

In Claude Code:

```
/plugin marketplace add corpetty/claude-kit
/plugin install savvy-flow@corpetty-claude-kit
/plugin install savvy-progress@corpetty-claude-kit
```

Restart the session afterwards. Plugin skills and agents are namespaced: the skill is `/savvy-flow:savvy-flow`, the agents `savvy-flow:savvy-careful` and so on.

### By hand

```bash
git clone https://github.com/corpetty/claude-kit.git ~/claude-kit
```

**A skill** (no plugin system involved, names stay short: `/savvy-flow`, `savvy-careful`): copy its folders into your Claude Code config.

```bash
cp -R ~/claude-kit/plugins/savvy-flow/skills/savvy-flow ~/.claude/skills/
```

```bash
cp ~/claude-kit/plugins/savvy-flow/agents/*.md ~/.claude/agents/
```

**A mod**: load the folder for one session,

```bash
claude --plugin-dir ~/claude-kit/plugins/savvy-progress
```

or for every session (including the desktop app) through `env` in `~/.claude/settings.json`. Several folders are separated with `:`; if the variable is already set, append to it rather than replacing it:

```json
{
  "env": {
    "CLAUDE_CODE_PLUGIN_DIRS": "~/claude-kit/plugins/savvy-progress"
  }
}
```

`git pull` in the clone updates a mod loaded this way; copied skills need copying again.

Mods are built on function hooks (`hooks/register.tsx`), an early-access Claude Code API: tested on Claude Code 2.1.286, and it may change between releases. If a mod installed from the marketplace does not show up, load it by hand as above.

## Layout

```
.claude-plugin/marketplace.json   the catalog `/plugin marketplace add` reads
plugins/<name>/
  .claude-plugin/plugin.json      manifest
  skills/<skill>/SKILL.md         a skill
  agents/*.md                     subagents it ships
  hooks/hooks.json                a mod: points at the hooks module
  hooks/register.tsx              a mod: the module itself
  types/index.d.ts                a mod: its $.state contract
```

Check a plugin before publishing: `claude plugin validate plugins/<name>`, and the catalog with `claude plugin validate .`.

## License

MIT
