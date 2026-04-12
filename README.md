# claude-code-skills

Practical, ready-to-use skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) that solve real daily workflow problems.

**#skill使おう** -- One skill per day, building a toolkit that actually ships.

## Skills

| # | Skill | Description |
|---|-------|-------------|
| 1 | [quick-meeting-prep](skills/quick-meeting-prep.md) | Generate a 1-page meeting prep sheet with agenda, talking points, and questions from calendar events or manual input |
| 2 | [commit-changelog](skills/commit-changelog.md) | Generate a clean, user-facing changelog from git commits grouped by type with natural language descriptions |
| 3 | [url-to-insight](skills/url-to-insight.md) | Extract key insights, actionable takeaways, and relevance analysis from any URL using WebFetch |
| 4 | [smart-file-rename](skills/smart-file-rename.md) | Scan messy files in a directory, analyze their content, and rename them intelligently with approval before executing |
| 5 | [api-health-check](skills/api-health-check.md) | Check health of API endpoints with response time, status codes, and body validation in a dashboard-style report |
| 6 | [weekly-git-digest](skills/weekly-git-digest.md) | Summarize a week of git activity across repos with commits, PRs, files changed, and most active areas for standup or weekly review |
| 7 | [expense-log](skills/expense-log.md) | Log expenses in natural language, parse into structured data, and append to a CSV or JSON ledger file |

## Installation

### Option 1: Copy individual skills

```bash
# Copy a single skill
cp skills/commit-changelog.md ~/.claude/skills/

# Copy all skills
cp skills/*.md ~/.claude/skills/
```

### Option 2: Clone and symlink

```bash
git clone https://github.com/taiki200504/claude-code-skills.git
ln -s "$(pwd)/claude-code-skills/skills" ~/.claude/skills
```

### Option 3: Use directly

You can also reference skills directly in your project's `.claude/` directory:

```bash
mkdir -p your-project/.claude/skills
cp skills/api-health-check.md your-project/.claude/skills/
```

## How Skills Work

Skills are markdown files that give Claude Code structured instructions for completing specific tasks. When installed in `~/.claude/skills/` or `.claude/skills/`, they are automatically available in your Claude Code sessions.

Each skill includes:
- **When to Use** -- trigger conditions so Claude Code activates the skill at the right time
- **How It Works** -- step-by-step instructions Claude Code follows
- **Error Handling** -- defensive patterns for edge cases
- **Example** -- concrete input/output demonstration

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed
- No additional setup required -- skills work out of the box
- Some skills optionally use MCP servers (Google Calendar, Gmail) for enhanced functionality

## Contributing

Contributions welcome. Each skill should:

1. Solve a real, recurring workflow problem
2. Work immediately with no setup beyond Claude Code
3. Follow the frontmatter format (`name`, `description`)
4. Include error handling instructions
5. Stay under 150 lines
6. Include at least one concrete example

### Submitting a skill

1. Fork this repo
2. Add your skill to `skills/`
3. Update this README's skill table
4. Open a PR with a description of what problem your skill solves

## License

MIT

## Credits

By **Taiki Mishima** ([@MISHIMATAIKI22](https://x.com/MISHIMATAIKI22)) / **[Gugen Inc.](https://gugenlab.com)**

---

**#skill使おう** -- Follow [@MISHIMATAIKI22](https://x.com/MISHIMATAIKI22) for daily skill drops.
