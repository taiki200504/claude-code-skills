---
name: smart-file-rename
description: Scan messy files in a directory, analyze their content, and rename them intelligently with approval before executing
---

# Smart File Rename

Scan a directory of disorganized files (screenshots, downloads, random documents) and generate intelligent, descriptive filenames based on content analysis. Shows a rename plan for approval before executing any changes.

## When to Use

- Your Downloads folder is full of `Screenshot 2026-04-11 at 14.32.05.png` files
- You have a batch of documents with meaningless names like `Document (3).pdf`
- After receiving files from collaborators with inconsistent naming
- Organizing project assets before committing to a repository

## How It Works

### Step 1: Scan the Directory

```bash
ls -la "{target_directory}"
```

Collect all files, ignoring:
- Hidden files (`.DS_Store`, `.gitignore`, etc.)
- Files already following a clean naming convention
- System files and directories

### Step 2: Analyze Each File

Based on file type, determine the content:

**Images (.png, .jpg, .webp, .svg):**
- Use the Read tool to visually inspect the image
- Describe the subject: screenshot of what app/page, photo of what, diagram of what
- Note key visual elements (UI components, text visible, people, products)

**Documents (.md, .txt, .docx):**
- Read the first 50 lines to identify the topic
- Extract the title, heading, or main subject
- Note the document type (report, notes, draft, spec)

**PDFs (.pdf):**
- Read the first 2 pages to identify the content
- Extract the document title and type

**Code files (.ts, .py, .js, etc.):**
- Read the file to identify its purpose
- Check for module exports, class names, or main function

**Other files:**
- Use file extension and size as classification hints
- If content is binary/unreadable, use the original name with cleaned formatting

### Step 3: Generate Rename Plan

Create a naming convention:
- Format: `{descriptive-name}.{ext}` using kebab-case
- For dated content: `{YYYY-MM-DD}-{descriptive-name}.{ext}`
- For versioned content: `{name}-v{N}.{ext}`
- Maximum filename length: 60 characters

Present the plan as a table:

```markdown
## Rename Plan for: ~/Downloads/

| # | Current Name | New Name | Reason |
|---|-------------|----------|--------|
| 1 | Screenshot 2026-04-11 at 14.32.05.png | 2026-04-11-flux-dashboard-overview.png | Screenshot of FLUX dashboard |
| 2 | Document (3).pdf | gugen-pitch-deck-v3.pdf | Pitch deck, version 3 |
| 3 | IMG_4521.jpg | egg-pod-prototype-photo.jpg | Photo of EGG hardware prototype |
| 4 | untitled.md | meeting-notes-softbank-demo.md | Meeting notes from SoftBank demo |
| 5 | a.py | csv-sales-data-parser.py | Python script parsing sales CSV |

### Summary
- **5** files to rename
- **0** files skipped (already well-named)
- **0** conflicts detected
```

### Step 4: Execute After Approval

**Wait for explicit user approval before renaming any files.**

Once approved, execute renames:
```bash
mv "{old_name}" "{new_name}"
```

After execution, verify:
```bash
ls -la "{target_directory}"
```

Report results:
```
Renamed 5/5 files successfully. No errors.
```

### Step 5: Handle Conflicts

If a target filename already exists:
1. Append a numeric suffix: `name-2.ext`
2. Flag the conflict in the plan for user review
3. Never overwrite existing files without explicit permission

### Error Handling

- If a file cannot be read (permissions, corruption), skip it and note in the plan
- If the directory is empty, report immediately instead of generating an empty plan
- If renaming fails (permissions, path too long), report the specific error and continue with remaining files
- For very large directories (>100 files), process in batches of 20 and confirm each batch
- Never delete files — this skill only renames

## Example

**User input:**
```
Clean up the filenames in ~/Downloads/project-stuff/
```

**Output (Step 3):**
```markdown
## Rename Plan for: ~/Downloads/project-stuff/

| # | Current Name | New Name | Reason |
|---|-------------|----------|--------|
| 1 | スクリーンショット 2026-04-10 9.15.22.png | 2026-04-10-egg-os-login-screen.png | EGG OS login page screenshot |
| 2 | final_FINAL_v2.pptx | egg-investor-pitch-v2.pptx | Investor pitch deck |
| 3 | notes.txt | 2026-04-09-brainstorm-pricing-model.txt | Pricing model brainstorm notes |
| 4 | test.py | api-endpoint-load-test.py | Load testing script for API |

### Summary
- **4** files to rename
- **0** conflicts detected

Proceed with renaming? (yes/no)
```
