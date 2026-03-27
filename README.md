# Cowork — Alpha Coders Plugins

A Claude Code plugin marketplace by [Alpha Coders](https://alphacoders.io). Plugins in this catalog are designed for engineering managers and developers, for use with Cowork and Claude Code.

## Install this marketplace

```shell
/plugin marketplace add Alpha-Coders/claude-ac
```

## Available plugins

| Plugin | Version | Description |
|--------|---------|-------------|
| [gitcustos](./plugins/gitcustos/README.md) | 0.1.0 | AI-powered PR situational awareness for tech managers |

## Install a plugin

```shell
/plugin install gitcustos@claude-ac
```

## Plugin catalog

| Plugin | Commands | Category |
|--------|----------|----------|
| `gitcustos` | `/briefing`, `/pulse`, `/setup`, `/ask`, `/remember`, `/metrics`, `/contributor` | productivity |

---

## Repository structure

```
.claude-plugin/
  marketplace.json     # marketplace catalog
plugins/
  gitcustos/           # GitCustos plugin source
    .claude-plugin/
      plugin.json
    commands/
    skills/
    README.md
```

## Adding a new plugin

1. Create a directory under `plugins/<plugin-name>/`
2. Add `.claude-plugin/plugin.json` with name, version, description
3. Add your commands, skills, agents, hooks as needed
4. Add an entry to `.claude-plugin/marketplace.json`

See the [Claude Code plugin documentation](https://code.claude.com/docs/en/plugin-marketplaces) for the full schema reference.
