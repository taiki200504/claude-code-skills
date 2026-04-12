---
name: weekly-git-digest
description: Summarize a week of git activity across repos with commits, PRs, files changed, and most active areas for standup or weekly review
---

# Weekly Git Digest

Generate a comprehensive summary of git activity over the past week. Covers commits, pull requests, files changed, lines added/deleted, and identifies the most active areas of the codebase. Outputs a formatted report suitable for standup meetings, weekly reviews, or async team updates.

## When to Use

- Monday morning standup prep — summarize what happened last week
- Weekly team review or sprint retrospective
- Monthly report compilation
- When onboarding someone who needs to catch up on recent changes

## How It Works

### Step 1: Determine Scope

**Default:** Current repo, last 7 days.

Accept user overrides:
- Custom date range: `--since="2026-04-01" --until="2026-04-07"`
- Specific repo path: `/path/to/other/repo`
- Multiple repos: process each and combine into one report

### Step 2: Collect Git Data

Run these commands to gather raw data:

```bash
# Commit count and authors
git -C "{repo}" log --since="7 days ago" --oneline --no-merges | wc -l
git -C "{repo}" shortlog --since="7 days ago" -sn --no-merges

# Detailed commit list
git -C "{repo}" log --since="7 days ago" --format="%h %s (%an, %ar)" --no-merges

# Files changed with stats
git -C "{repo}" diff --stat "$(git -C "{repo}" log --since="7 days ago" --format="%H" --reverse | head -1)^"..HEAD 2>/dev/null

# Lines added/deleted
git -C "{repo}" log --since="7 days ago" --numstat --no-merges --format="" | awk 'NF==3 {add+=$1; del+=$2} END {print add, del}'

# Most changed files
git -C "{repo}" log --since="7 days ago" --name-only --no-merges --format="" | sort | uniq -c | sort -rn | head -15
```

If the repo is on GitHub, also check for PR activity:
```bash
gh pr list --state all --search "updated:>$(date -v-7d +%Y-%m-%d)" --json number,title,state,author,createdAt,mergedAt --limit 50
```

### Step 3: Analyze Patterns

From the raw data, identify:
1. **Most active areas** — group changed files by directory/module
2. **Top contributors** — who committed the most
3. **Hotspot files** — files changed most frequently (potential refactoring candidates)
4. **Commit pattern** — distribution across days of the week
5. **Large changes** — any commit touching 10+ files (potential risk)

### Step 4: Generate Report

Output these sections:
- **Summary table:** Total commits, contributors, files changed, lines +/-, PRs merged/opened
- **Top Contributors:** Author, commit count, lines changed
- **Most Active Areas:** Directory, file count, highlights
- **Key Changes:** Top 3-5 changes summarized in natural language
- **Pull Requests:** PR number, title, status, author
- **Hotspot Files:** Files changed 3+ times (refactoring candidates)
- **Daily Activity:** Commit distribution across Mon-Sun

After generating, offer export formats: Slack-ready (simplified), Notion, or email.

### Error Handling

- If the repo has no commits in the period, report clearly with the last commit date
- If `gh` CLI is not available, skip PR data and note it
- For repos with 500+ weekly commits, summarize by directory rather than listing all
- If multiple repos are requested, process each independently to isolate failures
- Handle repos with no remote (local-only) gracefully

## Example

**User input:**
```
Give me a git digest for this week
```

**Output:**
```markdown
# Weekly Git Digest
**Period:** Apr 5 to Apr 11, 2026
**Repository:** Gugen (main)

## Summary
| Metric | Count |
|--------|-------|
| Total commits | 12 |
| Contributors | 1 |
| Files changed | 28 |
| Lines added | +1,240 |
| Lines deleted | -380 |

## Key Changes This Week
1. **CLAUDE.md:** Consolidated workspace rules and memory sharing structure
2. **docs/:** Added business plan v7 and legal research documents
3. **corporate-materials/:** New pitch deck and financial projections

## Most Active Areas
| Area | Changes |
|------|---------|
| docs/ | 15 files |
| corporate-materials/ | 8 files |
| root config | 5 files |

No open pull requests this week.
```
