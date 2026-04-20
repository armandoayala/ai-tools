---
name: solid-review
description: >
  Analyzes source code files or folders for compliance with SOLID principles (Single Responsibility,
  Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion) and generates a
  detailed Markdown report. The report flags which portions of code violate each principle, explains
  why they violate it, proposes concrete solutions, and computes an overall SOLID score from 0 to 5.
  Use this skill whenever the user wants to: review code quality against SOLID principles, audit a
  file or folder for design issues, get a SOLID compliance score, analyze a code snippet for OOP
  design problems, or check if their architecture follows best practices. Trigger even if the user
  only says "review my code", "audit this file", "check SOLID", "analyze my classes", or pastes
  code and asks for design feedback.
---

# SOLID Review Skill

Performs a deep SOLID-principles analysis on source code and produces a structured Markdown report
with a score from 0 to 5.

---

## 1. Understand the input

The user can provide input in four ways:

| Mode | Description |
|---|---|
| **File path** | A single source file, e.g. `src/services/UserService.py` |
| **Folder path** | A directory; analyze every source file found recursively |
| **Inline code** | Code pasted directly in the conversation |
| **Git diff (auto)** | No explicit target given and the working directory is a git repo; analyze only modified or new files |

**If the input is a path**, use bash to read the file(s):
```bash
# Single file
cat /path/to/file.py

# Folder — find all source files and read each one
find /path/to/folder -type f \( -name "*.py" -o -name "*.ts" -o -name "*.js" \
  -o -name "*.java" -o -name "*.cs" -o -name "*.go" -o -name "*.rb" \
  -o -name "*.php" -o -name "*.kt" -o -name "*.swift" \) | sort
```

**If no target is provided and the directory is a git repo**, resolve the file list from git:
```bash
# Modified or new (untracked) files with source extensions
git diff --name-only HEAD
git ls-files --others --exclude-standard
```
Filter the combined output to supported source extensions, then read each file. Prefix the report with a note: *"Analysis scope: git-modified and new files only."*

Read the SKILL reference for per-principle heuristics before starting analysis:
→ `references/principles.md`

---

## 2. Analyze each principle

For every file (or the inline snippet), run through the five SOLID principles in order.
Use the detection heuristics in `references/principles.md`.

For each principle, determine:
- **Status**: ✅ Complies / ⚠️ Partially complies / ❌ Does not comply
- **Evidence**: The specific class, method, or lines that fail (quote the code)
- **Why it fails**: Plain-language explanation tied to the principle definition
- **Recommended fix**: Concrete refactor suggestion (pseudocode or renamed structure is fine)

---

## 3. Compute the SOLID score

After analyzing all five principles, calculate a score from **0.0 to 5.0**.

Each principle is worth **1.0 point**:
- ✅ Complies → **1.0**
- ⚠️ Partially complies → **0.5**
- ❌ Does not comply → **0.0**

Sum all five sub-scores. Round to one decimal place.

**Score interpretation:**
| Score | Label |
|---|---|
| 4.5 – 5.0 | Excellent |
| 3.5 – 4.0 | Good |
| 2.5 – 3.0 | Acceptable |
| 1.5 – 2.0 | Needs work |
| 0.0 – 1.0 | Critical |

---

## 4. Generate the Markdown report

Follow the exact template in `references/report-template.md`.

Key rules for the report:
- Write in the **same language the user used** to ask the question.
- Quote the **exact lines** that violate a principle (short excerpts, not full files).
- For folder inputs, add a **per-file summary table** at the top before the detailed sections.
- Keep each recommendation actionable — not just "apply SRP" but *how* to restructure.
- End with the score box and overall commentary.

---

## 5. Deliver

Output the report **directly in the chat** as a Markdown code block so the user can copy it,
then offer to save it as a `.md` file if they want.

---

## Edge cases

- **Mixed languages in folder**: analyze each file independently; state the language per file.
- **Very large file (>500 lines)**: focus on class/function definitions, not line-by-line.
- **Non-OOP code** (procedural scripts, SQL, config files): note that SOLID is OOP-centric,
  apply what applies (SRP and DIP often still relevant), skip what doesn't, and say so.
- **No classes found**: score each applicable principle 0.5 (neutral), explain the limitation.
- **Test files**: analyze them separately and note they follow different conventions.