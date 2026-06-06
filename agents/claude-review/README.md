# claude-review

> AI-powered PR Review Sub-Agent — structured Markdown reviews for any GitHub pull request.

`claude-review` fetches a GitHub PR diff, analyzes it for risks and improvements, and outputs a structured Markdown review. Works standalone or as a GitHub Action.

Bounty submission for [issue #4](https://github.com/claude-builders-bounty/claude-builders-bounty/issues/4).

## Quick start

```bash
# CLI (matches acceptance criteria)
chmod +x agents/claude-review/claude-review
agents/claude-review/claude-review --pr https://github.com/owner/repo/pull/123

# Direct Python invocation
python agents/claude-review/claude_review.py --pr https://github.com/owner/repo/pull/123

# Save output to a file
agents/claude-review/claude-review --pr https://github.com/owner/repo/pull/123 -o review.md
```

Set `GITHUB_TOKEN` (or pass `--token`) with `public_repo` scope for public repositories.

## Output format

Every review includes:

- **Summary of Changes** — 2–3 sentences plus file breakdown
- **Identified Risks** — checklist of potential issues
- **Improvement Suggestions** — actionable recommendations
- **Confidence Score** — `Low`, `Medium`, or `High`

## GitHub Action

Copy the workflow into your repository:

```bash
mkdir -p .github/workflows
cp agents/claude-review/.github/workflows/claude-review.yml .github/workflows/claude-review.yml
```

The included workflow uses `pull_request` (not `pull_request_target`) and checks out the trusted base branch.

## Tested on real PRs

| PR | Result |
|----|--------|
| [`kcolbchain/brand#8`](https://github.com/kcolbchain/brand/pull/8) | ✅ [+255/-1, 15 files] |
| [`claude-builders-bounty/claude-builders-bounty#2505`](https://github.com/claude-builders-bounty/claude-builders-bounty/pull/2505) | ✅ [+217/-0, 4 files] |
| [`claude-builders-bounty/claude-builders-bounty#2504`](https://github.com/claude-builders-bounty/claude-builders-bounty/pull/2504) | ✅ [+152/-0, 4 files] |

Sample outputs are in [`samples/`](samples/).

## Tests

```bash
python3 -m unittest agents/claude-review/tests/test_claude_review.py
```

## How reviews work

1. Fetch PR metadata via GitHub REST API (title, description, files, commits)
2. Fetch the full PR diff
3. Flag risks: large diffs, TODO/FIXME markers, debug statements, commented-out code, hardcoded secrets, large single-file additions
4. Generate suggestions for commit granularity, cross-language changes, and PR descriptions
5. Compute a confidence score from PR size and file count
