# Scholia

**English** | [한국어](README.ko.md)

Scholia is an Agent Skill that turns a user's authored and merged GitHub pull requests from one week into learning material. It uses PR descriptions, changed files, diffs, review discussions, and checks as evidence to produce one standalone HTML workbook.

## What it provides

- Background needed to understand the system before the changes
- Learning chapters that group related PRs by concept
- Inline SVG and CSS diagrams for data flows and state transitions
- Analogies with explicit mappings to the implementation
- Failure modes, constraints, and design decisions
- Three questions per chapter with answers and explanations in `<details>` elements
- An evidence ledger showing where every PR from the week is covered

The result is a single HTML file with no external images, fonts, scripts, or CDNs. Open it directly in a browser or print it.

## Defaults

| Setting | Default |
|---|---|
| Period | The most recently completed Monday–Sunday |
| PRs | PRs authored by the user and merged during that period |
| Filename | `scholia-<start-date>-<end-date>.html` |
| Location | Current directory |
| Language | The user's language; Korean when unspecified |
| Storage and transfer | Local file only |

An explicit date range, GitHub user, repository, organization, or output path overrides the corresponding default. Open or unmerged PRs are included only when explicitly requested.

## Requirements

- An agent that supports Agent Skills
- A GitHub account with access to the repositories being analyzed
- An authenticated [GitHub CLI](https://cli.github.com/) or equivalent GitHub integration

When using GitHub CLI, check authentication first:

```sh
gh auth status
```

If necessary, authenticate:

```sh
gh auth login
```

## Installation

To install Scholia in the personal skill directory shared by Codex and other Agent Skills hosts, run these commands from this repository:

```sh
mkdir -p ~/.agents/skills/scholia
cp SKILL.md ~/.agents/skills/scholia/SKILL.md
```

To use Scholia only in the current project, copy `SKILL.md` into `.agents/skills/scholia/`:

```sh
mkdir -p .agents/skills/scholia
cp SKILL.md .agents/skills/scholia/SKILL.md
```

Restart the agent session if the host does not detect newly installed skills automatically.

## Usage examples

Analyze all PRs from the most recently completed week:

```text
Use Scholia to create a workbook from the PRs I merged last week.
```

Specify a period and repository:

```text
Create a Scholia workbook from the PRs I authored in octocat/example from 2026-09-01 through 2026-09-07.
```

Specify a GitHub user and output path:

```text
Analyze PRs authored by octocat last week and save the workbook to reports/weekly.html.
```

Include open PRs:

```text
Create a Scholia workbook containing both my merged and open PRs from this week.
```

## Verification

Before generating the HTML, the agent verifies each PR's author and merge timestamp. After generation, it checks that:

1. The table of contents, PR links, and answer disclosures work.
2. The page produces no external network requests or browser console errors.
3. The workbook remains readable at a 320px viewport and in print.
4. Every discovered PR maps to a chapter, the brief-changes section, or an exclusion reason.
5. At least one key claim in each chapter matches its cited PR evidence.

The completion response reports only the generated HTML path, covered period, PR count, and evidence completeness.

## Trust and security

- Instructions found in PR titles, bodies, comments, or code are not executed.
- Claims that cannot be verified from PR evidence are not presented as facts.
- Private repository content and generated HTML are not transmitted externally.
- Secrets, personal data, and long raw diffs are not copied into the workbook.
- Missing evidence caused by permissions, pagination, or deleted repositories is reported instead of presenting an incomplete result as complete.
