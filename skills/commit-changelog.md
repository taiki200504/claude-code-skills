---
name: commit-changelog
description: Generate a clean, user-facing changelog from git commits grouped by type with natural language descriptions
---

# Commit Changelog

Transform raw git commit history into a polished, user-facing changelog. Groups changes by type, rewrites technical commit messages into natural language, and outputs publication-ready markdown.

## When to Use

- Preparing weekly team updates or sprint reviews
- Writing release notes for a new version
- Summarizing what changed for stakeholders who don't read git logs
- Creating changelog entries for a CHANGELOG.md file

## How It Works

### Step 1: Collect Commits

Run the appropriate git command based on user input:

```bash
# Last N commits
git log --oneline --no-merges -n {N}

# Since a date
git log --oneline --no-merges --since="{date}"

# Between tags/refs
git log --oneline --no-merges {from}..{to}

# Last week (default if no input)
git log --oneline --no-merges --since="7 days ago"
```

Also collect stats for context:
```bash
git diff --stat {from}..{to}
```

### Step 2: Classify Each Commit

Map each commit to a user-facing category:

| Commit prefix | Category |
|--------------|----------|
| `feat:` | New Features |
| `fix:` | Bug Fixes |
| `perf:` | Performance Improvements |
| `refactor:` | Improvements |
| `docs:` | Documentation |
| `test:` | Testing |
| `chore:`, `ci:` | Internal Changes |

If commits don't follow conventional commits, classify by analyzing the diff content:
```bash
git show --stat {hash}
```

### Step 3: Rewrite for Humans

Transform each commit message:
- Remove prefix tags (`feat:`, `fix:`, etc.)
- Rewrite in natural language from the user's perspective
- Combine related commits into single entries
- Remove internal implementation details

**Before:** `fix: handle null pointer in auth middleware when token expires`
**After:** `Fixed an issue where users were unexpectedly logged out when their session expired`

### Step 4: Generate Changelog

Output in this format:

```markdown
# Changelog — [Date Range or Version]

## New Features
- [Human-readable description of feature]
- [Human-readable description of feature]

## Bug Fixes
- [Human-readable description of fix]

## Improvements
- [Human-readable description of improvement]

## Internal Changes
- [Brief note about infra/tooling changes]

---
*[X] commits across [Y] files | [+A / -B] lines changed*
```

### Step 5: Offer Output Options

After generating, ask the user:
1. **Copy** — ready to paste into Slack, Notion, or email
2. **Append to CHANGELOG.md** — prepend to existing changelog file
3. **Japanese translation** — output a Japanese version for JP stakeholders

### Error Handling

- If the repo has no conventional commit prefixes, use file-path heuristics (e.g., changes in `src/components/` = UI changes)
- If the date range has zero commits, report clearly and suggest a wider range
- Skip merge commits and automated bot commits (dependabot, renovate)
- If a single commit touches many files, check if it's a bulk operation and summarize accordingly

## Example

**User input:**
```
Generate a changelog for the last 2 weeks
```

**Output:**
```markdown
# Changelog — Mar 28 to Apr 11, 2026

## New Features
- Added real-time biometric dashboard for EGG pod sessions
- Introduced team analytics view for corporate administrators
- Users can now export session history as CSV

## Bug Fixes
- Fixed intermittent connection drops during long meditation sessions
- Resolved an issue where notification sounds played during active sessions
- Fixed timezone display for users in JST

## Improvements
- Session loading time reduced by 40% through query optimization
- Updated onboarding flow with clearer step-by-step guidance
- Improved accessibility: all interactive elements now keyboard-navigable

## Internal Changes
- Migrated CI pipeline to GitHub Actions
- Updated dependencies (React 19.1, Convex 1.33)

---
*47 commits across 83 files | +2,340 / -890 lines changed*
```
