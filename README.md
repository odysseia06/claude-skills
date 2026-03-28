# claude-skills

My personal collection of Claude Code skills and plugins. A place to keep skills I write or collect from the community.

## Usage

To use as a plugin during development:

```bash
claude --plugin-dir /path/to/claude-skills
```

Or install from the plugin directory in any project:

```bash
claude plugins add /path/to/claude-skills
```

## Structure

```
claude-skills/
├── .claude-plugin/
│   └── plugin.json        # Plugin manifest
├── skills/                 # Skill definitions
│   └── <skill-name>/
│       └── SKILL.md
└── agents/                 # Custom agents (optional)
```

## License

MIT
